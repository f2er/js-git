# File System

This interface describes the filesystem interface used in the js-git project.

All examples use `yield` to consume [continuables][], assuming generators with [gen-run][] wrapping, but can be used with ES5 code as well.  Streams are in [simple-stream][] format.

```js
// ES6 syntax
var target = yield fs.readlink("/path/to/symlink");

// ES5 syntax
fs.readlink("/path/to/symlink")(function (err, target) {
  if (err) throw err;
  // do something with target
});
```

## stat(path) -> continuable&lt;stat>

Stat a file getting mtime, ctime, size, mode, etc as a JS object with the following structure:

  - ctime [seconds, nanoseconds]
  - mtime [seconds, nanoseconds]
  - dev
  - ino
  - mode 1000,1010,1110 | 0755,0644
  - uid
  - gid
  - size

```js
var stat = yield fs.stat("/path/to/my/file.txt");
```

## read(path, [encoding]) -> continuable<binary_or_string>

Read a file as a single string or binary buffer

## write(path, value, [encoding]) -> continuable

Write a file as a single string or binary buffer

## readStream(path, [options]) -> continuable&lt;stream>

Open a file by path for reading and return a simple-stream.

  - options.start - start at offset
  - options.end - end at offset

```js
var input = yield fs.readStream("/path/to/my/file.txt");
```

## writeStream(path, [options]) -> continuable&lt;sink>

Create a simple-stream sink that saves the stream to disk.

  - options.mode - create file with custom mode (base 8 string or integer)

```js
var save = yield fs.writeStream("/path/to/output.txt");
console.log("Saving input to disk...");
yield save(input);
console.log("Saved");
```

## unlink(path) -> continuable

Delete a file from working directory by path.  returns a continuable.

```js
yield fs.unlink("/file/i/want/to/delete.log");
```

## readlink(path) -> continuable&lt;target>

Read a symlink and give the string it points to.

```js
var target = yield fs.readlink("/path/to/symlink");
```

## symlink(path, target) -> continuable

Create a symlink at path with given target.

```js
yield fs.symlink("/path.to/symlink", "../target");
```

## readdir(path) -> continuable&lt;stream>

Returns a stream of filenames in the target path.

```js
var nameStream = yield fs.readdir("..");
```

## rmdir(path) -> continuable

Delete a directory.

```js
yield fs.rmdir("/directory/to/kill");
```

## mkdir(path) -> continuable

Create a directory

```js
yield fs.mkdir("/path/to/create");
```

## rename(old, new) -> continuable

Rename a file or directory.

```js
yield fs.rename("/.git/1ae5mir17t298lcu.tmp", ".git/objects/e7/aecb2c038b16c2ef544b1413d41cbe40aa514d");
```

## Chroot Capabilities

The exports object is also itself a chroot function.  This returns a new version of the fs with a path prefix added.

```js
// This example uses simple-fs, but the idea is the same for all.
var fs = require('simple-fs')('/home/tim/Code/js-git');
var metaFs = fs(".git");
// fs now points to the working tree and metaFs points to the .git folder inside it.
```

If your module requires a callback to initialize the filesystem, simply make sure that the final exported instance is a chroot function.

```js
require('simple-html5-fs')('/js-git', function (err, fs) {
  if (err) throw err;
  var metaFs = fs(".git");
  // fs now points to the working tree and metaFs points to the .git folder inside it.
});
```

# Concrete Implementations

 - [simple-fs](https://github.com/creationix/simple-fs) - Implementation for Node.JS

[gen-run]: https://github.com/creationix/gen-run
[continuables]: https://github.com/creationix/js-git/blob/master/specs/continuable.md
[simple-stream]: https://github.com/creationix/js-git/blob/master/specs/simple-stream.md
