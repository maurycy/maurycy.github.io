---
title: "Run a Static Site On Google Cloud Run"
date: 2019-06-21T14:39:03+02:00
draft: true
---

Google Cloud Run is a newly announced Google Cloud service. The idea is simple: **it’s Docker on serverless**. Google Cloud Run is similar to Google Cloud Function: you write code without worrying about infrastructure.

What makes it unique is that Google Cloud Run works directly with Docker containers, exposes Dockerfile, supports any programming language, custom domains and has much longer timeouts (15 minutes). Google Cloud Run on GKE also supports Websockets and GPUs/TPUs making it interesting choice for resource-intensive applications, like machine learning.

In this guide I’m going to show you how to dive into Google Cloud Run by running a simple static site in few minutes, using literally one command. In a word, your own poor man’s Netlify. This is one of the easiest ways to get the taste of the platform, even if there are definitely better use cases. (Please familiarize yourself with the Pricing first, but don’t worry: you likely won’t pay a dime thanks to the Free Tier.)


Let’s start with actually building a static website. There are few options, from creating one from scratch to using a framework, a static site generator, like Jekyll or Hugo. I will go with the latter since I like it but they’re all valid options. I’d gently ask you to familiarize yourself with the Hugo’s Quick Start first:

```
$ brew install hugo
$ hugo new site static-google-cloud-run
$ cd static-google-cloud-run
$ git init
$ git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
$ echo 'theme = "ananke"' >> config.toml
$ hugo new posts/hello-world.md
```

Now, using your favourite editor, edit the content/posts/hello-world.md by adding some content and removing the draft: true line that effectively publishes it:

```
---
title: "Hello World"
date: 2019-06-18T03:15:54+02:00
---
Hello, world!
```

Also, edit config.tomland customize the site configuration by changing the title and removing example.org from the baseURL:

```
baseURL = ""
languageCode = "en-us"
title = "Static Site on Google Cloud Run"
theme = "ananke"
```

We can generate the HTML and run the server now:

```
$ hugo
$ hugo server
```

The default URL http://localhost:1313/ should show us the following:

Let’s dockerize a static site. The problem with using Hugo not only as a static site generator but also as a server is performance and lack of good Docker images. Nginx is one of the fastest web servers out there, with standard Docker image provided and very little performance footprint.

This our Dockerfile:

```
# Use a nginx Docker image
FROM nginx
# Copy the static HTMLs to the nginx directory
COPY public /usr/share/nginx/html
# Copy the nginx configuration template to the nginx config directory
COPY nginx/default.template /etc/nginx/conf.d/default.template
# Substitute the environment variables and generate the final config
CMD envsubst < /etc/nginx/conf.d/default.template > /etc/nginx/conf.d/default.conf && exec nginx -g 'daemon off;'
```

This is our `nginx/default.template`:

```
server {
    listen       ${PORT};
    server_name  localhost;
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}
```

The first two lines of Dockerfile are pretty obvious. Why are we using a configuration template, though? The Google Cloud Run container runtime contract states:

> The container must listen for requests on 0.0.0.0 on the port defined by the PORT environment variable.

Clear?

If so, let’s build and run the image:

```
$ docker build -t static-google-cloud-run .
$ docker run -p 1312:1312 -e PORT=1312 static-google-cloud-run
```

The http://localhost:1312/ should return us the same as above:

Let’s Google Cloud Run our static site Docker image! This is the final step. I assume that you’ve got Google Cloud SDK installed, enabled Google Cloud Run, and you’re familiar with the gcloud CLI tool. My Google Cloud project name is maurycy-sandbox, so please replace it with any you’re using.

First, let’s build the image using [Google Cloud Build](https://cloud.google.com/cloud-build/):

```
$ gcloud builds submit --tag gcr.io/maurycy-sandbox/static-google-cloud-run
```

After few minutes, you can deploy the service:

```
$ gcloud beta run deploy static-google-clod-run --image gcr.io/maurycy-sandbox/static-google-cloud-run
```

The console will output:

```
Allow unauthenticated invocations to [static-google-clod-run] (y/N)? y
Deploying container to Cloud Run service [static-google-clod-run] in project [maurycy-sandbox] region [us-central1]
⠶ Deploying new service... Creating Resources.
  ⠶ Creating Revision...
  . Routing traffic...
Done.
Service [static-google-clod-run] revision [static-google-clod-run-00001] has been deployed and is serving traffic at https://static-google-clod-run-6mirbza5oa-uc.a.run.app
```

The last line is the most important. It will be different for you but this is where the site is hosted unless we set a custom domain:

Hurray! We’re done: we’ve got an inifnitely scalable static site on Google Cloud Run. You can now even [map your own domain](https://cloud.google.com/run/docs/mapping-custom-domains).

PS. Please don’t forget to stop the service, either with [the Console UI](https://console.cloud.google.com/run) or gcloud:

```
$ gcloud beta run services delete static-google-cloud-run
```
