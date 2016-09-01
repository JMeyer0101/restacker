# Restacker

Restacker is the DevSecOps deployment swiss army knife. You can use it safely & securely deploy, update, migrate, and/or remove AWS stacks.  
Although not feature complete yet, you can begin using it to deploy or re-stack your existing AWS accounts/instances.

## Install it:
- Grab the binary from [GitHub Releases](https://github.com/devsecops/restacker/releases)
- Or build from source:
```
git clone https://github.com/devsecops/restacker.git
cd restacker/source
gem build restacker.gemspec
gem install restacker
# if you're using rbenv, then: rbenv rehash
```

## Use it:

```
$ restacker
Please specify an ACTION

Usage: restacker [ACTION] [OPTONS]

ACTIONS

    list	Lists current stack names
    describe	Describes a stack parameters and template
    restack	Restacks a given deployment
    deploy	Deploys a new stack
    migrate	Migrates from green to blue stack
    remove	Removes the given deployment
    configure	Configure the target account in the restacker.yml file
    dump	Dumps the default configuration for a given template or module
    console	Opens the AWS Console


OPTIONS

    -c, --credentials=PROFILE        Specify the AWS profile credential (~/.aws/config) to use
    -d, --debug                      Show stack traces
    -e, --environment-params=PARAMS  Parameters to add to the instance environment (/etc/profile.d) in the form of
                                     k1=v1,k2=v2. E.g., -p SCORING_URL=https://scoring...
    -h, --help                       Prints these wonderful lines...
    -l, --location=LOCATION          Where to deploy, ksp, kvp, kcp...
    -m, --migrate-module=MODULE      Migration module to execute
    -n, --name=NAME                  Stack prefix stack name (alphanumeric with dashes)
    -o, --migrate-options=OPTIONS    Options required by migration module to migrate from green to
                                     blue stack in the form of k1=v1,k2=v2. E.g., -p EIP=1.2.3.4
    -p, --params=PARAMS              Parameters to override current stack parameters in the form of
                                     k1=v1,k2=v2. E.g., -p AmiId=ami-a4jd7928
    -P, --parameters-file=FILE       YAML formated parameters file (as generated by the dump command) containing parameters to override stack parameters
    -r, --region=REGION              The region where the stack to be restacked exists
    -t, --template=PATH              Template file path to override current stack template
    -u, --username=USERNAME          Your username, defaults to $USER
    -v, --verbose                    Show more output
    -V, --version                    Display the version

Notes:
 - If no template file path is provided when restacking restacker will use the same
   template as if currently deployed.
 - Deployed stack name will be in the form of NAME-DATE using today's date
 ```

## Configure it
- `restacker configure -l <location>`
- Or copy the `restacker-sample.yml` to `~/.restacker/restacker.yml` & update the configurations
The below configuration is an example of MyApp1 and MyApp2 as target accounts and CTRL as master.

```
$ cat ~/.restacker/restacker.yml

:default:
  :label: myapp1

:ctrl: &ctrl_default
  :label: ctrlAcct
  :account_number: '123456789012'
  :role_name: ctrl-ctrl-DeployAdmin
  :role_prefix: "/dso/ctrl/ctrl/"
  :bucket:
    :name: kaos-installers
    :prefix: cloudformation
    :ami_key: latest_amis

:ctrlAcct:
  :region: us-west-2
  :ctrl:
    <<: *ctrl_default
  :target:
    <<: *ctrl_default

:myapp1:
  :region: us-west-2
  :ctrl:
    <<: *ctrl_default
    :role_name: ctrl-myapp1-DeployAdmin
  :target:
    :label: myapp1
    :account_number: '098765432123'
    :role_name: myapp1-dso-DeployAdmin
    :role_prefix: "/dso/human/"

:myapp2:
  :region: us-west-2
  :ctrl:
    <<: *ctrl_default
    :role_name: ctrl-myapp2-DeployAdmin
  :target:
    :label: myapp2
    :account_number: '123098456765'
    :role_name: myapp2-dso-DeployAdmin
    :role_prefix: "/dso/human/"

...
```

## More Info
Checkout the [docs](./docs/) for detailed information.
