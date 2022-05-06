I organized my dotfiles by `./dotfiles/{ORGANIZATION}/{PROJECT}`.

My playbook is configured in such a way that each dot folder in `{ORGANIZATION}/{PROJECT}` will be copied to the host's `HOME` directory.
So for example, `./dotfiles/govtech/tt/.gcp` will be copied to `$HOME/.gcp`

If ya comfortable with the same style, then you can proceed with the next few steps, using `govtech/tt` as example:

# Install ansible

```
brew install ansible
```

## Adding your own dot files

1. Replace `./dotfiles/govtech/tt/.aws/*` with `~/.aws/*`
2. Replace `./dotfiles/govtech/tt/.gcp/*` with `tracetogether-backend/gcp-*.json`
3. Replace `./dotfiles/govtech/tt/.ssh/*` with `~/.ssh/*`
4. Replace `./dotfiles/govtech/tt/.tt_aws_otp/*` with `~/.tt_aws_otp/*`

## Encrypt your dot files!

This is extremely important, dont forget to do this

```
ansible-vault encrypt ./dotfiles/.aws/*
```

```
ansible-vault encrypt ./dotfiles/.gcp/*
```

```
ansible-vault encrypt ./dotfiles/.ssh/*
```

```
ansible-vault encrypt ./dotfiles/.tt_aws_otp/*
```

I use the same password for all of them.

## Usage

In the host that you want to port your dot files over, do:

1. create a password file, and put in the the password you used above. In my case i created a file `.ansible-credentials`
2. Run

```
ansible-pull -U {REPO_URL} playbook.yml --vault-password-file .ansible-credentials --tags govtech/tt
```

This will execute the `playbook.yml`, which in turn execute `tt-be.yml`
Ansible is smart enough to know if your dot files are encrypted or not, and will decrypt using the given credentials automatically.

## Updating your encrypted files

Personally i'll just decrypt it first, for example if i want to edit `./dotfiles/govtech/tt/.aws/config`

```
ansible-vault decrypt ./dotfiles/govtech/tt/.aws/config
```

This will decrypt it into plaintext so i can update it.
Then i'll run

```
ansible-vault encrypt ./dotfiles/govtech/tt/.aws/config
```

with the same password. git add, git commit and push.

## Extending to other projects

1. Create a new `./dotfiles/{ORGANIZATION}/{PROJECT}`
2. Clone `tt-be.yml` to `{PROJECT}.yml`. Update variables `organization` and `project`, as well as the `dir_name` in each task.
3. Update `playbook.yml` to import `{PROJECT}.yml`
4. run

```
ansible-pull -U {REPO_URL} playbook.yml --vault-password-file .ansible-credentials --tags {ORGANIZATION}/{PROJECT}
```

remember to add the tags flag so that you only install credentials for that `{ORGANIZATION}/{PROJECT}`!
