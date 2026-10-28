# GovStack Analytics Building Block — Technical Specification

Markdown export of the Analytics Building Block technical specification (v0.3, September 2026), originally published on the Experience Digital Transformation GitBook:
https://experience-digital-transform.gitbook.io/experience-digital-transformation/analytics/technical-specifications

The specification lives in [`spec/`](spec/README.md). `spec/SUMMARY.md` provides the page order, so the folder can be connected to a GitBook space with Git Sync.

## Repo Structure

```sh
README.md
/spec # the markdown files which are used to build the specification in GitBook
/api # the openapi specification
/test # the test plan and tests
  plan.md
/examples # examples for deploying, configuring, and testing applications which implement the behaviors specified by this building block
  /application-a
    README.md # instructions for deployment/testing
    docker-compose.yaml # example deployment file
      db
      web
      adaptor
      security-server
    Caddyfile # example config for "adaptor"
    Dockerfile # dockerfile to build "adaptor"
  /application-b
  /application-c
```

## ORB setup

Documentation for ORB setup is available here:
[ORB setup instruction](https://govstack-global.atlassian.net/wiki/spaces/GH/pages/191692823/ORB+setup+instruction)
