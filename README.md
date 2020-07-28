# concourse-fetch

A repository for fetching github repo's and placing them on aws s3 with versioning using git.

## Prerequisites

You must make sure you have the following pre-requisites installed: 

- [direnv](https://direnv.net/#basic-installation)
- [concourse-docker](https://github.com/RealOrko/concourse-docker/blob/master/README.md#prerequisites)
- [nodejs-handlebars-cli](https://github.com/RealOrko/nodejs-handlebars-cli/blob/master/README.md#prerequisites)

## Getting Started

There are a couple of steps to getting this going. Then end result is a local dev environment :)

### Setup .envrc

You will have to setup an `.envrc` file in the root of the checkout directory.

The format is: 

```
export PIPELINE_NAME=fetch
export PIPELINE_JOB_NAME=copy_concourse_fetch_source_to_s3
export AWS_DEFAULT_REGION=eu-west-1
export AWS_SECRET_ACCESS_KEY=...
export AWS_ACCESS_KEY_ID=...
export AWS_S3_BUCKET_NAME=concourse-published
export GITHUB_PRIVATE_KEY='-----BEGIN OPENSSH PRIVATE KEY-----
...
...
...
-----END OPENSSH PRIVATE KEY-----'
```

Don't forget to run `direnv allow` once this is done. 

### Generate and fly the template

Next we want to generate and fly the template which is explained further below, but please run the following commands from the checkout directory:

 - [bin/generate](https://github.com/RealOrko/concourse-fetch/blob/master/bin/generate)
 - [bin/fly](https://github.com/RealOrko/concourse-fetch/blob/master/bin/fly)

### Browse to concourse

Now you want to see the result in a browser. Please browse to `http://localhost:8080/` and login using the following details: 

 - Username: `test`
 - Password: `test`

 If everything has gone right, you should see the following pipeline output. 

#### Home view

This is the home view of concourse.

![Home](https://github.com/RealOrko/concourse-fetch/blob/master/docs/images/home-pipeline.png)

#### Run view

This is the view after the pipeline has run.

![Run](https://github.com/RealOrko/concourse-fetch/blob/master/docs/images/run-pipeline.png)

#### AWS S3 output

These are the artifacts you should end up with.

![Artifacts](https://github.com/RealOrko/concourse-fetch/blob/master/docs/images/aws-s3-output.png)

## Going deeper

Further details about concourse-fetch.

### The control plane

The control plane is extended through [fetch.yml](https://github.com/RealOrko/concourse-fetch/blob/master/templates/fetch.yml). 

A simple example for fetching this repo from master would be: 

```yml
  - name: concourse_fetch_source
    uri: git@github.com:RealOrko/concourse-fetch.git
    private_key: ((github_private_key))
    branch: master
    version_branch: versions
```

### Adding a new repository

After adding a new git repository you can then run the following commands to see it run locally:

 - [bin/generate](https://github.com/RealOrko/concourse-fetch/blob/master/bin/generate)
 - [bin/fly](https://github.com/RealOrko/concourse-fetch/blob/master/bin/fly)

### Promotion using branches

If you wanted to source using promotion from a `foo` release branch then you would change it to this: 

```yml
  - name: concourse_fetch_foo_source
    uri: git@github.com:RealOrko/concourse-fetch.git
    private_key: ((github_private_key))
    branch: foo 
    version_branch: foo_versions
```

You could then for example release by merging master into foo, `master`->`foo`. Branches always have to be created in the source repositories before you fly the pipeline. 

Try [bin/versionify](https://github.com/RealOrko/concourse-fetch/blob/master/bin/verionify) for any new branches you require before flying the pipeline making sure you adjust branch names appropriately.

### S3 versioning

This repository uses the same file names to publish artifacts, so you should enable s3 bucket versioning, to support consuming these artifacts via the [s3-resource](https://github.com/concourse/s3-resource).


Happy fetching folks! :)
