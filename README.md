# Local dev stack for learn.med

learn-med-stack is a vagrant-based stack for developing the learn.med LMS. It's based on [Trellis](https://roots.io/trellis), uses Ansible for provisioning the local server, and can be used to provision the production and staging servers also.

## What's included

Trellis will configure a server with the following and more:

* Ubuntu 14.04 Trusty LTS
* Nginx (with optional FastCGI micro-caching)
* PHP 5.5
* [MariaDB](https://mariadb.org/) as a drop-in MySQL replacement (but better)
* SSL support (A+ on https://www.ssllabs.com/ssltest/)
* Composer
* WP-CLI
* sSMTP (mail delivery)
* Memcached
* Fail2ban
* ferm

## Requirements

* Ansible >= 1.9.2 (**not** 2.0) - [Install using Pip on Mac OS (see below)](#installing-ansible-using-pip-on-mac-os) • [Docs](http://docs.ansible.com/) • [Windows docs](https://roots.io/trellis/docs/windows/)
* Virtualbox >= 4.3.10 - [Install](https://www.virtualbox.org/wiki/Downloads)
* Vagrant >= 1.5.4 - [Install](http://www.vagrantup.com/downloads.html) • [Docs](https://docs.vagrantup.com/v2/)
* vagrant-bindfs >= 0.3.1 - [Install](https://github.com/gael-ian/vagrant-bindfs#installation) • [Docs](https://github.com/gael-ian/vagrant-bindfs) (Windows users may skip this)
* vagrant-hostsupdater - [Install](https://github.com/cogitatio/vagrant-hostsupdater#installation) • [Docs](https://github.com/cogitatio/vagrant-hostsupdater)

### Installing Ansible using Pip on Mac OS

    $ sudo easy_install pip
    $ sudo pip install "ansible>=1.9.2,<2.0"

For Windows, see the [Windows docs](https://roots.io/trellis/docs/windows/)

## Installation

1. Create a directory called `learn.med` on your local machine. *Important note: The full paths to these directories must not contain spaces or else [Ansible will fail](https://github.com/ansible/ansible/issues/8555).*
2. Clone this repo inside the `learn.med` directory. This will create a `learn-med-stack` directory.
3. Run `ansible-galaxy install -r requirements.yml` inside the learn-med-stack directory to install external Ansible roles/packages.
4. Clone the [learn-med-app](https://git.med.uottawa.ca/E-Learning/learn-med-app) repo inside the `learn.med` directory. This will create a `learn-med-app` directory.

You should now have the following directories at the same level somewhere:

```
learn.med/            - Primary folder for the project
├── learn-med-stack/  - Your clone of this repository
└── learn-med-app/    - A clone of the learn-med-app repository
```

## Sensitive settings are encrypted using ansible-vault

Database passwords, LRS credentials and other sensitive information are stored in the repo (in `vault.yml` files) encrypted via ansible-vault.

### Add a .vault_pass file to learn-med-stack

To get the decryption key installed on your local machine, contact members of the team for the `.vault_pass` file (or check in the LMS Basecamp project in the "Passwords and Stuff" text file) and add it in the `learn-med-stack` directory. Make sure never to commit that file to the repo (it's in the .gitignore)

### Update encrypted settings

Use `ansible-vault edit <file>` to edit the settings in the encrypted `vault.yml` files.

*Important:  Avoid using the `decrypt` ansible-vault command. If your intention is to view or edit an encrypted file, use the `view` or `edit` commands instead. Any time you decrypt a file, you risk forgetting to re-encrypt the file before committing changes to your repo.*

## Development setup

1. Run `vagrant up` from inside the learn-med-stack directory. During the first run, everything will be installed, which will take a while. When prompted, enter your computer's password to add entries to your local `hosts` file.
2. Download a copy of the production database. To do this, `cd learn-med-app` and `bundle exec cap production wpcli:db:pull`. You'll need to install ruby (through rbenv is recommended) and required gems. See the [learn-med-app/README.md](https://git.med.uottawa.ca/E-Learning/learn-med-app/) for installation steps.
3. Access the local dev environment via https://learn.med.uottawa.dev/ or https://apprendre.med.uottawa.dev/ and confirm the unverified certificates when asked in your browser.

## SSL

Trellis will also *auto-generate* self-signed certificates for development purposes.

## Updating to new versions of Trellis

1. Add the remote to the roots/trellis to your local git: `git remote add trellis https://github.com/roots/trellis.git` and do `git fetch`
2. Create a new branch called `update-trellis`
3. Find a release of trellis that you want to update to (see the tags)
4. Rebase to that release and fix conflicts or cherry-pick individual commits.
5. After testing, rebase the `update-trellis` branch to `master`

## Documentation

- Trellis documentation is available at [https://roots.io/trellis/docs/](https://roots.io/trellis/docs/).
- See the [learn-med-app wiki](https://git.med.uottawa.ca/E-Learning/learn-med-app/wiki) for documentation on deploying to servers, updating the code for the LMS, running reports, etc.
