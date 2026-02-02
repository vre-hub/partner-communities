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
