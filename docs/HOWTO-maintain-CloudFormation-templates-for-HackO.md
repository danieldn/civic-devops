# How to maintain updates to CF Templates

CloudFormation templates are a beast to maintain.  We keep them in GitHub for source control and change tracking, but we also have to keep a copy of all of them in S3 for CloudFormation deploys.  And sometimes we find it convenient to make changes closer to the metal, but those have bitten us time and again - trying to figure out the state of the cluster is pretty trying, even when CF changeset updates are working smoothly.

So here's the workflow that keeps us in sync between GitHub and S3 (and hopefully doesn't drift too far from the CloudFormation live configuration, since the whole point is to deploy changes as we decide to make them):

1. Clone the [hackoregon-aws-infrastructure](https://github.com/hackoregon/hackoregon-aws-infrastructure) repo.
2. `git checkout -b a-branch-name` to capture changes off-master.
3. Make your changes to whichever templates you need to - one type of change per PR, please.  Let's not mix grab-bag commits into a single PR (or worse, to a single commit) and lose the ability to understand the history of changes.
4. add/commit/push your changes, then generate a PR.
5. Merge the PR.
6. `git checkout master` and `git pull` to make sure your filesystem is in sync with the current version of our repo's templates.
7. `aws cp . s3://hacko-infrastructure-cfn --recursive` to sync the repo's contents with the S3 bucket.
8. Go make your CloudFormation changeset with the current code, and execute it.
9. (Cross fingers that the changeset deploys without rollback.)