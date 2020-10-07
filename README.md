<<<<<<< HEAD
[![license](https://img.shields.io/github/license/RediSearch/redisearch-go.svg)](https://github.com/RediSearch/redisearch-go)
[![CircleCI](https://circleci.com/gh/RediSearch/redisearch-go/tree/master.svg?style=svg)](https://circleci.com/gh/RediSearch/redisearch-go/tree/master)
[![GitHub issues](https://img.shields.io/github/release/RediSearch/redisearch-go.svg)](https://github.com/RediSearch/redisearch-go/releases/latest)
[![Codecov](https://codecov.io/gh/RediSearch/redisearch-go/branch/master/graph/badge.svg)](https://codecov.io/gh/RediSearch/redisearch-go)
[![Go Report Card](https://goreportcard.com/badge/github.com/RediSearch/redisearch-go)](https://goreportcard.com/report/github.com/RediSearch/redisearch-go)
[![GoDoc](https://godoc.org/github.com/RediSearch/redisearch-go?status.svg)](https://godoc.org/github.com/RediSearch/redisearch-go)


# RediSearch Go Client
[![Forum](https://img.shields.io/badge/Forum-RediSearch-blue)](https://forum.redislabs.com/c/modules/redisearch/)
[![Gitter](https://badges.gitter.im/RedisLabs/RediSearch.svg)](https://gitter.im/RedisLabs/RediSearch?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

Go client for [RediSearch](http://redisearch.io), based on redigo.

# Installing 

```sh
go get github.com/RediSearch/redisearch-go/redisearch
```

# Usage Example

```go
package main 
import (
	"fmt"
	"log"
	"time"

	"github.com/RediSearch/redisearch-go/redisearch"
)

func ExampleClient() {

	// Create a client. By default a client is schemaless
	// unless a schema is provided when creating the index
	c := redisearch.NewClient("localhost:6379", "myIndex")

	// Create a schema
	sc := redisearch.NewSchema(redisearch.DefaultOptions).
		AddField(redisearch.NewTextField("body")).
		AddField(redisearch.NewTextFieldOptions("title", redisearch.TextFieldOptions{Weight: 5.0, Sortable: true})).
		AddField(redisearch.NewNumericField("date"))

	// Drop an existing index. If the index does not exist an error is returned
	c.Drop()

	// Create the index with the given schema
	if err := c.CreateIndex(sc); err != nil {
		log.Fatal(err)
	}

	// Create a document with an id and given score
	doc := redisearch.NewDocument("doc1", 1.0)
	doc.Set("title", "Hello world").
		Set("body", "foo bar").
		Set("date", time.Now().Unix())

	// Index the document. The API accepts multiple documents at a time
	if err := c.Index([]redisearch.Document{doc}...); err != nil {
		log.Fatal(err)
	}

	// Searching with limit and sorting
	docs, total, err := c.Search(redisearch.NewQuery("hello world").
		Limit(0, 2).
		SetReturnFields("title"))

	fmt.Println(docs[0].Id, docs[0].Properties["title"], total, err)
	// Output: doc1 Hello world 1 <nil>
}
```


## Supported RediSearch Commands

| Command | Recommended API and godoc  |
| :---          |  ----: |
| [FT.CREATE](https://oss.redislabs.com/redisearch/Commands.html#ftcreate) |   [CreateIndex](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.CreateIndex)          |
| [FT.ADD](https://oss.redislabs.com/redisearch/Commands.html#ftadd) |   [IndexOptions](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.IndexOptions)          |
| [FT.ALTER](https://oss.redislabs.com/redisearch/Commands.html#ftalter) |    [AddField](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.AddField) |
| [FT.ALIASADD](https://oss.redislabs.com/redisearch/Commands.html#ftaliasadd) |  [AliasAdd](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.AliasAdd)         |
| [FT.ALIASUPDATE](https://oss.redislabs.com/redisearch/Commands.html#ftaliasupdate) |     [AliasUpdate](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.AliasUpdate)          |
| [FT.ALIASDEL](https://oss.redislabs.com/redisearch/Commands.html#ftaliasdel) |     [AliasDel](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.AliasDel)        |
| [FT.INFO](https://oss.redislabs.com/redisearch/Commands.html#ftinfo) |   [Info](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.Info)          |
| [FT.SEARCH](https://oss.redislabs.com/redisearch/Commands.html#ftsearch) |  [Search](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.Search)          |
| [FT.AGGREGATE](https://oss.redislabs.com/redisearch/Commands.html#ftaggregate) |   [Aggregate](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.Aggregate)          |
| [FT.CURSOR](https://oss.redislabs.com/redisearch/Aggregations.html#cursor_api) |   [Aggregate](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.Aggregate) + (*WithCursor option set to True)         |
| [FT.EXPLAIN](https://oss.redislabs.com/redisearch/Commands.html#ftexplain) |   [Explain](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.Explain)        |
| [FT.DEL](https://oss.redislabs.com/redisearch/Commands.html#ftdel) |   [DeleteDocument](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.DeleteDocument)        |
| [FT.GET](https://oss.redislabs.com/redisearch/Commands.html#ftget) |    [Get](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.Get) |
| [FT.MGET](https://oss.redislabs.com/redisearch/Commands.html#ftmget) |    [MultiGet](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.Multi) |
| [FT.DROP](https://oss.redislabs.com/redisearch/Commands.html#ftdrop) |   [Drop](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.Drop)        |
| [FT.TAGVALS](https://oss.redislabs.com/redisearch/Commands.html#fttagvals) |    [GetTagVals](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.GetTagVals) |
| [FT.SUGADD](https://oss.redislabs.com/redisearch/Commands.html#ftsugadd) |    [AddTerms](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Autocompleter.AddTerms) |
| [FT.SUGGET](https://oss.redislabs.com/redisearch/Commands.html#ftsugget) |    [SuggestOpts](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Autocompleter.SuggestOpts)  |
| [FT.SUGDEL](https://oss.redislabs.com/redisearch/Commands.html#ftsugdel) |    [DeleteTerms](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Autocompleter.DeleteTerms)  |
| [FT.SUGLEN](https://oss.redislabs.com/redisearch/Commands.html#ftsuglen) |    [Autocompleter.Length](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Autocompleter.Length)  |
| [FT.SYNUPDATE](https://oss.redislabs.com/redisearch/Commands.html#ftsynupdate) |    [SynUpdate](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.SynUpdate) |
| [FT.SYNDUMP](https://oss.redislabs.com/redisearch/Commands.html#ftsyndump) |    [SynDump](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.SynDump) |
| [FT.SPELLCHECK](https://oss.redislabs.com/redisearch/Commands.html#ftspellcheck) |  [SpellCheck](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.SpellCheck)        |
| [FT.DICTADD](https://oss.redislabs.com/redisearch/Commands.html#ftdictadd) |    [DictAdd](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.DictAdd)  |
| [FT.DICTDEL](https://oss.redislabs.com/redisearch/Commands.html#ftdictdel) |    [DictDel](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.DictDel)  |
| [FT.DICTDUMP](https://oss.redislabs.com/redisearch/Commands.html#ftdictdump) |    [DictDump](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.DictDump)  |
| [FT.CONFIG](https://oss.redislabs.com/redisearch/Commands.html#ftconfig) |    [SetConfig](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.SetConfig)、[GetConfig](https://godoc.org/github.com/RediSearch/redisearch-go/redisearch#Client.GetConfig) |

=======
[![Release](https://img.shields.io/github/v/release/redisearch/redisearch.svg?sort=semver)](https://github.com/RediSearch/RediSearch/releases)
[![CircleCI](https://circleci.com/gh/RediSearch/RediSearch/tree/master.svg?style=svg)](https://circleci.com/gh/RediSearch/RediSearch/tree/master)
[![Docker Cloud Build Status](https://img.shields.io/docker/cloud/build/redislabs/redisearch.svg)](https://hub.docker.com/r/redislabs/redisearch/builds/)
[![Forum](https://img.shields.io/badge/Forum-RediSearch-blue)](https://forum.redislabs.com/c/modules/redisearch/)
[![Gitter](https://badges.gitter.im/RedisLabs/RediSearch.svg)](https://gitter.im/RedisLabs/RediSearch?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)
[![Codecov](https://codecov.io/gh/RediSearch/RediSearch/branch/master/graph/badge.svg)](https://codecov.io/gh/RediSearch/RediSearch)
[![Total alerts](https://img.shields.io/lgtm/alerts/g/RediSearch/RediSearch.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/RediSearch/RediSearch/alerts/)

# RediSearch

### Full-Text search over Redis by RedisLabs
<img src="docs/img/logo.svg" alt="logo" width="300"/>

### See Full Documentation at [https://oss.redislabs.com/redisearch/](https://oss.redislabs.com/redisearch/)

# Overview

RediSearch implements a search engine on top of Redis, but unlike other Redis
search libraries, it does not use internal data structures like Sorted Sets.

Inverted indexes are stored as a special compressed data type that allows for fast indexing and search speed, and low memory footprint.

This also enables more advanced features, like exact phrase matching and numeric filtering for text queries, that are not possible or efficient with traditional Redis search approaches.

# Docker Image

[https://hub.docker.com/r/redislabs/redisearch/](https://hub.docker.com/r/redislabs/redisearch/)

```sh
$ docker run -p 6379:6379 redislabs/redisearch:latest
```
# Mailing List / Forum

Got questions? Feel free to ask at the [RediSearch Discussion Forum](http://forum.redislabs.com/c/modules/redisearch).

# Client Libraries

Official (Redis Labs) and community Clients:

| Language | Library | Author | License | Comments |
|---|---|---|---|---|
|Python | [redisearch-py](https://github.com/RedisLabs/redisearch-py) | Redis Labs | BSD | Usually the most up-to-date client library |
| Java | [JRediSearch](https://github.com/RedisLabs/JRediSearch) | Redis Labs | BSD | |
| Go | [redisearch-go](https://github.com/RedisLabs/redisearch-go) | Redis Labs | BSD | Incomplete API |
| JavaScript | [RedRediSearch](https://github.com/stockholmux/redredisearch) | Kyle J. Davis | MIT | Partial API, compatible with [Reds](https://github.com/tj/reds) |
| JavaScript | [redis-redisearch](https://github.com/stockholmux/node_redis-redisearch) | Kyle J. Davis | MIT | |
| C# | [NRediSearch](https://libraries.io/nuget/NRediSearch) | Marc Gravell | MIT | Part of StackExchange.Redis |
| PHP | [redisearch-php](https://github.com/ethanhann/redisearch-php) | Ethan Hann | MIT |
| Ruby on Rails | [redi_search_rails](https://github.com/dmitrypol/redi_search_rails)  | Dmitry Polyakovsky | MIT | |
| Ruby | [redisearch-rb](https://github.com/vruizext/redisearch-rb) | Victor Ruiz | MIT | |
| Ruby | [redi_search](https://github.com/npezza93/redi_search) | Nick Pezza | MIT | Also works with Ruby on Rails |

## Features:

* Full-Text indexing of multiple fields in documents.
* Incremental indexing without performance loss.
* Document ranking (provided manually by the user at index time).
* Field weights.
* Complex boolean queries with AND, OR, NOT operators between sub-queries.
* Prefix matching, fuzzy matching and exact phrase search in full-text queries.
* Support for DM phonetic matching
* Auto-complete suggestions (with fuzzy prefix suggestions).
* Stemming based query expansion in [many languages](https://oss.redislabs.com/redisearch/Stemming/) (using [Snowball](http://snowballstem.org/)).
* Support for logographic (Chinese, etc.) tokenization and querying (using [Friso](https://github.com/lionsoul2014/friso))
* Limiting searches to specific document fields (up to 128 fields supported).
* Numeric filters and ranges.
* Lightweight tag fields for exact-match boolean queries
* Geographical search utilizing Redis' own GEO commands.
* A powerful aggregations engine.
* Supports any utf-8 encoded text.
* Retrieve full document content or just ids.
* Automatically index existing HASH keys as documents.
* Document deletion.
* Sortable properties (i.e. sorting users by age or name).

## Cluster Support

RediSearch has a distributed cluster version that can scale to billions of documents and hundreds of servers. However, at the moment it is only available as part of Redis Labs Enterprise. See the [Redis Labs Website](https://redislabs.com/modules/redisearch/) for more info and contact information.

### License

 Redis Source Available License Agreement - see [LICENSE](LICENSE)
>>>>>>> c926cae0f788d95f0d887ee41df5f1551de704d8
