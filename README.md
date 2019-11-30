```
$ docker build -t maurycy-com .
$ docker run -p 1312:1312 -e PORT=1312 maurycy-com
```

```
$ gcloud builds submit --tag gcr.io/maurycy-sandbox/maurycy-com
$ gcloud beta run deploy maurycy-com --image gcr.io/maurycy-sandbox/maurycy-com
```
