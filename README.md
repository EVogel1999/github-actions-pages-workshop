# GitHub Actions & Pages Workshop

This workshop contains all the code needed to create a very basic static website that is hosted (for free) on [GitHub Pages](https://pages.github.com/) as well as a basic [GitHub Actions](https://github.com/features/actions) workflow to run a simple python script.

## Non-Technical Pre-Requisites

You will need to have a [GitHub](https://github.com/) account in order to follow along with this workshop.  Basic working knowledge of `git` is required: namely the concepts of `repositories`, `branches`, and `commits`.

For this workshop, **make sure the repository you create is `public`**.  GitHub restricts feature access to private repositories unless you pay for GitHub.

## Technical Pre-Requisites

You will need the following installed in order to write the code locally:

- [Git](https://git-scm.com/downloads)
    - [Local Git Configured to GitHub](https://docs.github.com/en/get-started/quickstart/set-up-git#authenticating-with-github-from-git)
- A Text Editor
    - [Visual Studio Code](https://code.visualstudio.com/)
    - Any other text editor you prefer

**Don't want to install these?**  You can pull up an online text editor via GitHub by creating a new "repository" and pressing the `.` key.

## GitHub Actions

GitHub Actions is GitHub's Continuous Integration / Continuous Deployment (CI/CD) feature.  It allows you to set up listeners for changes within a git repository:

- Push changes to X branch
- Pull request is accepted
- Etc

When the *condition* specified is met, lets say someone commits something to the `main` branch, it will run a set of predetermined automated steps.  These steps could be anything:

- Check for bad words saved in the code
- Code linters
- Run tests
- Build a distributable untested package of code
- Deploy a new version of a website
- Release a new version of the software
- Update an API hosted in a cloud provider like AWS
- Etc

### Goal

What we are going to do is write a simple "Hello World" python script and write a `workflow` to run that script when someone pushes commits to the `main` branch.

### Hello World Script

The following is what is written for the script:

```python
if __name__ == '__main__':
    print("Hello World!")
```

Create, save, and commit this code to `src/main.py` within this repository.

### Terminology

A `workflow` is a set of jobs that accomplishes an overarching goal.  You can have multiple workflows for a single repository.  For instance, one could only check for your code's quality while another workflow would deploy your code.

A `job` is a set of steps that accomplishes a specific goal.  Using the previous example, checking for code quality could mean creating two jobs: one to lint the code and another to run tests.

A `step` is the actual command to run.  This could be anything from running the `python` terminal command to building a `docker image` and pushing it to `dockerhub`.  There are many organizations that support official packages so you don't have to do things like manually configure python in a workflow.

### The CI Workflow

To create a new workflow, create a new file: `.github/workflows/ci.yml`.  GitHub uses the `.github` and `workflows` folders to see if it should run a new actions workflow.  You can name the `yaml (.yml)` file whatever you want.  For this workshop, I went with `ci.yml`.

In the file, lets save the following to it:

```yaml
name: Continuous Integration

on:
  push:
    branches:
      - main
```

What we are saying here is that we want to name this workflow "Continuous Integration" and we only want to run the workflow when there is a **push** on the **main** branch.  You can also specify other things such as if a pull request was created or if a change was made to a specific folder in the repo.

As the file is now, there are no jobs or steps so this workflow essentially does nothing.  So lets add a new job:

```yaml
jobs:
  run-script:
    runs-on: ubuntu-20.04
```

What we are saying here is that we want to define a new "run-script" job that runs on an `ubuntu` VM or docker image.  `run-on` is important because it specifies what computing environment you are using, including terminal commands you are allowed to use.  For instance, the `ls` command is valid in linux/mac but the equivalent windows command is `dir`.

The last part we need is to define steps associated with the job:

```yaml
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-python@v4
        with:
          python-version: '3.10' 
      - run: python src/main.py
```

We need to do three things in order to run our script:

1. Run `checkout` - which is required for every new job we define
2. Set up python - which we can use an official package to do
3. Run our actual script - the terminal command to run

And that's it!  If you save and push this code to GitHub, go to the "Actions" tab and you should see the workflow running.  One of the steps in the workflow should have "Hello World!" printed out in the output.

### Total Workflow Code

```yaml
name: Continuous Integration

on:
  push:
    branches:
      - main

jobs:
  run-script:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-python@v4
        with:
          python-version: '3.10' 
      - run: python src/main.py
```