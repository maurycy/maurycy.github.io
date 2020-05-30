---
title: "Do Not Upload Files To Your App"
date: 2020-05-20T13:10:39+02:00
draft: true
---

THESE DAYS, a regular web app outsources file handing to a cloud provider, like
[Google Cloud Storage](https://cloud.google.com/storage) or [Amazon S3](https://aws.amazon.com/s3/).
On the business side of things, cloud storages are generally cheaper by giving
much better service level agreements and by incurring less operational overhead,
scaling infinitely without having to buy new servers nor hire additional devops
resources. They are advantageous technically, too. A single HTTP request for
images or other static assets does not have to compete for resources with
the dynamic stack, like Ruby on Rails or PHP.

What is frequently overlooked, though, is the other side of the equation. Not
the `GET`, but the `POST` or `PUT`. People upload files first to the app, then
the app reuploads it to the cloud:

![Placeholder](https://miro.medium.com/max/865/1*Sloyat7YCd-psuf9lNw48w.png)

A file (eg: an image) is uploaded once to the app, incurring bandwith
costs (ingress), then to the cloud, inducing further expenses (outgress):

![Placeholder](https://miro.medium.com/max/865/1*Sloyat7YCd-psuf9lNw48w.png)

Using the same HTTP pipeline for long-running HTTP requests is especially
unfavorable in slot-priced environments like Heroku where long-running queries
might cause regular ones to time out, similarly so in metered environments
where every second of waiting is billed.

