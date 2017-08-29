# Docker images for research environment

## Workflow of development

1. Push commits to develop branch until docker image is ready to deploy
   in order not to invoke automated build on Docker Hub too much.

2. When it's ready to deploy docker image, update the `version` file,
   then merge develop branch to master branch with `--squash` option.

    ```shell
    $ git checkout master
    $ git merge --squash develop
    ```

3. Push to GitHub and invoke automated build.

4. Tag the latest commit on master branch, and push it.

    ```shell
    $ git tag <tag>
    $ git push origin <tag>
    ```

    Here, `<tag>` should be the same as the content of the `version` file.

## How to use images

See `README.md` in each directories.
