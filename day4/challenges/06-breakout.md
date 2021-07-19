# 💎 Breakout 1:  Deploy the sample application with GitHub workflows💎

⏲️ *Est. time to complete: 60 min.* ⏲️

## Here is what you will learn 🎯

We have already seen how to create a professional CI/CD workflow with GitHub Actions workflows. Although we have only deployed the shared Azure resources of the sample application so far, we have learned all necessary things to deploy the other bounded contexts to a development and testing environment. 

In this challenge you will learn how to:

- deploy all bounded contexts of the sample application
- see Azure resource tags in action
- add data to the sample application

## Table of contents

1. [Plan your work](#plan-your-work)

## Plan your work

As in the previous challanges we want to reflect our work for the breakout session on the project board.
We have already created a _Note_ on our project board, which says:
_Deploy the sample application_

To describe the outstanding work for this breakout session, we create the following issue in the imported trainingdays repository, assign the label `azdc-breakout` and link it to the _Note_:

```text
Deploy all SCM bounded contexts

Prepare GitHub Actions workflows to deploy all SCM bounded contexts.
Deploy all bounded contexts to a development and testing environment.
```

After this is done, the project board should look like this:

![GitHub board overview 07](./images/gh-board-overview-07.png)

## Prepare the workflows 

Now we have to prepare all extsing workflows. As we pushed changes to the master branch in the previous challenge, wee need to pull these changes first:

```shell
git checkout master
git pull
```

Then we create a new feature branch for the outstanding work:

```shell
git branch cicd/all
git checkout cicd/all

# or
git checkout -b cicd/all
```

Now we need to replace the organisation name in each job condition of the following workflows:

- day4-scm-contactsapi.yml
- day4-scm-frontend.yml
- day4-scm-resourcesapi.yml
- day4-scm-searchapi.yml
- day4-scm-visitreports.yml

:::tip

📝 In total, five files must be changed. You can check this with the `git status`command:

```shell
git status
On branch cicd/all
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   .github/workflows/day4-scm-contactsapi.yml
	modified:   .github/workflows/day4-scm-frontend.yml
	modified:   .github/workflows/day4-scm-resourcesapi.yml
	modified:   .github/workflows/day4-scm-searchapi.yml
	modified:   .github/workflows/day4-scm-visitreports.yml

no changes added to commit (use "git add" and/or "git commit -a")
```
:::

After this is done, commit and push the changes:

```shell
git add .
git commit -m "activate day4 workflows"
git push --set-upstream origin cicd/all
```

## Create and merge the Pull Request

Create a pull request to push the changes to the master branch. Close the issue _Deploy all SCM bounded contexts_ with the pull request.
After the pull request is created, wait for all status checks and merge the pull request.

After the pull request is merged, nativate to the `Àctions` page and observe the workflows.

## Add data to the sample application