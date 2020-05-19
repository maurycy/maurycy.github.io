---
title: "99.95% API Uptime Without New Servers"
date: 2020-05-18T18:01:23+02:00
draft: false
summary: elo
---

Every API request lost is money lost.  This holds especially true today, where
both mobile and web consume the same backend, and the API became a single
point of failure. In some contexts, like B2B applications, customers may upload
or request critical data that simply cannot disappear because of a temporary
outage, or the company could face unpleasant business consequences.

Approaching [the nine nines](https://en.wikipedia.org/wiki/High_availability#%22Nines%22)
is prohibitibitely expensive and outside of reach of any company on Earth, with
few exceptions. Things do not get better: [it takes only half an hour of downtime per week](https://en.wikipedia.org/wiki/High_availability#Percentage_calculation) to get below two nines.

In this article I show you a very simple approach on how to get at least three
nines, depending on the service level agreement, without investing in new
servers or creating sophisticated devops infrastructure, by effectively
borrowing it from your cloud storage provider.

![Google Cloud Storage to Google Cloud Function to API](images/gcp-storage-func.png)

The idea here is as follows: instead of uploading files directly to your
application, send it to the cloud storage first with the attributes as
[the object metadata](https://cloud.google.com/storage/docs/metadata), and
then send the final request using [a serverless function](https://cloud.google.com/functions/docs/calling/storage).


I'm using Google Cloud Storage here, because I like it, but it works perfectly
fine with literally any cloud that lets you to attach, including
[Amazon S3 object metadata](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-metadata).

```python
friends = ['john', 'pat', 'gary', 'michael']
for i, name in enumerate(friends):
    print ("iteration {iteration} is {name}".format(iteration=i, name=name))
```

The approach

## Asynchronous

## Eventual consistency
