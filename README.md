# Fansunite addition
+ We have added support to generate grpc stubs and services for elixir as well!f

# Protocol Buffer Compiler Containers

This repository contains the Dockerfile for generating gRPC and protobuf code
for various languages, removing the need to setup protoc and the various gRPC
plugins lcoally. It relies on setting a simple volume to the docker container,
usually mapping the current directory to `/defs`, and specifying the file and
language you want to generate.

If you're having trouble, see [Docker troubleshooting](#docker-troubleshooting) below.

> Note - throughout this document, commands for bash are prefixed with `$` and commands
> for PowerShell on Windows are prefixed with `PS>`. It is not required to use "Windows
> Subsystem for Linux" (WSL)

## Usage

Pull the container:

```sh
$ docker pull fansunite/protoc-all:1.0.0
```

After that, travel to the directory that contains your `.proto` definition
files.

So if you have a directory: `~/my_project/protobufs/` that has:
`myproto.proto`, you'd want to run this:

```sh
$ cd ~/my_project/protobufs
$ docker run -v `pwd`:/defs fansunite/protoc-all:1.0.0 -f myproto.proto -l elixir #or go, csharp, etc
```

```powershell
PS> cd ~/my_project/protobufs
PS> docker run -v ${pwd}:/defs fansunite/protoc-all:1.0.0 -f myproto.proto -l elixir #or go, csharp, etc
```

The container automatically puts the compiled files into a `gen` directory with
language-specific sub-directories. So for Golang, the files go into a directory
`./gen/pb-go`; For ruby the directory is `./gen/pb-ruby`.

## Options

You can use the `-o` flag to specify an output directory. This will
automatically be created. For example, add `-o my-gen` to add all fileoutput to
the `my-gen` directory. In this case, `pb-*` subdirectories will not be created.

You can use the `-d` flag to generate all proto files in a directory. You cannot
use this with the `-f` option.

You can also use `-i` to add extra include directories. This can be helpful to
_lift_ protofiles up a directory when generating. As an example, say you have a
file `protorepo/catalog/catalog.proto`. This will by default output to
`gen/pb-go/protorepo/catalog/` because `protorepo` is part of the file path
input. To remove the `protorepo` you need to add an include and change the
import:

```
$ docker run ... fansunite/protoc-all:1.0.0 -i protorepo -f catalog/catalog.proto -l go
# instead of
$ docker run ... fansunite/protoc-all:1.0.0 -f protorepo/catalog/catalog.proto -l go
# which will generate files in a `protorepo` directory.
```
