# Quick Start

## Releases

**Whenever we** do [a new release on Github](https://github.com/marianoj/surfsc/releases) (pre-release for staging) it triggers a [Github Action](https://github.com/marianoj/surfsc/actions) that  builds an image from the repository, uploads it to GCP Image directory and deployed to GCP Kubernetes Engine where the Pod is remplace with a new one

## Themes/Plugins updates

Plugins uploads are disabled in production for security and stability reasons.

To perform an update, you should first update the plugins on staging or your local  environment and you can push the plugins using [Wordmove](quick-start.md#wordmove), SSH or with a new [Release](quick-start.md#releases)

## Wordpress updates

Wordpress uploads are disabled for stability reasons and possible problems with plugins compatibility.

So to perform a wordpress update, you should wait until the wordpress team updates the official docker image, the just do a new release as described [here](quick-start.md#releases), i strongly recommend to test it first on staging to make sure everything works correctly.

## Wordmove

Wordmove is a tool that makes it easy to pull/push or migrate wordpress environments

Example pulling uploads from production&#x20;

`docker run --rm -v ~/.ssh:/root/.ssh:ro -v ~/dev/surfsc/wordpress/:/html welaika/wordmove wordmove pull -u -e production`

#### SSH permission caveat

If you are on a Winodws or Linux host, then you could get permission errors while trying to use your ssh keys. To work around this problem we've a trick for you:

`docker run -it --rm -v ~/.ssh:/tmp/.ssh:ro welaika/wordmove`

Mounting `.ssh/` inside `/tmp/` will tell the image to automatically copy it over in `/root/` and to fix permissions.\


## Google Cloud Plataform Storage

We have a CDN setup and media is uploaded to a [GCP storage bucket](https://console.cloud.google.com/storage/browser/surfsc-wpoffload-media;tab=objects?forceOnBucketsSortingFiltering=false\&project=surf-strength-coach-242708\&prefix=SurfStrengthCoach-How-To-Surf-A-New-Spot\&forceOnObjectsSortingFiltering=false)&#x20;

To update/upload not synced files

`gsutil -m cp -r ./wordpress/wp-content/uploads/. "gs://surfsc-wpoffload-media/wp-content/uploads"`

### Making all objects in a bucket publicly readable <a href="#buckets" id="buckets"></a>

`gsutil iam ch allUsers:objectViewer gs://surfsc-wpoffload-media/files`

### Create secret with the GCP CDN service account <a href="#buckets" id="buckets"></a>

kubectl create secret generic wp-offload-media-credentials --from-file ./gpc-cdn-key.json`e`
