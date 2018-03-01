# Kibana on Dokku

Deploy your own instance of [Kibana](https://www.elastic.co/fr/products/kibana)
on [Dokku](https://github.com/dokku/dokku)!

This setup is intended to be used as a `syslog` container log visualiser,
using the [LogTrail](https://github.com/sivasamyk/logtrail) Kibana plugin.

To aggregate the logs, you can go with the full ELK stack by running
[Logstash](https://github.com/rclement/dokku-logstash) side-by-side.


# Notice

This project makes use of:

- The official (legacy) [Kibana](https://hub.docker.com/_/kibana/) Docker image 
- The [LogTrail](https://github.com/sivasamyk/logtrail) plugin for Kibana

For compatibility reasons with `dokku-elasticsearch`, deployed versions are:

- ElasticSearch: 2.4.6
- Kibana: 4.6.6
- LogTrail: 4.x-0.1.14


# Setup

## Requirements

Be sure to properly setup a [Dokku](https://github.com/dokku/dokku) instance.

The following Dokku plugins need to be installed:

- [dokku-elasticsearch](https://github.com/dokku/dokku-elasticsearch)

## App and database

1. Create the `kibana` app:

```
    dokku apps:create kibana
```

2. Create the elasticsearch database (if not done yet for `logstash`):

```
    export ELASTICSEARCH_IMAGE="elasticsearch"
    export ELASTICSEARCH_IMAGE_VERSION="2.4.6"
    dokku elasticsearch:create logstash
    dokku elasticsearch:link logstash kibana
```


# Deploy

1. Clone this repository:

```
    git clone https://github.com/rclement/dokku-kibana.git
```

2. Setup Dokku git remote (with your defined domain):

```
    git remote add dokku dokku@example.com:kibana
```

3. Push `kibana`:

```
    git push dokku master
```

4. Fix proxy ports:

```
    dokku proxy:ports-add kibana http:80:5601
    dokku proxy:ports-remove kibana http:5601:5601
```


# Run

`kibana` should be running at: `http://kibana.example.com`


# Troubleshooting

## Error while installing the LogTrail plugin

This error can happen during the LogTrail plugin installation:

```
    The command '/bin/sh -c kibana-plugin install ...' returned a non-zero code: 137
```

This usually means there is not enough RAM allocated for the virtual machine
where Dokku is installed. A minimum of 2GB is recommended.

