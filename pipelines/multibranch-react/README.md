# Gitlab pipeline for React apps with multi-branch deploy
## Assumptions
The pipeline will work well with the following configuration:
- Deploying to AWS
- Using Terraform
- Different AWS account per environment
- Using different roles per AWS account
- Using fast-forward for merge requests (no merge commits)
- Deploying to `dev`, `test` and `prod` environments
- Multi-branch builds in the dev environment only
- Branch builds in `dev` are cleaned up (terraform destroy) when branches have been merged
- Merged builds are deployed to `dev` automatically
- Tagged builds are deployed to `test` automatically
- Tagged builds need a manual approval to deploy to `test`
- A build account is used to store artifacts
- A react build is needed per environment

## Requirements
### Gitlab project config
#### Merge requests
Change merge method from `Merge commit` to `Fast-forward merge`

#### Environment variables
The following variables should be created:
- `VOQUIS_PROJECT_NAME` - All lowercase alphanumeric, hyphens only
- `VOQUIS_DOMAIN_NAME` - Top level domain to use with sub-environments
- `AWS_ACCESS_KEY_ID` - AWS Credentials to be used when assuming roles
- `AWS_SECRET_ACCESS_KEY` - AWS Credentials to be used when assuming roles

## Assumed project structure
```
|-- .aws
|   |-- config
|-- terraform
|   |-- environments
|   |   |-- dev
|   |   |-- test
|   |   |-- prod
|   |-- modules
|   |   |-- module1
|   |   |-- module2
|-- frontend
|   |-- build (generated from npm build)
```

### AWS config
Four profiles are required, sample config in `.aws/config`:
```
[profile build]
source_profile = gitlab
role_arn       = arn:aws:iam::123456789012:role/gitlab
region         = eu-west-2

[profile dev]
source_profile = gitlab
role_arn       = arn:aws:iam::123456789013:role/gitlab
region         = eu-west-2

[profile test]
source_profile = gitlab
role_arn       = arn:aws:iam::123456789014:role/gitlab
region         = eu-west-2

[profile prod]
source_profile = gitlab
role_arn       = arn:aws:iam::123456789015:role/gitlab
region         = eu-west-2
```
