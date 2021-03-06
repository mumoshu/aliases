# aliases

 is a tool for resolving command dependencies with containers.

* YAML-based command definition
* Version of command with environment variable
* Call another dependent command from the command

## Install

```
$ go get github.com/k-kinzal/aliases
```

## Get Started

**~/.aliases/aliases.yaml**

```
/usr/local/bin/kubectl:
  image: chatwork/kubectl
  tag: 1.11.2
  volume:
  - $HOME/.kube:/root/.kube
  network: host

/usr/local/bin/helmfile:
  image: chatwork/helmfile
  tag: 0.36.1-2.10.0
  volume:
  - $HOME/.kube:/root/.kube
  - $HOME/.helm:/root/.helm
  - $PWD:/helmfile
  workdir: /helmfile
  network: host
  dependencies:
  - /usr/local/bin/kubectl
```

```
$ eval $(aliases gen)
```

or 

```
$ eval $(aliases gen --binary)
```


Using the option of `--binary` can be used as a command instead of an alias.

## CLI

```
$ aliases --help
NAME:
   aliases - Generate alias for command on container

USAGE:
   aliases [global options] command [command options] [arguments...]

VERSION:
   dev

COMMANDS:
     gen      Generate aliases
     home     Get aliases home path
     help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --config FILE, -c FILE  Load configuration from FILE
   --home value            Home directory for aliases
   --help, -h              show help
   --version, -v           print the version
```

## Version Environment variable

If `/usr/local/bin/kubectl` is defined, you can specify the version in the environment variable with `_VERSION` in the suffix with `kubectl` in the file name capitalized.

```
$ KUBECTL_VERSION=1.11.3 kubectl get all
```

When version environment variable is specified, command is executed by overwriting `tag` defined by YAML.


## Expand Environment Variable

```
env:
  $ENV1: $ENV2
```

```
volume:
- $ENV1:$ENV2:rw
```


Environmental variables are expanded with parameters expressed like this.
`Env1` expands at the timing when aliases generates a command.
`Env2` expands at the timing when you call the command generated by aliases.

## Special Environment Variable

`$PWD` is a special environment variable.
Even if you specify it on the left side, `$PWD` will be expanded only when the command is called.
