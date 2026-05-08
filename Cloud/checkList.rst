.. _checklist:

Checklist for new TOPAS-nBio versions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When a new TOPAS-nBio version is released, you may need to update your image build
configuration:

* If Geant4 data files (G4Data) are updated, adjust the download links or versions
  in the Dockerfile used for building the TOPAS-nBio image
  (for example, ``.github/workflows/dockerFile.txt`` in the TOPAS-nBio repository).
* If the CMake configuration or build steps for OpenTOPAS or TOPAS-nBio change,
  align the build commands in ``dockerFile.txt`` with the new instructions.
* Re‑run your ECR and Docker Hub build‑and‑push workflows (for example,
  ``build-push-ecr.yml`` and ``build-push-dockerhub.yml``) so that the new version
  of TOPAS-nBio is available as a container image.