---
title: "99.95% API Uptime Without New Servers"
date: 2020-05-18T18:01:23+02:00
draft: true
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
fine with literally any cloud that lets you to attach object metadata and
fires a trigger after upload. In the Amazon S3 nomenclature, we'd deal with
[object medata](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-metadata),
[event notifications](https://docs.aws.amazon.com/AmazonS3/latest/dev/NotificationHowTo.html) and
[AWS Lambda](https://aws.amazon.com/lambda/).

## How to Send a Request?

```python
# Lorem ipsum Python code

# importing the hashlib module
import hashlib

def hash_file(filename):
   """"This function returns the SHA-1 hash
   of the file passed into it"""

   # make a hash object
   h = hashlib.sha1()

   # open file for reading in binary mode
   with open(filename,'rb') as file:

       # loop till the end of the file
       chunk = 0
       while chunk != b'':
           # read only 1024 bytes at a time
           chunk = file.read(1024)
           h.update(chunk)

   # return the hex representation of digest
   return h.hexdigest()

message = hash_file("track1.mp3")
print(message)
```

## Asynchronous

## Eventual consistency
