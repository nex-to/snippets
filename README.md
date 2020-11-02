# Nex-To Snippets
This is where to store text files that don't belong to a specific project and snippets that we paste into the odoo console.

## Files

- git/hooks/post-receive: place this file in .git/hooks for the repo you want to enable auto-updates for

- scripts/restart-odoo: triggered by post-receive hook to restart odoo after a repo is updated

## Auto-update and odoo restart

- make sure the restart-odoo script is in /sbin and executable, and the post-receive file is in the place in the repo
- this line needs to be in /etc/sudoers
`gitpull ALL=(ALL:ALL) NOPASSWD: /sbin/restart-odoo`
- clone the repo into /opt/odoo13/odoo-custom-addons
- run this command on the cloned repo:
`sudo chown -R gitpull:odoo13 ` [path to cloned repo]
- create an action for the repo at .github/workflows/git-pull.yml like this:
```
name: git-pull
on:
  push:
    branches:
      - master
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: cd and git pull via ssh
        uses: garygrossgarten/github-action-ssh@release
        with:
          command: cd /opt/odoo13/odoo-custom-addons/${{ github.event.repository.name }} && git pull
          host: ${{ secrets.HOST }}
          username: gitpull
          passphrase: ${{ secrets.PASSPHRASE }}
          privateKey: ${{ secrets.PRIVATE_KEY}}
```
