# gonfig [![Go](https://github.com/g41797/gonfig/actions/workflows/go.yml/badge.svg)](https://github.com/g41797/gonfig/actions/workflows/go.yml)

Fork of github.com/tkanos/gonfig - added "using prefixes for environment variables name"

gonfig is a lightweight Golang package for integrating both JSON configs and environment variables into one config object.

## Usage

First define a configuration structure:

```golang
type Configuration struct {
	Port              int
	Connection_String string
}
```

Then fill in our JSON file:

```json
{
	"Port": 8080
}
```

We do not define `Connection_String` in the JSON as we would prefer to define that through an environment variable.

[Best practices of configuration file](https://medium.com/@tkanos/best-practices-for-configuration-file-in-your-code-2d6add3f4b86#.dze386j1t)

using Docker:
```bash
$ docker run [...] -e Connection_String="..." [...]
```

To make this simple for developers we can use gonfig to easily fill in our struct.

```bash
$ go get github.com/g41797/gonfig
```

```golang
import "github.com/g41797/gonfig"

configuration := Configuration{}
err := gonfig.GetConf("pathtomyjonfile.json", &configuration)
if err != nil {
	panic(err)
}
```

Now we can use the configuration as if it was coming from one source.

```golang
// pseudo code
if configuration.Port == 8080 {
	return true
}
if configuration.Connection_String != nil {
	return true
}
```

### using different environment variables name

If your env variable has a different name than the json one, you can just define an env attribute

```golang
type Configuration struct {
	Port              int  `env:"MYAPP_PORT"`
	Connection_String string
}
```

### using prefixes for environment variables name

If an env attribute was not defined , gonfig 
- looking for **prefixed** name
- if this name does not exist - tries to get using former name.

Prefixed name created from the name of the configuration file.

For example for file *example*.json:
```json
{
	"Port": 8080,
    "Connection_String": "connection_string"
}
```
prefix will be **EXAMPLE_** and names of environment variables:
- EXAMPLE_Port
- EXAMPLE_Connection_String

This feature allows to place the same variables used in different configuration files to environment.

## When should gonfig be used?

If you have a limited number of environment configuration variables, it's probably better to set the struct values yourself.

```golang
configuration.Connection_String = os.Getenv("Connection_String")
```

gonfig makes it easier to combine JSON and environment variables into one struct automatically.

## Sample

You can find a sample of the use of Gonfig project [HERE](https://github.com/tkanos/gonfig-sample)


# Links
- [Best practices of configuration file](https://medium.com/@tkanos/best-practices-for-configuration-file-in-your-code-2d6add3f4b86#.dze386j1t)
- [Sample](https://github.com/g41797/gonfig-sample)
