# Terraform Beginner Bootcamp 2023

## Semantic Versioning :mage:

This project is going to utilize semantic versioning for its tagging.
[semver.org](https://semver.org)

The general format:

**MAJOR.MINOR.PATCH**, e.g. `1.0.1`

- **MAJOR** version when you make incompatible API changes
- **MINOR** version when you add functionality in a backward-compatible manner
- **PATCH** version when you make backward compatible bug fixes

## Install the Terraform CLI

### Considerations with the Terraform CLI changes

The Terraform CLI installation isntructions have changed due to gpg keyring changes. We needed to refer to the latest CLI installation instructions in the Terraform docuemntation and update the install script.

[Install Terraform CLI](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)

### Considerations for Linux Distribution

This project is built against Ubuntu. Plese check your Linux distribution and update the script accordingly.

[How to check the OS version in Linux](https://www.cyberciti.biz/faq/how-to-check-os-version-in-linux-command-line)

```sh
cat /etc/os-release
```

```sh
PRETTY_NAME="Ubuntu 22.04.3 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.3 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy
```

### Refactoring into Bash Scripts

While updating the Terraform CLI gpg deprecation issues, we noticed that the updated instructions were much more verbose. We decided to refactor the installation into a bash script to reduce noise within .gitpod.yml.

The script is located at [./bin/install-terraform.sh](./bin/install-terraform.sh)

- This will reduce noice within the GitPod task file [.gitpod.yml](.gitpod.yml) tidy.
- This will allow us an easier to execute and debug manually, if needed
- This will allow better portability for other projects that need to install Terraform CLI

#### Shebang

A [Shebang](<https://en.wikipedia.org/wiki/Shebang_(Unix)>) (pronounced "sha-bang") is the character sequence consisting of the characters number sign and exclamation mark (#!) at the beginning of a script. It is also called sha-bang, hashbang, pound-bang, or hash-pling.

This Shebang is used to tell the operating system what interpreter to use when executing this file. In this case, we are using bash.

```sh
#!/usr/bin/env bash
```

This will search all of the paths specified in the users environment variable `$PATH` for the bash interpreter and use the first one it locates. This is a more portable way to specify the interpreter than hardcoding the path.

#### Execution Considerations

Once the execute bit has been set on the script using [Chmod](<https://en.wikipedia.org/wiki/Chmod>), we can use the `./` shorthand notation to execute the script.

`./bin/install_terraform_cli.sh`

If we are using the script in .gitpod.yml, we need to point the script to a program to interpret the script.

`source ./bin/install_terraform_cli.sh`

#### Linux Permission Considerations

In order to make our script executable, we must change the linux file permissions to enable the execute bit.

```sh
chmod u+x ./bin/install_terraform_cli.sh
```

or

```sh
chmod 744 ./bin/install_terraform_cli.sh
```

### GitPod Lifecycle (Before, Init, Command)

Init scripts will not re-run when restarting an existing workspace. You can read more about the GitPod lifecycle here: <https://gitpod.io/docs/configure/workspaces/tasks>.

### Working Env Vars

#### env command

We can list out all Environment Variables (Env Vars) using the `env` command.

We can filter specific env vars using `grep`, e.g. `env | grep AWS_`

#### Setting and Unsetting Env Vars

Environment variables can be set using the `export` command, unset using the `unset` command, and passed to a command directly without setting them.

```sh
# Variables can be set with export
export HELLO='world'
echo $HELLO
```

```sh
export HELLO='world'
unset HELLO
echo $HELLO
```

```sh
# Variables can be passed to a command directly without setting them
HELLO='goodbye' ./bin/print_message
```

If variables only need to exist within a script, they can be set within the script itself.

```sh
#!/usr/bin/env bash
HELLO='world'
echo $HELLO
```

#### Printing Vars

We can print an env using echo, e.g. `echo $AWS_ACCESS_KEY_ID`

#### Scoping of Env Vars

When you open up a new bash terminal in VS Code, it will not be aware of env vars that you have set in another window.

If you want Env Vars to persist across multiple bash terminals, you can set them in your `~/.bashrc` or `~/.bash_profile` file.

#### Persisting Env Vars in Gitpod

We can persist env vars into GitPod by setting them in Gitpod Secrets Storage.

```sh
gp env HELLO='world'
```

All future workspaces launched will set the env vars for all bash terminals opened in those workspaces.

You can also set Env Vars in the `.gitpod.yml` file, but this can only contain non-sensitive information.

```yml
tasks:
  - name: Setting an env var for a task terminal
    env:
        PRINT_ME: 'Hello World!'
    command: echo "$PRINT_ME"
```

