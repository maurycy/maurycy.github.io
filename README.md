```
$ docker build -t maurycy-com .
$ docker run -p 1312:1312 -e PORT=1312 maurycy-com
```

```
$ gcloud config set run/platform managed
$ gcloud config set run/region us-central1
```

```
$ gcloud builds submit --tag gcr.io/maurycy-sandbox/maurycy-com
$ gcloud run deploy maurycy-com --image gcr.io/maurycy-sandbox/maurycy-com
```

```
$ gcloud run domain-mappings create --service maurycy-com --domain maurycy.com
```
