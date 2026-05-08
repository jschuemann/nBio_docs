.. _cloud_implementation_part2:

Running TOPAS-nBio on AWS
-------------------------

This section explains how to configure AWS and its resources to run TOPAS-nBio simulations at scale on the cloud.

**What you will use and why**

AWS provides several resources that work together to run TOPAS-nBio simulations:

* **AWS Batch** – a resource that organises and runs your simulation jobs. You submit a job definition (see step ...) and compute environment (see step ...)
  which Batch uses to launch the necessary compute resources (EC2 instances) to run your simulations.
* **EC2** – the actual virtual machines that run your simulations. When you use Batch, you normally do not create or manage these EC2 instances yourself;
  Batch does that automatically.
* **S3** – object storage for input and output data (referred to as "buckets" in AWS). This is where your TOPAS-nBio input and output data will be stored.
  Scripts provided in this guide (see steps :ref:`6 <cloud_step6_compute_resources>` to :ref:`7 <cloud_step7_submit_jobs>`) will upload your local simulation directory to S3, the container will sync files down to a working
  directory, run TOPAS, and sync results back to S3.
* **ECR** – container registry for storing the TOPAS-nBio image. Pulls from this registry can be requested by the Batch resource to run your simulations
  using the version of TOPAS-nBio loaded in the image.

**Prerequisites**

Before following the detailed steps, ensure that:

* You have an AWS account with an IAM user configured as described in :ref:`step 1 <aws_account_setup>`.
* You have a TOPAS-nBio container image available in a registry (either the official TOPAS-nBio image provided in ECR_ or `Docker Hub`_, 
  or a custom image that you built and pushed using the workflows described in steps :ref:`2 <cloud_step2_repositories>` and :ref:`3 <cloud_step3_github_actions>`).

Before starting with steps :ref:`4 <cloud_step4_aws_setup>` and beyond, you need to download and `install the AWS CLI`_. You can verify that
that it is installed correctly by running the following command in your terminal:

.. code-block:: bash

   aws --version

If you see a version number, then the AWS CLI is installed correctly. To authenticate with AWS folllow the steps below.

* First login with your IAM user in the AWS Console.

.. code-block:: bash

   aws login --profile your-username

* Replace ``your-username`` with the the IAM user you created in step :ref:`1.5 <cloud_step1_5_create_iam_user>`. Enter the region if prompted.
  Your region can be found in the top right corner of the AWS Console, next to your account name.
  This will open a browser window where you will need to select the IAM user you created, following which you will get a message saying that the 
  sign in was successful. If you are not logged into AWS in the browser you will be prompted to first login.

.. figure:: images/part2/step2.pdf
  :alt: AWS login with IAM user
  :width: 50%
  :align: center

.. figure:: images/part2/step2b.pdf
  :alt: AWS login successful message
  :width: 50%
  :align: center

* Then verify that you are authenticated with the following command. This should return your account ID, user ARN, and user ID.

.. code-block:: bash

    aws sts get-caller-identity --profile your-username

* Finally, set your profile name and region (i.e. us-east-1) in the terminal using the following commands:

.. code-block:: bash

    export AWS_PROFILE=your-username
    export AWS_DEFAULT_REGION=us-east-1

* These export commands need to be run whenever you open a new terminal session, and allows you to use aws commands without the ``--profile`` flag. 
  Alternatively, you can add the export command to your ``~/.bashrc`` or ``~/.zshrc`` file so that it is run automatically whenever you open a new terminal session.

.. note::

   These authentications steps are based on the recommendations from AWS found here: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-authentication.html.
   Alternative authentication methods are discussed in the AWS documentation if you prefer to use a different method.

.. _cloud_step4_aws_setup:

4. One-time AWS setup for Batch
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The next steps are performed once per AWS account and region.

* **4.1 Set your CLI profile**.

After authenticating with the AWS CLI, ensure that your profile name is set for convenience. This can be checked with 
``echo $AWS_PROFILE`` which should return your profile name.

* **4.2 Create IAM roles for Batch and EC2**.

Batch requires an IAM **service role** so that it can create and manage compute resources on your behalf, and the EC2 instances 
require an IAM **instance role** so that they can pull container images and access S3.

1. Batch service role.

   * In the AWS Console, search for **IAM** then click **Roles → Create role**.

    .. figure:: images/part2/step3.2.pdf
      :alt: Create role Batch service role
      :align: center

   * Trusted entity type: **AWS service**.
   * Service or use case: **Batch**.
   * Use case: **Batch**.
   * Accept the default permission policy of ``AWSBatchServiceRole``.


    .. figure:: images/part2/step3.2b.pdf
      :alt: AWS Batch - Compute environment
      :align: center

    .. figure:: images/part2/step3.2c.pdf
      :alt: AWS Batch Service Role
      :align: center

   * Give the role a descriptive name, such as ``TOPAS-nBio-BatchServiceRole``, and create the role.
   * After creating the role you should land on the following page. Click the role name you assigned, then copy the **Role ARN** which 
     you will need later to create the compute environment.

    .. figure:: images/part2/step3.2d.pdf
      :alt: AWS Batch Service Role created
      :align: center
      
    .. figure:: images/part2/step3.2e.pdf
      :alt: AWS Batch Service Role details
      :align: center

2. EC2 instance role for Batch compute instances.

   * In the AWS Console, search for **IAM** then click **Roles → Create role**.

    .. figure:: images/part2/step3.3.pdf
      :alt: Create role EC2 instance role
      :align: center

   * Trusted entity type: **AWS service**.
   * Service or use case: **EC2**.
   * Use case: **EC2**.

    .. figure:: images/part2/step3.3b.pdf
      :alt: AWS EC2 - Instance role
      :align: center

   * Then in the search bar look for and attach the following policies.

      * ``AmazonEC2ContainerRegistryReadOnly`` (allows instances to pull images from ECR)
      * ``AmazonS3FullAccess`` (allows instances to read and write to and from any S3 bucket in your account).
      * ``AmazonEC2ContainerServiceforEC2Role`` (allows instances to talk to the AWS container service (ECS) so that AWS can start and monitor containers on those machines).

    .. figure:: images/part2/step3.3c.pdf
      :alt: AWS EC2 - Attach policies
      :align: center

   * Choose a name such as ``TOPAS-nBio-BatchEC2Role`` and create the role.
   * After creating the role click on the role name to open it in the console. Instead of copying the ARN like we did for the Batch service role, 
     copy the **Instance profile ARN** which you will need later to create the compute environment.

    .. figure:: images/part2/step3.3d.pdf
      :alt: AWS EC2 - Instance role created
      :align: center

    .. figure:: images/part2/step3.3e.pdf
      :alt: AWS EC2 - Instance role details
      :align: center

.. note::

   For convenience this guide recommends broad policies such as ``AmazonS3FullAccess`` for the EC2 instance role. However the best practice is 
   to restrict S3 permissions to the specific buckets that hold your TOPAS-nBio inputs and outputs.

.. _cloud_step5_inputs_image:

5. Create S3 buckets for inputs and outputs
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

TOPAS-nBio jobs running on AWS Batch will read inputs from S3 and write outputs back to S3. You can either create two buckets or use a 
single bucket with different prefixes; the guide uses two buckets for clarity.

Create two buckets with unique names using the following commands. The remaining steps in this guide will use the bucket 
names ``topas-nbio-input`` and ``topas-nbio-output`` so be mindful if you choose to use different names:

.. code-block:: bash

   aws s3 mb s3://topas-nbio-input
   aws s3 mb s3://topas-nbio-output

You can verify that the buckets have been created by searching for **S3** in the AWS Console and clicking on **Buckets**. 
You should see the two buckets you created.

.. figure:: images/part2/step4.pdf
   :alt: S3 buckets search results
   :align: center

.. figure:: images/part2/step4b.pdf
   :alt: S3 buckets created list
   :align: center

The submission script provided in this guide (see step :ref:`7 <cloud_step7_submit_jobs>`) will upload your local simulation directory containing your TOPAS parameter files 
to the input bucket before submitting jobs. Results are then written to the output bucket.

As also detailed in step :ref:`7 <cloud_step7_submit_jobs>`, you have the options to keep your input and output data on AWS S3 and use Batch to postprocess your results, or download 
your output data from S3 to your local machine and postprocess your results locally.

.. warning::

   Please take note of the pricing for storing your input and output data on AWS S3. This guide uses Standard S3 storage, which at the time of 
   writing is priced at $0.023 per GB per month for the first 50TB of data. Further details concerning the pricing of AWS S3 can be found 
   on the `AWS S3 website`_.

.. _cloud_step6_compute_resources:

6. Compute environment, job queue and job definitions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

AWS Batch resources are defined in three main parts: a **compute environment**, a **job queue** and one or more **job definitions**.

* **6.1 Compute environment**.

The compute environment defines what kind of compute resources Batch may use. In the example JSON configuration below, 
``minvCpus`` is the minimum number of virtual CPUs that Batch keeps running (for example, ``0`` means it can scale down to no instances when idle),
and ``maxvCpus`` is the upper limit on how many virtual CPUs Batch is allowed to use when scaling up to run your jobs.

Either work directly from the `aws directory`_ of the TOPAS-nBio repository source code, or create a local directory to work in and copy the files over.
The contents of each of the files are shown in this guide for convenience.

The ``batch-compute-env.json`` file should contain the following content:

.. code-block:: json

   {
     "computeEnvironmentName": "topas-nbio-compute-env",
     "type": "MANAGED",
     "state": "ENABLED",
     "computeResources": {
       "type": "EC2",
       "minvCpus": 0,
       "maxvCpus": 256,
       "desiredvCpus": 0,
       "instanceTypes": ["optimal"],
       "subnets": ["subnet-xxxxxxxx", "subnet-yyyyyyyy"],
       "securityGroupIds": ["sg-xxxxxxxx"],
       "instanceRole": "arn:aws:iam::ACCOUNT_ID:instance-profile/TOPAS-nBio-BatchEC2Role"
     },
     "serviceRole": "arn:aws:iam::ACCOUNT_ID:role/TOPAS-nBio-BatchServiceRole"
   }

.. note::

   Setting ``desiredvCpus`` to ``0`` means that Batch will keep no instances running
   when the queue is idle. When you submit a job, Batch scales up instances (within
   the ``minvCpus`` and ``maxvCpus`` limits) and scales down again once jobs finish.

If you are using the same IAM role names specified in this guide, simply replace ``ACCOUNT_ID`` with the account ID of your AWS account.
Alternatively, replace the ``serviceRole`` and ``instanceRole`` values with the ARN of the Batch service role and the EC2 instance role 
you created in step :ref:`4.2 <cloud_step4_aws_setup>`.

A VPC (Virtual Private Cloud) is a private network inside AWS attached to your AWS account. A **subnet** is a range of IP addresses inside 
your VPC. Batch needs at least one subnet so it has a place to start the EC2 instances that run your jobs. A **security group** controls 
what network traffic is allowed in and out of the instances. You can list multiple subnets and multiple security groups. This gives Batch 
more flexibility in where to run your jobs. In this guide we assign two subnets and one security group.

Replace the subnet and security group IDs by searching for **VPC** in the AWS Console and clicking on **Subnets** and **Security groups**.

.. figure:: images/part2/step6.1.pdf
   :alt: AWS VPC
   :align: center

.. figure:: images/part2/step6.1b.pdf
   :alt: AWS VPC subnets and security groups
   :align: center

This should take you to a page which lists the subnets and security groups available in your account. Copy the IDs and paste them 
into the ``batch-compute-env.json`` file.

.. list-table::
   :widths: 50 50
   :header-rows: 0
   :class: no-row-bg

   * - .. image:: images/part2/step6.1c.pdf
         :align: center
     - .. image:: images/part2/step6.1d.pdf
         :align: center

Once you have modified the ``batch-compute-env.json`` file, with the appropriate ARNs, subnets and security group IDs, create the 
compute environment with the following command:

.. code-block:: bash

   aws batch create-compute-environment --cli-input-json file://batch-compute-env.json

Once created, you should be able to see that it is valid in the AWS Console by searching for **Batch → Compute environment**.

.. figure:: images/part2/step6.1e.pdf
   :alt: AWS Batch compute environment created
   :align: center

.. figure:: images/part2/step6.1f.pdf
   :alt: AWS Batch compute environment valid
   :align: center

* **6.2 Job queue**.

The job queue links to the compute environment and is the queue to which you submit jobs. Create a job queue with the following command:

.. code-block:: bash

   aws batch create-job-queue \
     --job-queue-name topas-nbio-queue \
     --state ENABLED \
     --priority 1 \
     --compute-environment-order order=1,computeEnvironment=topas-nbio-compute-env

After creating the job queue, you should be able to see that it is enabled in the AWS Console by searching for **Batch → Job queue**.

.. figure:: images/part2/step6.2.pdf
   :alt: AWS Batch job queue created
   :align: center

.. figure:: images/part2/step6.2b.pdf
   :alt: AWS Batch job queue enabled
   :align: center

* **6.3 Job definitions**.

First create the cloudwatch log group with the following command. This is required for logging and monitoring your simulations and will be used in the job definitions.

.. code-block:: bash

   aws logs create-log-group --log-group-name /aws/batch/topas-nbio

Cloudwatch is an AWS resource that allows you to store and retrieve logs from your AWS resources. After creating the log group, you should be able 
to see that it is created in the AWS Console by searching for **Cloudwatch** then clicking on **Log Management**.

.. figure:: images/part2/step6.3.pdf
   :alt: AWS Cloudwatch
   :align: center

.. figure:: images/part2/step6.3b.pdf
   :alt: AWS Cloudwatch log group created
   :align: center

Job definitions tell Batch which container image to use, which resources to allocate and how to configure logging. 
The JSON files provided in the `aws directory`_ of the TOPAS-nBio repository and shown below refer to two separate job definitions: 
one for TOPAS-nBio simulations and one for post-processing. The latter is required if you want to use AWS Batch to also postprocess your results.

In the example job definition below for simulation jobs, 4 virtual CPUs and 8GB of memory are allocated. You can adjust these values to suit your 
needs as long as they are within the limits defined in the compute environment (``batch-compute-env.json``). Note that the 
log group name must be the same as the one created above (in this example, ``/aws/batch/topas-nbio``).

The ``batch-job-definition.json`` file should contain the following content:

.. code-block:: json

   {
     "jobDefinitionName": "topas-nbio-job",
     "type": "container",
     "platformCapabilities": ["EC2"],
     "containerProperties": {
       "image": "YOUR_IMAGE_URI",
       "resourceRequirements": [
         { "type": "VCPU", "value": "4" },
         { "type": "MEMORY", "value": "8192" }
       ],
       "environment": [
         { "name": "INPUT_BUCKET", "value": "topas-nbio-input" },
         { "name": "OUTPUT_BUCKET", "value": "topas-nbio-output" }
       ],
       "logConfiguration": {
         "logDriver": "awslogs",
         "options": {
           "awslogs-group": "/aws/batch/topas-nbio",
           "awslogs-region": "AWS_REGION"
         }
       }
     },
     "retryStrategy": { "attempts": 1 }
   }

In the example job definition below for post-processing jobs, 1 virtual CPU and 2GB of memory are allocated.

The ``batch-postP-job-definition.json`` file should contain the following content:

.. code-block:: json

   {
     "jobDefinitionName": "topas-nbio-postprocess-job",
     "type": "container",
     "platformCapabilities": ["EC2"],
     "containerProperties": {
       "image": "YOUR_IMAGE_URI",
       "resourceRequirements": [
         { "type": "VCPU", "value": "1" },
         { "type": "MEMORY", "value": "2048" }
       ],
       "environment": [
         { "name": "OUTPUT_BUCKET", "value": "topas-nbio-output" }
       ],
       "logConfiguration": {
         "logDriver": "awslogs",
         "options": {
           "awslogs-group": "/aws/batch/topas-nbio",
           "awslogs-region": "AWS_REGION"
         }
       }
     },
     "retryStrategy": { "attempts": 1 }
   }


Replace:

* ``YOUR_IMAGE_URI`` with your TOPAS-nBio image URI in ECR or Docker Hub. If you would like to use the official TOPAS-nBio images from ECR, 
  you can use the following URIs: ``public.ecr.aws/q4p2q6v1/topas-nbio-test`` for the latest TOPAS-nBio image and 
  ``public.ecr.aws/q4p2q6v1/topas-nbio-test:postprocess`` for the postprocessing image
* ``AWS_REGION`` with your AWS region.
* Bucket names if you chose to name them something different from ``topas-nbio-input`` and ``topas-nbio-output``.

.. note::
   Even if you don't think you will be interested in postprocessing your results using AWS Batch you can still keep and modify the ``batch-postP-job-definition.json`` as 
   detailed in these instructions. No resources are allocated to it if it sits idly. Alternatively, you can remove the file entirely.

Next, register the job definitions with the following commands for the simulation and postprocessing jobs respectively:

.. code-block:: bash

   aws batch register-job-definition --cli-input-json file://batch-job-definition.json
   aws batch register-job-definition --cli-input-json file://batch-postP-job-definition.json

After registering the simulation job definition, you should be able to see that it is registered in the AWS Console by searching for **Batch** 
then clicking on **Job definitions**. Any future revisions to the job definitions will appear here along with a new revision number.

.. figure:: images/part2/step6.3c.pdf
   :alt: AWS Batch job definitions registered
   :align: center

.. _cloud_step7_submit_jobs:

7. Submitting simulations with topas_submit.sh
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* **7.1 Submit simulation jobs using the script**.

This submission script can be found in the `aws directory`_ of the TOPAS-nBio repository source code. It uploads your local simulation files to S3 
and then submits one or more Batch jobs.

Edit the variables at the top of the script to match your run:

* ``PROJECT_NAME`` and ``RUN_DATE`` define the S3 output structure.
* ``NUM_JOBS`` sets how many jobs to submit.
* ``JOB_NAME`` is the base Batch job name.
* ``INPUT_BUCKET`` and ``OUTPUT_BUCKET`` are your S3 buckets.
* ``LOCAL_SIM_DIR`` points to your local simulation folder. This entire folder is uploaded, so ensure that it only contains files relevant to the simulation.
* ``FILE_TO_RUN`` is the main TOPAS parameter file.
* ``JOB_QUEUE`` and ``JOB_DEFINITION`` are the Batch queue and job definition names that were created in step :ref:`6 <cloud_step6_compute_resources>`.

The script currently used is the following:

.. code-block:: bash

  #!/bin/bash
  set -e

  ########################################################################################
  # Edit these variables for each run
  ########################################################################################

  PROJECT_NAME="fricke_project"
  RUN_DATE=$(date +%Y-%m-%d)
  SIM_DIR_BASE="projects/${PROJECT_NAME}/${RUN_DATE}"

  NUM_JOBS=2
  JOB_NAME="topas-run-fricke"
  INPUT_BUCKET="topas-nbio-input" # input and output bucket names should match batch-job-definition.json
  OUTPUT_BUCKET="topas-nbio-output"

  LOCAL_SIM_DIR="/Applications/TOPAS/public/TOPAS-nBio/examples/scorers/Fricke"
  FILE_TO_RUN="FrickeIRT.txt"       # Main TOPAS parameter file
  JOB_QUEUE="topas-nbio-queue"
  JOB_DEFINITION="topas-nbio-job"

  ########################################################################################
  #
  ########################################################################################

  # For each job, create a job-specific copy of the input directory, append a random seed
  # to the main parameter file, sync to a job-specific S3 prefix, and submit the job.
  for i in $(seq 1 "$NUM_JOBS"); do
    # Job-specific temporary directory
    JOB_DIR="$(mktemp -d)"

    # Copy local simulation files into this job-specific directory
    cp -r "${LOCAL_SIM_DIR}/." "${JOB_DIR}/"

    # Generate a random seed for this job
    SEED=$RANDOM

    # Append the seed to the end of the main parameter file for this job
    {
      echo ""
      echo "i:Ts/Seed = ${SEED}"
    } >> "${JOB_DIR}/${FILE_TO_RUN}"

    # Job-specific SIM_DIR prefix in S3, e.g. projects/fricke_project/2025-02-18/run_1
    JOB_SIM_DIR="${SIM_DIR_BASE}/run_${i}"

    # Sync this job's inputs to its own S3 prefix
    aws s3 sync "${JOB_DIR}/" "s3://${INPUT_BUCKET}/${JOB_SIM_DIR}/" --only-show-errors

    # Clean up local temp directory
    rm -rf "${JOB_DIR}"

    # Submit the job. TOPAS_XVFB_DISPLAY prevents Xvfb conflicts when multiple jobs share an instance.
    aws batch submit-job \
      --job-name "${JOB_NAME}-${i}" \
      --job-queue "${JOB_QUEUE}" \
      --job-definition "${JOB_DEFINITION}" \
      --container-overrides "{
        \"environment\": [
          {\"name\": \"TOPAS_AWS_BATCH_MODE\", \"value\": \"1\"},
          {\"name\": \"TOPAS_XVFB_DISPLAY\",    \"value\": \":$((98 + i))\"},
          {\"name\": \"JOB_INDEX\",          \"value\": \"${i}\"},
          {\"name\": \"INPUT_BUCKET\",      \"value\": \"${INPUT_BUCKET}\"},
          {\"name\": \"OUTPUT_BUCKET\",     \"value\": \"${OUTPUT_BUCKET}\"},
          {\"name\": \"SIM_DIR\",           \"value\": \"${JOB_SIM_DIR}\"},
          {\"name\": \"FILE_TO_RUN\",       \"value\": \"${FILE_TO_RUN}\"}
        ]
      }"
  done


Run it with:

.. code-block:: bash

   chmod +x topas_submit.sh
   ./topas_submit.sh

* **7.2 Monitor jobs, logs and outputs**.

After the jobs have been launched, they can be monitored in the AWS console by going to the **AWS Batch** Dashboard. If the jobs have just been launched
you will likely see that a set of jobs are **runnable**. Beyond this dashboard, more information can be accessed by clicking on the job queue.

.. figure:: images/part2/step7.3.pdf
   :alt: Monitor submitted jobs in AWS Batch
   :align: center

If everything is fine with the job queue, you should see that the state is **enabled** and that the status is **valid/healthy**.
You may also see what position the jobs are in the queue, and how long they've been in the queue.

.. figure:: images/part2/step7.3c.pdf
   :alt: job queue health
   :align: center

As the jobs begin, you'll see them go from **runnable** to **running** then to either **succeeded** or **failed** in the dashboard.
Once the jobs have completed either successfully or unsuccessfully, the logs can be consulted by going to **CloudWatch**, **log management**, 
then clicking on the log group you created in step :ref:`6.3 <cloud_step6_compute_resources>`. Here you will see various log streams for each 
job that was submitted which you can consult and verify that everything ran as expected.

.. figure:: images/part2/step7.3e.pdf
   :alt: View logs in CloudWatch
   :align: center

If you prefer not to use the AWS console, you can also use the following commands to monitor the status of your jobs. 
To check a specific job:

.. code-block:: bash

   aws batch describe-jobs --jobs <jobId>

To list jobs by status:

.. code-block:: bash

   aws batch list-jobs --job-queue topas-nbio-queue --job-status RUNNING
   aws batch list-jobs --job-queue topas-nbio-queue --job-status SUCCEEDED

Or to view the logs:

.. code-block:: bash

   aws logs tail /aws/batch/topas-nbio --follow

If completed successfully, the outputs should appear in the output bucket in **S3**.

.. figure:: images/part2/step7.3f.pdf
   :alt: Check outputs in S3
   :align: center

* **7.3 Download raw data or post-process results**.

If you do **not** want to run postprocessing on AWS, you can simply download the raw
simulation outputs from your output bucket and post-process locally. This can be done with the following command:

.. code-block:: bash

   mkdir -p ./raw_data
   aws s3 sync s3://topas-nbio-output/projects/<PROJECT_NAME>/<RUN_DATE>/ ./raw_data/

If you also want to post-process your results using AWS compute resources then modify the editable variables and run ``postProcess_submit.sh`` in the same
way as the simulation job submission script. The ``postProcess_submit.sh`` script can be found in the `aws directory`_ of the TOPAS-nBio repository, 
and is also shown below for convenience.

.. code-block:: bash

  #!/bin/bash
  set -e

  ########################################################################################
  # Postprocess submit: upload a local script to S3 and run it on AWS Batch.
  # The container syncs OUTPUT_BUCKET/SIM_DIR (run_1/outputs, run_2/outputs, ...),
  # accumulates files (excluding *.log) and gives them a run_N_ prefix, then runs your
  # script from that directory. 

  # Ensure that your script is runnable on raw data files that are located in the same 
  # directory as the script. See the following for an example: 
  # https://topas-nbio.readthedocs.io/en/latest/Cloud/CloudPart2.html#submitting-simulations-with-topas-submit-sh

  # The new files created by the script are uploaded to SIM_DIR/postP_results/ on S3.
  ########################################################################################

  ########################################################################################
  # Edit these variables for each run
  ########################################################################################

  PROJECT_NAME="fricke_project"
  RUN_DATE="2026-03-03"   # Date when the simulations were launched (must match topas_submit.sh run)
  OUTPUT_BUCKET="topas-nbio-output"

  LOCAL_SCRIPT="./postP.py"   # Local path to your postprocessing script
  EXTRA_PIP_PACKAGES="numpy matplotlib"  # Optional: Python packages required by your script which will be pip installed in the postprocess container

  JOB_QUEUE="topas-nbio-queue"
  POSTPROCESS_JOB_DEFINITION="topas-nbio-postprocess-job"

  ########################################################################################
  # AWS Commands
  ########################################################################################

  SIM_DIR="projects/${PROJECT_NAME}/${RUN_DATE}"
  SCRIPT_BASENAME="$(basename "$LOCAL_SCRIPT")"
  SCRIPT_S3_URI="s3://${OUTPUT_BUCKET}/${SIM_DIR}/${SCRIPT_BASENAME}"

  echo "Uploading script to ${SCRIPT_S3_URI}"
  aws s3 cp "${LOCAL_SCRIPT}" "${SCRIPT_S3_URI}" --only-show-errors

  echo "Submitting postprocess job (job-definition: ${POSTPROCESS_JOB_DEFINITION})"
  aws batch submit-job \
    --job-name "postprocess-${PROJECT_NAME}-${RUN_DATE}" \
    --job-queue "${JOB_QUEUE}" \
    --job-definition "${POSTPROCESS_JOB_DEFINITION}" \
    --container-overrides "{
      \"environment\": [
        {\"name\": \"OUTPUT_BUCKET\",   \"value\": \"${OUTPUT_BUCKET}\"},
        {\"name\": \"SIM_DIR\",         \"value\": \"${SIM_DIR}\"},
        {\"name\": \"SCRIPT_S3_URI\",   \"value\": \"${SCRIPT_S3_URI}\"},
        {\"name\": \"SCRIPT_FILENAME\", \"value\": \"${SCRIPT_BASENAME}\"},
        {\"name\": \"EXTRA_PIP_PACKAGES\", \"value\": \"${EXTRA_PIP_PACKAGES}\"}
      ]
    }"

If using the same S3 bucket structure as suggested in this documentation (topas-nbio-output/projects/<PROJECT_NAME>/<RUN_DATE>/), 
be sure that the ``RUN_DATE`` in the post-processing script corresponds to the date of the results you wish to post-process.

In the example above, the post-processing script is ``postP.py`` which requires the Python packages ``numpy`` and ``matplotlib``. 
In order for the script to work, please ensure that the script is runnable on raw data files contained in the same directory as the script. 
The example post-processing script is shown below which post-processes the phase space output files of the Fricke example and produces a G Value plot.

.. code-block:: python

  import numpy as np
  import glob
  import matplotlib.pyplot as plt

  files = glob.glob('*.phsp')

  gvalue = []
  counter = 0

  for file in files:
      a = np.genfromtxt(file, dtype=str)

      for i in range(len(a)):
          if counter == 0:
              gvalue.append(float(a[i][0]))
          else:
              gvalue[i] += float(a[i][0])
      
      counter += 1 
      
  final_value = np.array(gvalue) / counter

  # plot gvalue
  plt.plot(final_value)
  plt.savefig('gvalue.png')

When submitting the post-processing job, the script will be uploaded to S3, the post-processing container will sync the raw data files from 
the output bucket to a working directory then launch the script to produce the results. The results will be uploaded to the ``RUN_DATE`` 
"sub-directory" of the output bucket under the name ``postP_results``, which contains whatever your script produces and a log file.

Quick reference - important commands
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Here are some of the important commands used in this guide.

.. code-block:: bash

   # Set profile/region (if not already set in your shell)
   export AWS_PROFILE=your-username
   export AWS_DEFAULT_REGION=us-east-1

   # Create core AWS resources (run once)
   aws batch create-compute-environment --cli-input-json file://batch-compute-env.json
   aws batch create-job-queue --job-queue-name topas-nbio-queue --state ENABLED --priority 1 --compute-environment-order order=1,computeEnvironment=topas-nbio-compute-env
   aws logs create-log-group --log-group-name /aws/batch/topas-nbio
   aws batch register-job-definition --cli-input-json file://batch-job-definition.json
   aws batch register-job-definition --cli-input-json file://batch-postP-job-definition.json

   # Submit simulation jobs
   chmod +x topas_submit.sh
   ./topas_submit.sh

   # Monitor status
   aws batch describe-jobs --jobs <jobId>
   aws batch list-jobs --job-queue topas-nbio-queue --job-status RUNNING
   aws batch list-jobs --job-queue topas-nbio-queue --job-status SUCCEEDED

   # View logs
   aws logs tail /aws/batch/topas-nbio --follow

   # Optional: submit post-processing jobs
   chmod +x postProcess_submit.sh
   ./postProcess_submit.sh

.. _ECR: https://aws.amazon.com/ecr/
.. _Docker Hub: https://hub.docker.com/
.. _install the AWS CLI: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
.. _aws directory: https://github.com/topas-nbio/TOPAS-nBio/tree/main/aws
.. _AWS S3 website: https://aws.amazon.com/s3/pricing/
.. _link: https://gallery.ecr.aws/q4p2q6v1/topas-nbio-test