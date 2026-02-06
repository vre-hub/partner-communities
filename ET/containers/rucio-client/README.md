# ET Rucio client container

This directory contains the Dockerfile for the Rucio client enabled for the ET VO, used to authenticate users to the ET Data Lake.

Please refer to the official [Rucio clients repo](https://github.com/rucio/containers/tree/master/clients) for more information.

The Docker images for this project can be found [here](https://github.com/vre-hub/vre/pkgs/container/et-rucio-client) and it can be pulled with:

```bash
$ docker pull ghcr.io/vre-hub/et-rucio-client<:imagetag>
```

## Run with token authentication

You only need to run the container: 

```bash
$ docker run --rm --user root -it --name=et-rucio-client ghcr.io/vre-hub/et-rucio-client<:imagetag>
```

As soon as you execute a rucio command without a valid token, you will be presented with a personalized link to the ET Indigo IAM. Open the link in a browser and authenticate to the ET Indigo IAM. Go back to the container; Rucio will retrtieve the token from ET Indigo IAM automatically and place it in `/tmp/root/.rucio_root/auth_token_for_default_account`.

Images for Rucio client versions 35.7.0 and 38.5.1 contain the multi-RI capable client in a separate executable named `rucio-mri`, because on those versions the multi-RI capable client was not yet part of the official Rucio release.

## Naming conventions

The construction of the docker image tag now follows these rules:

- If the commit has an associated git tag: Use the git tag as the docker image tag
   - If in addition the git tag doesn't contain the string -rc:
      - Move the latest image tag to this docker image
        
These are the images that are supposed to be good for use. Users should always use the latest image.

Otherwise:

- Use the latest git tag in the history of the main branch and add to it a string of the kind -N-SHA where N = distance (measured in number of commits) from the latest tag and SHA = short SHA of the commit.
