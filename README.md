
# hyperdrive-import-files

Import the contents of a folder into a [hyperdrive](https://github.com/mafintosh/hyperdrive), and optionally keep watching for changes.

[![Build Status](https://travis-ci.org/juliangruber/hyperdrive-import-files.svg?branch=master)](https://travis-ci.org/juliangruber/hyperdrive-import-files)

## Example

```js
const hyperdrive = require('hyperdrive')
const memdb = require('memdb')
const hyperImport = require('hyperdrive-import-files')

const drive = hyperdrive(memdb())
const archive = drive.createArchive()

hyperImport(archive, 'a/directory/', err => {
  // ...
})
```

## Installation

```bash
$ npm install hyperdrive-import-files
```

## API

### hyperImport(archive, target, [, options][, cb])

Recursively import `target`, which is the path to a directory or file,  into `archive` and call `cb` with the potential error. The import happens sequentually. Returns a `status` object.

Options

- `live`: keep watching
- `resume`: assume the archive isn't fresh
- `basePath`: where in the archive should the files import to? (defaults to '')
- `ignore`: [anymatch](https://npmjs.org/package/anymatch) expression to ignore files

To enable watching, set `live: true`, like this:

```js
const status = hyperImport(archive, target, { live: true }, err => {
  console.log('initial import done')  
})
status.on('error', err => {
  // ...  
})
// when you want to quit:
status.close()
```

If you want to resume importing an already existing archive, set `resume: true `. This module then checks a file's size and mtime to determine whether it needs to be updated or created.

If you want to import into a subfolder, set `basePath`:

```js
hyperImport(archive, target, { basePath: '/some/subdir' }, err => {...})
```

### status

Events:

- `error` (`err`)
- `file imported` ({ `path`, `mode=updated|created` })
- `file skipped` ({ `path` })

Properties:

- `fileCount`: The count of currently known files
- `totalSize`: Total file size in bytes

## License

MIT
