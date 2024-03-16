# Edge OS Repository Workflow Guide

This document outlines the workflow for contributing to the project's repository.

## Workflow Overview

Our workflow is based on Git Flow, with modifications to accommodate our environment. The main branches are:
- `main`: The production branch, containing the released state of the project deployed on our production fleet.
- `development`: The integration branch for features and fixes.
- `staging`: The pre-release branch where final functional testing is performed before clearing for security check, all changes here are deployed on a small subset of devices in production use.
- `secCheck`: Separate branch used for verifying the security of our releases. Deploys on devices our external pentest partner has access to.

### Feature Development

1. **Starting a New Feature**
   - Checkout from `development`: `git checkout development`
   - Create a new feature branch: `git checkout -b feature/<FeatureName>`
   - Example: `git checkout -b feature/local-db`

2. **Completing a Feature**
   - Add and commit changes: `git add .` and `git commit -m "<short & clear commit msg>"`
   - Merge back into `development`: 
     - `git checkout development`
     - `git merge --no-ff feature/<FeatureName> -m "merge feature/<FeatureName> into development"`

### Bug fixes

1. **Starting a New BugFix**
   - Checkout from `development`: `git checkout development`
   - Create a new bug fix branch: `git checkout -b fix/<BugName>`
   - Example: `git checkout -b fix/load-time`

2. **Completing a BugFix**
   - Add and commit changes: `git add .` and `git commit -m "<short & clear commit msg>"`
   - Merge back into `development`: 
     - `git checkout development`
     - `git merge --no-ff fix/<BugName> -m "merge fix/<BugName> into development"`

### Preparing for Release

1. **Tagging Development for Release**
   - After all features for the release are merged, tag `development` with `start/Release/mm/yy`: 
     - `git tag -a start/Release/mm/yy -m "Start for release mm/yy"`

2. **Merging into Staging**
   - Create a pull request from `development` to `staging` with the tag `start/Release/mm/yy`.

### Staging Fixes

- If issues are found in `staging`, create fix branches off `staging`: `git checkout -b fix/<FixName>`
- After fixing, create a pull request from the fix branch back into `staging`, and then into `development` to keep the branches synchronized.

### Security Checks and Fixes

1. **Preparing for Security Check**
   - After staging validation, tag `staging` with `secCheck/Release/mm/yy` and prepare a release branch off `staging`.
   - `git checkout -b Release/mm/yy`

2. **Conducting Security Fixes**
   - Create security fix branches off `Release/mm/yy`: `git checkout -b fix/<FixName>`
   - After fixing, create pull requests from the fix branch into `Release/mm/yy`, then into `secCheck`, `staging`, and `development` sequentially.

### Making a Release

1. **Finalizing the Release**
   - Create a pull request from `Release/mm/yy` into `main` using a squash commit.
   - Tag `main` with `Release/mm/yy`.

2. **Post-Release Hotfixes**
   - For any immediate fixes after release, create hotfix branches off `Release/mm/yy`: `git checkout -b hotfix/<FixName>`
   - Merge back into `Release/mm/yy` through a pull request, then into `staging`& `main`  and `development` to ensure all branches are up to date, following the same PR process for `staging` & `main`. Direct merges into `development` can be done.

Note: All merges into `staging`, `secCheck`, and `main` are be performed via pull requests to ensure proper review and documentation of changes. Merges into `development` can be performed directly to expedite development workflows. Merges to `staging` & `secCheck` require review from 1 peer and merges to `main` require a review from 2 peers.
