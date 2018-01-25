# Docker images for research environment

## Workflow of development

1. Push commits to develop branch until docker image is ready to deploy.

2. When it's ready to deploy docker image, merge develop branch
   to master branch

    ```shell
    $ git checkout master
    $ git merge develop
    ```

3. Push to GitHub and invoke automated build.

4. Tag the latest commit on master branch, and push it.

    ```shell
    $ git tag <tag>
    $ git push origin <tag>
    ```

## How to use images

See `README.md` in each directories.
