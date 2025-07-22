# Rucio client for ET and mock CE servers

The following are instructions about how to install the ET Rucio client to access ET MDC data on the ET Data Lake. But it is easy to change configuration files for the CE server(see comments in the configuration files).

## Request a Rucio account

First of all, you need to have an account on Rucio. In the future this will be done automatically, but for the moment the procedure is manual. 
To request a Rucio account, write an email to Lia Lavezzi (lia.lavezzi@to.infn.it). She should be able to find you in the ET members database (ETMD), 
so make sure to provide her your name as it appears in the ETMD.

## Install Rucio client

### Manual installation using pip

1. In your terminal:
   ```
   pip install rucio-clients
   ```
   The above command installs the latest version of rucio-clients. For installing a specific version, for example 32.8.0, this command is needed:
   ```
   pip install rucio-clients==32.8.0
   ```
   ET server is using 32.8.0 version. CE server is using 35.7.0 version.

2. If you want to authenticate to Rucio using OIDC, download ```rucio.default.cfg.et``` from this repository, rename it as ```rucio.cfg``` and edit it to add your rucio account.
If instead you want to authenticate to Rucio using username and password (not recommended; use it only if no other auth method works for you), download ```rucio.cfg``` from this repository and edit it to add your account, username and password.

3. Move your ```rucio.cfg``` file inside ```/opt/rucio/etc/``` or into another directory where the Rucio client searches for the config file. 
If the Rucio client can not find ```rucio.cfg```, it will tell what are the paths where it tries to find it. But sometimes it's more complicated and rucio can't find configuration file anywhere. 
In this case, you need tell Rucio the specific path to the file by setting the ```RUCIO_HOME``` environment variable:
   ```
   export RUCIO_HOME=/path/to/rucio.cfg
   ```

### Instalation via docker

We assume that you have installed docker already.

We don't have yet a repository where to host ET container images. Therefore, in this section, we give the instructions to build a docker container image from the files available in this repository and then run a container from that image.

#### Build a container image

1. Download ```Dockerfile``` from this repository.

2. Download ```rucio.default.cfg.et``` from this repository.

3. Build a container image (the dockerfile should be named ```Dockerfile```):
   ```
   docker build --tag <image-name[:tag]> --file </path/to/Dockerfile>
   ```

4. (Optional) You may upload the container image to a personal container registry (e.g. dockerhub, ghcr).

#### Run a container

1. The default Rucio client configuration file ```rucio.default.cfg.et``` used in the image above, uses the OIDC authentication method. If instead you want to authenticate to Rucio using username and password (not recommended; use it only if no other auth method works for you), download ```rucio.cfg``` from this repository and edit it to add your account, username and password.

2. The ```docker run``` commands shown below can be used to run a container from an image hosted locally or remotely (you just need to set ```<image-name[:tag]>``` accordingly).

   **For authentication with OIDC**

   Run a container from the above image, passing your rucio account as an environment variable (an initialization script present inside the container will insert your account into the default Rucio client configuration file at ```/opt/rucio/etc/rucio.cfg```):
   ```
   docker run --rm -it -e RUCIO_CFG_CLIENT_ACCOUNT=<your-rucio-account> <image-name[:tag]>
   ```
   To use token authentication you need to:
   - Register to ET-IAM at [https://iam-et.cloud.cnaf.infn.it/](https://iam-et.cloud.cnaf.infn.it/)
   - Send the User Id to the Rucio Admin (lia.lavezzi_at_to.infn.it). The User Id is the string under your personal icon when you login to [https://iam-et.cloud.cnaf.infn.it](https://iam-et.cloud.cnaf.infn.it) and go to the dashboard.
   - Wait for the Rucio Admin to create an identity for you.
   - Use the proper configuration file in the Rucio client, which needs to be like this:
   ```
   [client]
   rucio_host = https://et-rucio-server.to.infn.it:443
   auth_host = https://et-rucio-server.to.infn.it:443
   auth_type = oidc
   account = <account name>
   oidc_issuer = et-indigo-iam
   oidc_scope = openid profile
   auth_token_file_path = /tmp/token
   request_retries = 3
   ```
   - Test that everything works, in this way:
       - login into the client
       - remove the X509 stuff:
       ```
       unset X509_USER_CERT 
       unset X509_USER_KEY
       voms-proxy-destroy
       ```
       - try `rucio whoami`, you should get a link which sends you to `https://iam-et.cloud.cnaf.infn.it/` and provides you with a string upon login. Copy and paste the string on the terminal and you get the token!
       - try `rucio get <scope>:<filename>` to download a file from either TORINO or LOUVAIN RSEs.

   **For authentication with username and password**
    
   Run a container from the above image, mounting your ```rucio.cfg``` file into ```/opt/rucio/etc/rucio.cfg``` inside the container to overwrite the default Rucio client configuration file:
   ```
   docker run --rm -it -v </path/in/your/machine/to/rucio.cfg>:/opt/rucio/etc/rucio.cfg <image-name[:tag]>
   ```
   **For authentication with x509**

   You need to have x509 certificates: public key - ```usercert.pem``` and privite key - ```userkey.pem```. Send your ```dn``` to admin of the server. Change ```rucio.cfg``` for x509 authentication as it is written in the comment inside the file.
   ```
   docker run --rm -it -v </path/in/your/machine/to/rucio.cfg>:/opt/rucio/etc/rucio.cfg <image-name[:tag]>
   ```
