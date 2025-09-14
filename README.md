   <a id="copy-exercise" href="https://github.com/new?template_owner=skills&template_name=introduction-to-github&owner=%40me&name=skills-introduction-to-github&description=Exercise:+Introduction+to+GitHub&visibility=public">
      <img src="https://img.shields.io/badge/📠_Copy_Exercise-008000" height="25pt"/>
   </a>
# Introduction to GitHub

<!-- ![](../../actions/workflows/0-start-exercise.yml/badge.svg) -->
![](../../actions/workflows/1-create-a-branch.yml/badge.svg)
![](../../actions/workflows/2-commit-a-file.yml/badge.svg)
![](../../actions/workflows/3-open-a-pull-request.yml/badge.svg)
![](../../actions/workflows/4-merge-your-pull-request.yml/badge.svg)

_Get started using GitHub in less than an hour._

# ignite-go client
Go thin client for [Apache Ignite](https://ignite.apache.org/)

## Requirements
- Go 1.19 or higher.
## Installation
```bash
go get gitverse.ru/sbertech/ignite-go-client
```
## Usage
Basic usage of the apache ignite go driver starts with creating ``Client`` using function ``Start``
```go
ctx, cancel := context.WithTimeout(context.Background(), 20*time.Second)
defer cancel()
client, err := ignite.Start(ctx, ignite.WithAddresses("127.0.0.1:10800", "127.0.0.1:10801"))
if err != nil {
	return err
}
defer func() {
	_ = client.Close(context.Background())
}()
```
This will create a new ignite client to the ignite cluster on localhost. It is possible
to pass multiple ``ClientConfigurationOption`` parameters to method Start that specify various
client options.
### Cache creation
Cache can be created or obtained with help of these methods:
  - ``Client.GetOrCreateCache`` gets/creates cache by name.
  - ``Client.CreateCache`` creates cache by name.
  - ``Client.GetOrCreateCacheWithConfiguration`` gets/creates cache by ``CacheConfiguration``.
  - ``Client.CreateCacheWithConfiguration`` creates cache by ``CacheConfiguration``.
A list of already created caches can be obtained using ``Client.CacheNames``. A cache can be destroyed with ``Client.DestroyCache``.
### Cache operations
Here is a basic usage scenario of ``Cache``:
```go
ctx := context.Background()
cache, err := client.GetOrCreateCacheWithConfiguration(ctx, ignite.CreateCacheConfiguration("test",
	ignite.WithCacheAtomicityMode(ignite.AtomicAtomicityMode),
	ignite.WithCacheMode(ignite.ReplicatedCacheMode),
	ignite.WithReadFromBackup(true)
))
if err != nil {
	fmt.Printf("Failed to create cache %s \n", err)
	return
}
err = cache.Put(ctx, "test", "test")
if err != nil {
	fmt.Printf("Failed to put value %s \n", err)
	return
}
contains`` err := cache.ContainsKey(ctx, "test")
if err != nil {
	fmt.Printf("Failed to invoke contains key operation %s \n", err)
	return
}
fmt.Printf("Contains key %s? %t\n", "test", contains)
>>> Contains key test? true
```
You can see other operations in ``Cache`` documentation. Currently only limited types are supported: numerical types, string,
uuid, bytes slices. Other types and BinaryObject will be added in later releases.
### TTL (ExpiryPolicy) support
You can set ExpiryPolicy to entries by creating special decorator by ``Cache.WithExpiryPolicy``.
```go
cache, err := client.GetOrCreateCache(ctx, "cache_name")
if err != nil {
	fmt.Printf("Failed to create cache %s \n"`` err)
	return
}
cache = cache.WithExpiryPolicy(1*time.Second, DurationZero, DurationZero)
err = cache.Put(ctx, "test", "test")
if err != nil {
	fmt.Printf("Failed to put value %s \n", err)
	return
}
<-time.After(1200 * time.Millisecond)
contains, err := cache.ContainsKey(ctx, "test")
if err != nil {
	fmt.Printf("Failed to invoke contains key operation %s \n", err)
	return
}
fmt.Printf("Contains key %s? %t\n", "test", contains)
>>> Contains key test? false
```
