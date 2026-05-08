.. _cloud_implementation:

Implementing TOPAS-nBio cloud simulations
-----------------------------------------

This chapter walks you through how to run TOPAS-nBio simulations in the cloud using Amazon Web Services (AWS).
Please read this guide carefully, particularly if you are new to AWS. Before we begin, here is a brief overview and some important terms:

* An **image** refers to a packaged snapshot of software and its dependencies (i.e. TOPAS-nBio) that can in principle be run on any machine regardless of the operating system. 
* **Docker** is the technology commonly used to build and run such images inside isolated environments called **containers**. 
* You typically **host** an image in a **registry** such as AWS Elastic Container Registry (ECR) or Docker Hub.
* When you want to use the software contained in that image to run a simulation, you need to **pull** that image from the container registry 
  to either your local machine, a cluster, or some virtual machine hosted by a cloud provider (i.e. AWS). 
* You can then use the **compute resources** from that cloud provider to run the simulation and you are billed for the compute time used.

There are two main sections in this chapter:

* The section :ref:`cloud_implementation_part1` provides step by step instructions for how to:

    1. Create an AWS account :ref:`[1] <aws_account_setup>`.
    2. Create a public ECR repository or Docker Hub repository to host your image :ref:`[2] <cloud_step2_repositories>`.
    3. Configure GitHub Actions in the repository hosting your source code to build and push custom images to ECR :ref:`[3] <cloud_step3_github_actions>`.

* The section :ref:`cloud_implementation_part2` provides step by step instructions for how to:

    4. Configure AWS and its resources (Batch, EC2 and S3) to be able to run TOPAS-nBio simulations :ref:`[4] <cloud_implementation_part2>`.
    5. Set up the input (hosts your TOPAS parameter files) and output (hosts your TOPAS-nBio simulation results) directories on AWS :ref:`[5] <cloud_step5_inputs_image>`.
    6. Choose the computing resources to allocate to your simulations and how many jobs you want to run in parallel :ref:`[6] <cloud_step6_compute_resources>`.
    7. Submit and monitor your jobs, and optionally postprocess your results :ref:`[7] <cloud_step7_submit_jobs>`.

**IMPORTANT: Please take note of the following points before starting any of the steps.** 

* Steps 2-3 are only required if you want to **host your own customised TOPAS-nBio images** (for example, with additional user extensions).
  If you are happy to use the official TOPAS-nBio image, you can skip these steps and go from step 1 directly to step 4.
* If you already have an AWS account, or have access to other computing resources and are only interested in using the TOPAS-nBio image,
  you can find the image and its public URI here_. Additional resources detailing how to use this image can be found in the `OpenTOPAS repository`_.

.. warning::

   By following the workflow outlined in this chapter, there are two main costs that you should consider: **data storage/transfer** and **computing resources**.
   TOPAS-nBio hosts official images in `AWS ECR Public`_, and `Docker Hub`_. If you are using AWS computing resources, as this guide outlines, then we recommend 
   that you use the AWS ECR image since pulls to AWS compute resources are free and unlimited, while pulls from Docker Hub may be throttled after certain limits.
   If you want to host your own image on AWS ECR, the free tier is limited to 50 Gb/month, after which you will be charged for the excess storage. The workflow 
   files described in step :ref:`3 <cloud_step3_github_actions>` have safeguards to avoid you being billed. These can be copied into your own repository and modified to suit your needs.
   Further details concerning the pricing of AWS ECR or Docker Hub can be found on the respective AWS_ and Docker_ websites. As always, when using cloud resources
   be mindful of the costs associated with the resources you are using. For AWS these costs are detailed on the EC2_ website. The TOPAS collaboration is not responsible 
   for any costs incurred when using the resources outlined in this guide.

.. note::
  
  An alternative cloud approach for Microsoft Azure has been implemented by Hector Miras del Rio and can be found in `this GitHub repository`_. It has recently 
  been updated to be compatible with the latest version of OpenTOPAS (v4.2.3) and TOPAS-nBio (v4.1.0). Users interested in Azure instead of AWS should refer to
  the documentation in that repository instead.

Overview
^^^^^^^^

Assuming that you will be using the official TOPAS-nBio image, the schematic below provides an overview of the TOPAS-nBio 
infrastructure put in place in order to run your own TOPAS-nBio simulations on AWS.

.. figure:: images/schematic_FINAL.drawio.pdf
   :align: center
   :width: 100%
   :alt: Typical workflow for running TOPAS-nBio simulations on AWS

Official builds of TOPAS-nBio are available as pullable images on AWS ECR and Docker Hub. Two types of images exist: one that contains the OpenTOPAS/TOPAS-nBio source code for running simulations, and
an optional post-processing image if you want to use the same AWS compute resources to post-process your results. 

AWS offers a very large selection of resources. This documentation deliberately walks you through a minimal implementation that is sufficient to 
to reliably run TOPAS-nBio simulations. The main resources used are AWS ECR from which you pull images, AWS S3 which holds your input and output files, 
AWS Batch which launches compute instances for your simulations, and AWS CloudWatch Logs to monitor your jobs and collect their outputs. 
The TOPAS-nBio `repository`_ includes an ``aws/`` directory with template JSON files and shell scripts that you can copy and modify to suit your needs. 
Later sections in this ReadTheDocs provide more detailed instructions on what exactly needs to be modified in these files.

* ``batch-compute-env.json`` tells Batch what kind of computers to use behind the scenes.
* ``batch-job-definition.json`` describes your main simulation job: which image to run,
  how much memory and CPU, and where logs go.
* ``batch-postP-job-definition.json`` only needed if you also run post-processing on AWS;
  it is a second job description for that image.
* ``topas_submit.sh`` copies your local simulation folder up to S3, then asks Batch to
  start your simulation with the right bucket names and settings.
* ``postProcess_submit.sh`` does the same kind of thing, but for post-processing jobs
  instead of simulation runs.

.. tip::

  If you are having trouble with any of the steps in this AWS guide, modern day AI tools such as Claude can be of great help. Download this ReadTheDocs as a pdf, 
  upload it, then describe which step you encountered an error on, along with any potential error messages. If you feel that a section of this guide is outdated/incorrect, 
  please reach out on the TOPAS-nBio `user forum`_

.. _here: https://gallery.ecr.aws/q4p2q6v1/topas-nbio-test
.. _OpenTOPAS repository: https://github.com/OpenTOPAS/OpenTOPAS/tree/2d6f0cb3884a91561b8e35b783b8ba5911e982e9/docker
.. _AWS ECR Public: https://gallery.ecr.aws/q4p2q6v1/topas-nbio-test
.. _Docker Hub: https://hub.docker.com/r/tmasilela/topas-nbio
.. _this GitHub repository: https://github.com/HectorMiras/topas_docker_azure_batch
.. _AWS: https://aws.amazon.com/ecr/pricing/
.. _Docker: https://www.docker.com/pricing/
.. _EC2: https://aws.amazon.com/ec2/pricing/
.. _user forum: https://github.com/topas-nbio/TOPAS-nBio/discussions
.. _repository: https://github.com/topas-nbio/TOPAS-nBio