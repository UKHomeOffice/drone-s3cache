# drone-s3cache [![Docker Repository on Quay](https://quay.io/repository/ukhomeofficedigital/drone-s3cache/status "Docker Repository on Quay")](https://quay.io/repository/ukhomeofficedigital/drone-s3cache)

Drone plugin for caching build artifacts in S3 


This plugin allows for caching build artifact directories containing external libraries, packages, etc in S3 which should speed up build process. Cache is currently keyed by repository name e.g. `UKHomeOffice/<project_name>`. This should suffice in most cases. S3 cache automatically expires after 7 days.  

## Configuration

The following parameters are used to configure the plugin:

- `drone_s3_cache_mode`: defines the mode of operation. Possible values `pull`, `push`.
- `drone_s3_cache_folders`: list of local project directories to be cached in S3, e.g.:
```
drone_s3_cache_folders: 
  - .bundle
  - folder1
  - folder2/foo
```

- `drone_s3_cache_bucket_name`: S3 bucket name. This can be configured in drone secrets as `DRONE_S3_CACHE_BUCKET_NAME`. Ensure drone has permissions to `get` and `put` objects in that bucket.

AWS credentials need to be present in drone secrets (preferred!) as `DRONE_S3_CACHE_AWS_ACCESS_KEY_ID`, `DRONE_S3_CACHE_AWS_SECRET_ACCESS_KEY` or passed to the container via environment variables (local testing!) as `AWS_SECRET_KEY_ID`, `AWS_SECRET_ACCESS_KEY`


### Drone configuration example

```yaml
pipeline:
  drone_s3_cache_pull:
    image: quay.io/ukhomeofficedigital/drone-s3cache:latest
    drone_s3_cache_mode: "pull"

  build:
    image: ruby:2.3.1-alpine
    commands:
      - bundle install --path .bundle
    when:
      event: [push, pull_request]

  drone_s3_cache_push:
    image: quay.io/ukhomeofficedigital/drone-s3cache:latest
    drone_s3_cache_folders: ".bundle"
    drone_s3_cache_mode: "push"
```

## Release process

Push / Merge to master will produce a docker
[image](https://quay.io/repository/ukhomeofficedigital/drone-s3cache?tab=tags) with a tag `latest`.

To create a new release, just create a new tag off master.

## Contributing

We welcome pull requests. Please check issues and existing PRs before submitting a patch.

## Author

Marcin Ciszak [marcinc](https://github.com/marcinc)
