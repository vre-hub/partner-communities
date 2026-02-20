# ET Rucio client container

This directory contains the Dockerfile for the Rucio client enabled for the ET VO, used to authenticate users to the ET Data Lake.

Please refer to the official [Rucio clients repo](https://github.com/rucio/containers/tree/master/clients) for more information.

The docker images for the ET Rucio client can be found [here](https://github.com/vre-hub/vre/pkgs/container/et-rucio-client) and they can be pulled with:

```
$ docker pull ghcr.io/vre-hub/et-rucio-client[:imagetag]
```

Use `latest` as the image tag (or omit the image tag altogether) to get the latest official version of the ET rucio client.

## Run with token authentication

You only need to run the container: 

```
$ docker run --rm --user root -it --name=et-rucio-client ghcr.io/vre-hub/et-rucio-client[:imagetag]
```

**Note**: *The value passed to the `--name` option is arbitrary, but should be unique among the names of all the docker containers you are running on your system.*

**Note**: *If you are using `imagetag = latest`, keep in mind that the docker image might have been updated in the container registry since you last downloaded it. In that case, you should first remove the downloaded image, as docker will not pull the new image if it finds it locally. To remove a docker image, list first all your local images with the `docker images` command, note the image ID of the image you want to remove and run the command `docker image rm <imageid>`.*

As soon as you execute a rucio command without a valid token, you will be presented with a personalized link to the ET Indigo IAM. Open the link in a browser and authenticate to the ET Indigo IAM. Go back to the container; Rucio will retrtieve the token from ET Indigo IAM automatically and place it in `/tmp/root/.rucio_root/auth_token_for_default_account` (the `root` parts in this path correspond to the user you are inside the container, which is the user set via the `--user` option in the `docker run` command above).

Images for Rucio client versions 35.7.0 and 38.5.1 contain the multi-RI capable client in a separate executable named `rucio-mri`, because on those versions the multi-RI capable client was not yet part of the official Rucio release.

## Image tag naming rules and convention

Every new commit to the `main` branch of this project that modifies a file in the `ET/containers/rucio-client` directory (except for the `README.md` file) triggers the build of a new `et-rucio-client` docker image in the [container registry](https://github.com/vre-hub/vre/pkgs/container/et-rucio-client). Starting from image tag `38.5.1-mri`, the naming of the image tag follows these rules:

- If the commit has an associated git tag, use the git tag as the docker image tag. If in addition the git tag doesn't contain the string `-rc`, move the `latest` image tag to this docker image. These are the images that are supposed to be good for use. **Users should always use the image with the `latest` tag.**

- Otherwise, use the latest git tag in the history of the `main` branch and add to it a string of the kind `-N-SHA` where `N` is the distance (measured in number of commits) from the latest tag in the `main` branch, and `SHA` is the short SHA of the commit that triggered the build.

The idea behind these rules is the following:

1) Commits that are not tagged in git are supposed to be test commits, and vice versa (i.e. test commits should not be tagged). We continue to build docker images for these commits just in case we want to use them for tests. Since the `latest` tag will not move, users will not be affected by the creation of these images. These images should then be deleted in the [images versions management page](https://github.com/vre-hub/partner-communities/pkgs/container/et-rucio-client/versions).
   
2) If we want to create an image for users with a new rucio client version, we can create either:
   
   a) a release candidate image, or

   b) a final release image.

   For case a), the git tag of the commit should be the rucio client version plus a string `-rcN` where `N` is the release candidate number, for example `39.3.0-rc1`. The `latest` tag will not move, so users will not be affected by the creation of release candidate images, but test users will be able to use them and they will know from the image tag name that this is a release canditate, not a final release.

   For case b), the git tag of the commit should be the rucio client version, for example `39.3.0`. The `latest` tag will move and point to this image.

## Build your own docker image

You can build your own docker image from the Dockerfile and configuration files provided in this repository. Assuming you have all those files in one common directory and that you are inside that directory, you can run the following command to build an image:

```
$ docker build --tag my-et-rucio-client-image .
```

You can then run a container from this image:

```
$ docker run --rm --user root -it --name=my-et-rucio-client my-et-rucio-client-image
```
