# env
Tag-based environment configuration for structs.

[![Godoc](https://godoc.org/github.com/codingconcepts/env?status.svg)](https://godoc.org/github.com/codingconcepts/env)
[![Build Status](https://travis-ci.org/codingconcepts/env.svg?branch=master)](https://travis-ci.org/codingconcepts/env)
[![Go Report Card](https://goreportcard.com/badge/github.com/codingconcepts/env)](https://goreportcard.com/report/github.com/codingconcepts/env)

## Installation

``` bash
$ go get -u github.com/codingconcepts/env
```

## Usage

``` go
package main

import (
	"fmt"
	"log"
	"time"

	"github.com/codingconcepts/env"
)

type config struct {
	Secret            []byte        `env:"SECRET" required:"true"`
	Region            string        `env:"REGION"`
	Port              int           `env:"PORT" required:"true"`
	Peers             []string      `env:"PEERS"` // you can use `delimiter` tag to specify separator, for example `delimiter:" "`
	ConnectionTimeout time.Duration `env:"TIMEOUT" default:"10s"`
}

func main() {
	c := config{}
	if err := env.Set(&c); err != nil {
		log.Fatal(err)
	}

	...
}
```

``` bash
$ ID=1 SECRET=shh PORT=1234 PEERS=localhost:1235,localhost:1236 TIMEOUT=5s go run main.go
```
## Valid Tags and Combinations
|Tag Name|Example|Notes|
|---|---|---
|`env`|\`env:"REGION"\`|Mandatory tag indicating the name of the env var.|
|`delimiter`|\`delimiter:" "\`|Optional unless using delimiter other than `,`. Note that the specified delimiter applies to all of `env`, `choices` and `default` values for a given env var.|
|`choices`|\`choices:"a,b,c"\`<br>\`choices:"y\|n"&nbsp;delimiter:"\|"`|Validates env var value against a set of valid values. Assumes the set delimiter is `,` unless the `delimiter` tag is used in combination.|
|`default`|\`default:"text"\`<br>\`default:"a,b,c"\`<br>\`default:"1&nbsp;2&nbsp;3"&nbsp;delimiter:"&nbsp;"\`<br>\`default:"1\|3\|5"&nbsp;choices:"1\|2\|3\|4\|5"&nbsp;delimiter:"\|"\`|Substitute value if env var is non-existent or null. Default can also be a set of values, but must be a set or subset of `choices` tag value, if used in combination.|
|`required`|\`required:"true"\`|Forces a value to be present for the env var, unless the `default` tag is used. Valid values are "true" or "false".|

## Supported field types

- `bool` and `[]bool`
- `string` and `[]string`
- `[]byte`
- `int`, `int8`, `int16`, `int32`, `int64`, `[]int`, `[]int8`, `[]int16`, `[]int32`, and `[]int64`
- `uint`, `uint8`, `uint16`, `uint32`, `uint64`, `[]uint`, `[]uint8`, `[]uint16`, `[]uint32`, and `[]uint64`
- `float32`, `float64`, `[]float32`, and `[]float64`
- `time.Duration` and `[]time.Duration`
