# heroku-private-buildpack

This supports loading a build pack from a private git repository.

It hooks in only to the release and compile api.

## Usage

1. Add the buildpack: 

  ```shell
  heroku buildpacks:add https://github.com/allanpaiste/heroku-private-buildpack.git \
    --index 1 -a my-application
  ```

1. Add a file in the project root `.heroku-private-build.lst` that would contain plain list 
   of private build pack repository URLs:

  ```text
  git@github.com:my-company/my-secrent-buildpack-a.git
  git@github.com:my-company/my-secrent-buildpack-b.git
  ```

1. Register SSH key in GitHub that would have access to the private repositories. Guide for
   doin so can be found [HERE](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account ).

1. Add the base64 enocded deploy key to the BUILDPACK_SSH_KEY env variable for specific 
   service that uses heroku-private-buildpack (If you already have id_rsa, do no overwrite it 
   and choose a different name instead. Example: heroku_buildpacks):
    
   ```shell
   heroku config:set BUILDPACK_SSH_KEY=$(cat path/to/your/keys/id_rsa | base64)
   ```

## Buildpack Metadata

Note that this solution has currently a downside of not performing proper metadata consolidation
from the private buildpacks and returns empty metadata response instead. 

There is thought a possibility of composing their own merged meta-data via `.heroku-private-metadata.yml` 
which would be sent (when present) as output of `bin/release`.
