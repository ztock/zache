# zache

[![GitHub release](https://img.shields.io/github/release/ztock/zache.svg)](https://github.com/ztock/zache/releases)
[![Github Build Status](https://github.com/ztock/zache/workflows/Go/badge.svg?branch=main)](https://github.com/ztock/zache/actions?query=workflow%3AGo+branch%3Amain)
[![GoDoc](https://godoc.org/github.com/ztock/zache?status.svg)](https://godoc.org/github.com/ztock/zache)

An in-memory key:value store/cache (similar to Memcached) library for Go, suitable for single-machine applications. A fork from [patrickmn/go-cache](https://github.com/patrickmn/go-cache) project.

Any object can be stored, for a given duration or forever, and the cache can be safely used by multiple goroutines.

Although go-cache isn't meant to be used as a persistent datastore, the entire
cache can be saved to and loaded from a file to recover from downtime quickly.

## Installation

```shell
$ go get github.com/ztock/zache
```

## Usage

```go
import (
	"fmt"
	"time"

	"github.com/patrickmn/go-cache"
)

func main() {
	// Create a cache with a default expiration time of 5 minutes, and which
	// purges expired items every 10 minutes
	c := cache.New(5*time.Minute, 10*time.Minute)

	// Set the value of the key "foo" to "bar", with the default expiration time
	c.Set("foo", "bar", cache.DefaultExpiration)

	// Set the value of the key "baz" to 42, with no expiration time
	// (the item won't be removed until it is re-set, or removed using
	// c.Delete("baz")
	c.Set("baz", 42, cache.NoExpiration)

	// Get the string associated with the key "foo" from the cache
	foo, found := c.Get("foo")
	if found {
		fmt.Println(foo)
	}

	// Since Go is statically typed, and cache values can be anything, type
	// assertion is needed when values are being passed to functions that don't
	// take arbitrary types, (i.e. interface{}). The simplest way to do this for
	// values which will only be used once--e.g. for passing to another
	// function--is:
	foo, found := c.Get("foo")
	if found {
		MyFunction(foo.(string))
	}

	// This gets tedious if the value is used several times in the same function.
	// You might do either of the following instead:
	if x, found := c.Get("foo"); found {
		foo := x.(string)
		// ...
	}

	// or
	var foo string
	if x, found := c.Get("foo"); found {
		foo = x.(string)
	}

	// ...
	// foo can then be passed around freely as a string

	// Want performance? Store pointers!
	c.Set("foo", &MyStruct, cache.DefaultExpiration)
	if x, found := c.Get("foo"); found {
		foo := x.(*MyStruct)
			// ...
	}
}
```

## Issues

- [Open an issue in GitHub](https://github.com/ztock/zache/issues)

## License

[MIT](LICENSE)
