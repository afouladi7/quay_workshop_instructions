# Lab 5 - Working with images

Note for students:

*This portion of the lab is optional. Pushing and pulling images from a docker v2 compliant registry is a core feature of any registry on the market today. If your instructor has provided you with access to a virtual machine with container tools pre-installed, then charge ahead. Else, you'll need a machine with either Podman, Skopeo, or docker installed to follow along.*

*Note - If you're using podman with the following commands, replace the word `docker` with `podman` before executing commands.




## Pull an image from Quay

* From the Quay dashboard, click `centos-mirror`
* Click the `tags` icon
* On any of the tags in your repository, click the `fetch tag` icon
![Fetch tag icon](/images/fetch-tag-icon.png)

The `Fetch Tag` dialog appears

* From the `Image Format` drop down, select `Docker Pull (by tag)`
* Copy the docker pull command to the clipboard


Switch to a terminal with access to Podman or Docker:
* Paste in the contents of the clipboard, for example:

 `docker pull quay.apps.cluster-c2bmc-1d4d.c2bmc-1d4d.example.opentlc.com/user1org/centos-mirror:8.2.2004
`

 * Append the following tls flag to the end of the command before pressing enter : `--tls-verify=false`
 
 *This is necessary because we're using a non-trusted CA to sign the certificate that fronts this lab instance of Quay*

 * Press enter, and you should see your container tooling begin to download this image

## Verify 
To verify your image is now stored locally, run the following from a command prompt:
```
docker images | grep centos-mirror
```
The response should look something like this:
```
quay.apps.cluster-c2bmc-1d4d.c2bmc-1d4d.example.opentlc.com/user1org/centos-mirror             8.2.2004                        831691599b88   2 weeks ago     223 MB
```

## Push the image into another repo on Quay
To come full circle here, we're going to take the centos image you just pulled down in the steps above and push it into the `userXorg/test` repo. Remember, we did not set this repo's state to `mirror`, so Quay will allow us to push images into it from a client.

* From the Quay dashboard, click the `test` repo
* From the pull command helper, copy everything after `docker pull` to the clipboard, so that we have the FQDN of the image. This will be our image `destination`
* Switch back to terminal
* Paste your clipboard contents into the terminal
* From your terminal history, copy the value we found earlier using the `docker images | grep centos-mirror` command. It should look something like this: `quay.apps.cluster-c2bmc-1d4d.c2bmc-1d4d.example.opentlc.com/user1org/centos-mirror`. This is our image `source`.

### Tag the source image to it's new destination
* Using the values captured above, type the command below in your terminal, replacing the values with your own:
```
docker tag <source> <destination>
```
Example (notice the TAG appended to the source and destination image FQDN, you'll get this from your `docker images` command): 
```
docker tag quay.apps.cluster-c2bmc-1d4d.c2bmc-1d4d.example.opentlc.com/user1org/centos-mirror:8.2.2004 quay.apps.cluster-c2bmc-1d4d.c2bmc-1d4d.example.opentlc.com/user1org/test:8.2.2004

```

### Login to Quay with your container CLI

In order to push images into an image repository hosted on Quay, you must be authenticated as a user who has `Write` or `Admin` permissions to your target repo. You can verify what permissions a given user has in the settings tab of a given repository.

To login, you can use any standard image management client: IE Podman, Skopeo, docker.

Because the instance of Quay being used in this lab is temporary, we've used a self-signed TLS cert to secure the end point. By default, these are not trusted by image management tools. In order to login or push an image, you'll normall have to explicitly tell your tooling that you trust this registry.

Last note, rather than have you type your password into a terminal that is capturing your command history, Quay can generate a temporary encrypted version of your password that's time boxed and safer to use rather than a clear text version. We'll be using this to login to Quay in the steps below.

* From the Red Hat Quay dashboard, click your username dropdown in the top right of the UI, and select `Account Settings`.
* Click the gear icon on the left for the settings page.
* Under `Docker CLI Password`, click `Generate Encrypted Password`
* Enter your password `openshift`
* Click `Docker Login` on the left
* Copy the command shown to the clipboard
* Paste this command in your terminal

`If you're using podman:`
* Replace the word `docker` with `podman`
* Append the flag `--tls-verify=false` to tell Podman that we trust this registry
* You should see a `Login Succeeded!` message, you're not authenticated to Quay and your credentials are cached for further registry interactions.

`If you're using docker on Linux`
* You'll need to edit the /etc/docker/daemon.json file to include the `insecure-registries` key, with the value being set to the Quay instance used in the lab (requires sudo privilege). 

Example daemon.json:

```
{
"insecure-registries": [
    "quay.apps.example.opentlc.com"
    ]
}
```



`If you're using Docker on Mac / Windows`
* In your Docker desktop preferences or settings, you'll need to add the hostname of Quay to your allowed insecure-registries list, under the `Docker Engine` settings, defined in the configuration file. An example of this configuration is shown below:

```
{
  "experimental": false,
  "debug": true,
  "insecure-registries": [
    "quay.apps.example.opentlc.com"
  ]
}

```
* After updating your config file, press `Apply and Restart`
* Run the `docker login` command provided from the Quay UI described in the steps above.

### Push the image
* From your terminal, copy the command below and replace <destination> with the FQDN of your destination image from above before pressing enter.

```
docker push quay.apps.cluster-c2bmc-1d4d.c2bmc-1d4d.example.opentlc.com/user1org/test:8.2.2004 --tls-verify=false
```


 ## Optionally, Tag an image as latest
The Quay UI offers a trivial way (aside from just CLI tagging) to change image tags. For example, we'll assign the tag `latest` to an image in the `test` repo.

* From the Quay dashboard, click `test`
* Click the `tags` icon
* Click 

## Finished
[Previous](https://github.com/mbach04/quay_workshop_instructions/blob/master/lab4.md)