
wasm-imagemagick reference API
==============================

Getting started
---------------

Let's consider the following [execute](https://github.com/KnicKnic/WASM-ImageMagick/tree/master/apidocs#execute) call:

```ts
const { outputFiles, exitCode, stderr} = await execute({
  inputFiles: [await buildInputFile('fn.png', 'image1.png')],
  commands: `
    convert image1.png -bordercolor #ffee44 -background #eeff55 +polaroid image2.png
    convert image2.png -fill #997711 -tint 55 image3.jpg
`
})
if (exitCode) {
  alert(`There was an error with the command: ${stderr.join('\n')}`)
}
else {
  await loadImageElement(outputFiles[1], document.querySelector('#outputImage'))
}
```

*   See [execute](https://github.com/KnicKnic/WASM-ImageMagick/tree/master/apidocs#execute) and [executeAndReturnOutputFile](#executeandreturnoutputfile) for information on how to execute commands. Also [ExecuteResult](interfaces/executeresult.md) and [ExecuteConfig](interfaces/executeconfig.md).
    
*   See [ExecuteCommand](#executecommand) for information about command syntaxes supported. Some utilities related to commands are [asCommand](#ascommand), [cliToArray](#clitoarray), [arrayToCli](#arraytocli)
    
*   See [MagickInputFile](interfaces/magickinputfile.md), [MagickOutputFile](interfaces/magickoutputfile.md) for information about image magick files.
    
    *   Some utilities related to files are [buildInputFile](#buildinputfile), [asInputFile](#asinputfile), [asOutputFile](#asoutputfile), [loadImageElement](#loadimageelement), [getInputFilesFromHtmlInputElement](#getinputfilesfromhtmlinputelement)
*   See [call](#call) for the low level command execution API
    
*   Some utilities related to images are: [compare](#compare), [extractInfo](#extractinfo), [getBuiltInImages](#getbuiltinimages)
    
*   Some utilities related to image and execution management are [ImageHome](interfaces/imagehome.md), [ExecutionContext](interfaces/executioncontext.md)

## Index

### Interfaces

* [CallResult](interfaces/callresult.md)
* [ExecuteConfig](interfaces/executeconfig.md)
* [ExecuteEvent](interfaces/executeevent.md)
* [ExecuteListener](interfaces/executelistener.md)
* [ExecuteResult](interfaces/executeresult.md)
* [ExecuteResultOne](interfaces/executeresultone.md)
* [ExecutionContext](interfaces/executioncontext.md)
* [ImageHome](interfaces/imagehome.md)
* [MagickFile](interfaces/magickfile.md)
* [MagickInputFile](interfaces/magickinputfile.md)
* [MagickOutputFile](interfaces/magickoutputfile.md)

### Type aliases

* [Command](#command)
* [ExecuteCommand](#executecommand)

### Variables

* [builtInImageNames](#builtinimagenames)
* [knownSupportedReadWriteImageFormats](#knownsupportedreadwriteimageformats)

### Functions

* [Call](#call)
* [addExecuteListener](#addexecutelistener)
* [arrayToCli](#arraytocli)
* [asCommand](#ascommand)
* [asExecuteConfig](#asexecuteconfig)
* [asInputFile](#asinputfile)
* [asOutputFile](#asoutputfile)
* [blobToString](#blobtostring)
* [buildImageSrc](#buildimagesrc)
* [buildInputFile](#buildinputfile)
* [call](#call)
* [cliToArray](#clitoarray)
* [compare](#compare)
* [compareNumber](#comparenumber)
* [createImageHome](#createimagehome)
* [execute](#execute)
* [executeAndReturnOutputFile](#executeandreturnoutputfile)
* [executeOne](#executeone)
* [extractInfo](#extractinfo)
* [getBuiltInImage](#getbuiltinimage)
* [getBuiltInImages](#getbuiltinimages)
* [getConfigureFolders](#getconfigurefolders)
* [getFileName](#getfilename)
* [getFileNameExtension](#getfilenameextension)
* [getInputFilesFromHtmlInputElement](#getinputfilesfromhtmlinputelement)
* [getPixelColor](#getpixelcolor)
* [isExecuteCommand](#isexecutecommand)
* [isImage](#isimage)
* [isInputFile](#isinputfile)
* [isOutputFile](#isoutputfile)
* [loadImageElement](#loadimageelement)
* [newExecutionContext](#newexecutioncontext)
* [readFileAsText](#readfileastext)

---

## Type aliases

<a id="command"></a>

###  Command

**Ƭ Command**: *( `string` &#124; `number`)[]*

*Defined in [execute.ts:7](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/execute.ts#L7)*

___
<a id="executecommand"></a>

###  ExecuteCommand

**Ƭ ExecuteCommand**: * [Command](#command)[] &#124; [Command](#command) &#124; `string`
*

*Defined in [execute.ts:49](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/execute.ts#L49)*

Commands could have the following syntaxes:

*   array form like `[['convert', 'foo.png', 'bar.gif'], ['identify', 'bar.gif']]`
*   just one array: `['convert', 'foo.png', 'bar.gif']`
*   command line strings: `['convert foo.png bar.gif', 'idenfity bar.gif']`
*   just one string: `'convert foo.png bar.gif'`

Also, for command line strings, multiple commands can be specified in the same string separating with new lines:

```js
const result = await execute(`
  convert rose: -sharpen 0x1 reconstruct.jpg
  compare rose: reconstruct.jpg difference.png
  compare -compose src rose: reconstruct.jpg difference.png
`)
```

Also, command line strings support breaking the same command in multiple lines by using `\` like in:

```js
const result = await execute(`
  convert -size 250x100 xc: +noise Random -channel R -threshold .4% \\
    -negate -channel RG -separate +channel \\
    \( +clone \) -compose multiply -flatten \\
    -virtual-pixel Tile -background Black \\
    -blur 0x.6 -motion-blur 0x15-90 -normalize \\
    +distort Polar 0 +repage 'star inward.gif'
`)
```

If you need to escape arguments like file names with spaces, use single quotes `'`, like the output file in the previous example `'star inward.gif'`

___

## Variables

<a id="builtinimagenames"></a>

### `<Const>` builtInImageNames

**● builtInImageNames**: *`string`[]* =  ['rose:', 'logo:', 'wizard:', 'granite:', 'netscape:']

*Defined in [util/imageBuiltIn.ts:5](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/imageBuiltIn.ts#L5)*

___
<a id="knownsupportedreadwriteimageformats"></a>

### `<Const>` knownSupportedReadWriteImageFormats

**● knownSupportedReadWriteImageFormats**: *`string`[]* =  [
  'jpg', 'png',
  'psd',
  'tiff', 'xcf', 'gif', 'bmp', 'tga', 'miff', 'ico', 'dcm', 'xpm', 'pcx',
  //  'pix', // gives error
  'fits',
  // 'djvu', // read only support
  'ppm',
  'pgm',
  'pfm',
  'mng',
  'hdr',
  'dds', // generated using convert -define "dds:compression={dxt1, dxt5, none}" to_rotate.png  to_rotate.dds
  'otb', // generated using convert to_rotate.png  to_rotate.otb

  'txt', // generated using convert to_rotate.png  to_rotate.txt

  // 'rgb', // fails because  MustSpecifyImageSize `to_rotate.rgb'
]

*Defined in [util/support.ts:22](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/support.ts#L22)*

list of image formats that are known to be supported by wasm-imagemagick. See `spec/formatSpec.ts`

___

## Functions

<a id="call"></a>

###  Call

▸ **Call**(inputFiles: *[MagickInputFile](interfaces/magickinputfile.md)[]*, command: *`string`[]*): `Promise`<[MagickOutputFile](interfaces/magickoutputfile.md)[]>

*Defined in [magickApi.ts:27](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/magickApi.ts#L27)*

[call](#call) shortcut that only returns the output files.

**Parameters:**

| Name | Type |
| ------ | ------ |
| inputFiles | [MagickInputFile](interfaces/magickinputfile.md)[] |
| command | `string`[] |

**Returns:** `Promise`<[MagickOutputFile](interfaces/magickoutputfile.md)[]>

___
<a id="addexecutelistener"></a>

###  addExecuteListener

▸ **addExecuteListener**(l: *[ExecuteListener](interfaces/executelistener.md)*): `void`

*Defined in [execute.ts:128](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/execute.ts#L128)*

**Parameters:**

| Name | Type |
| ------ | ------ |
| l | [ExecuteListener](interfaces/executelistener.md) |

**Returns:** `void`

___
<a id="arraytocli"></a>

###  arrayToCli

▸ **arrayToCli**(command: * [Command](#command) &#124; [Command](#command)[]*): `string`

*Defined in [util/cli.ts:36](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/cli.ts#L36)*

Generates a valid command line string from given `string[]` that is compatible with [call](#call). Works with multiple commands by separating them with new lines and support comand splitting in new lines using `\`. See [ExecuteCommand](#executecommand) for more information.

**Parameters:**

| Name | Type |
| ------ | ------ |
| command |  [Command](#command) &#124; [Command](#command)[]|

**Returns:** `string`

___
<a id="ascommand"></a>

###  asCommand

▸ **asCommand**(c: *[ExecuteCommand](#executecommand)*): [Command](#command)[]

*Defined in [util/cli.ts:99](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/cli.ts#L99)*

Makes sure that given [ExecuteCommand](#executecommand), in whatever syntax, is transformed to the form `string[][]` that is compatible with [call](#call)

**Parameters:**

| Name | Type |
| ------ | ------ |
| c | [ExecuteCommand](#executecommand) |

**Returns:** [Command](#command)[]

___
<a id="asexecuteconfig"></a>

###  asExecuteConfig

▸ **asExecuteConfig**(arg: * [ExecuteConfig](interfaces/executeconfig.md) &#124; [ExecuteCommand](#executecommand)*): [ExecuteConfig](interfaces/executeconfig.md)

*Defined in [execute.ts:91](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/execute.ts#L91)*

Transform `configOrCommand: ExecuteConfig | ExecuteCommand` to a valid ExecuteConfig object

**Parameters:**

| Name | Type |
| ------ | ------ |
| arg |  [ExecuteConfig](interfaces/executeconfig.md) &#124; [ExecuteCommand](#executecommand)|

**Returns:** [ExecuteConfig](interfaces/executeconfig.md)

___
<a id="asinputfile"></a>

###  asInputFile

▸ **asInputFile**(f: *[MagickFile](interfaces/magickfile.md)*, name?: *`string`*): `Promise`<[MagickInputFile](interfaces/magickinputfile.md)>

*Defined in [util/file.ts:82](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/file.ts#L82)*

**Parameters:**

| Name | Type | Default value |
| ------ | ------ | ------ |
| f | [MagickFile](interfaces/magickfile.md) | - |
| `Default value` name | `string` |  f.name |

**Returns:** `Promise`<[MagickInputFile](interfaces/magickinputfile.md)>

___
<a id="asoutputfile"></a>

###  asOutputFile

▸ **asOutputFile**(f: *[MagickFile](interfaces/magickfile.md)*, name?: *`string`*): `Promise`<[MagickOutputFile](interfaces/magickoutputfile.md)>

*Defined in [util/file.ts:94](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/file.ts#L94)*

**Parameters:**

| Name | Type | Default value |
| ------ | ------ | ------ |
| f | [MagickFile](interfaces/magickfile.md) | - |
| `Default value` name | `string` |  f.name |

**Returns:** `Promise`<[MagickOutputFile](interfaces/magickoutputfile.md)>

___
<a id="blobtostring"></a>

###  blobToString

▸ **blobToString**(blb: *`Blob`*): `Promise`<`string`>

*Defined in [util/file.ts:15](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/file.ts#L15)*

**Parameters:**

| Name | Type |
| ------ | ------ |
| blb | `Blob` |

**Returns:** `Promise`<`string`>

___
<a id="buildimagesrc"></a>

###  buildImageSrc

▸ **buildImageSrc**(image: *[MagickFile](interfaces/magickfile.md)*, forceBrowserSupport?: *`boolean`*): `Promise`<`string`>

*Defined in [util/html.ts:21](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/html.ts#L21)*

Return a string with the inline image content, suitable to be used to assign to an html img src attribute. See [loadImageElement](#loadimageelement).

**Parameters:**

| Name | Type | Default value | Description |
| ------ | ------ | ------ | ------ |
| image | [MagickFile](interfaces/magickfile.md) | - |
| `Default value` forceBrowserSupport | `boolean` | false |  if true and the image extension is not supported by browsers, it will convert the image to png and return that src so it can be shown in browsers |

**Returns:** `Promise`<`string`>

___
<a id="buildinputfile"></a>

###  buildInputFile

▸ **buildInputFile**(url: *`string`*, name?: *`string`*): `Promise`<[MagickInputFile](interfaces/magickinputfile.md)>

*Defined in [util/file.ts:57](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/file.ts#L57)*

Builds a new [MagickInputFile](interfaces/magickinputfile.md) by fetching the content of given url and optionally naming the file using given name or extracting the file name from the url otherwise.

**Parameters:**

| Name | Type | Default value |
| ------ | ------ | ------ |
| url | `string` | - |
| `Default value` name | `string` |  getFileName(url) |

**Returns:** `Promise`<[MagickInputFile](interfaces/magickinputfile.md)>

___
<a id="call"></a>

###  call

▸ **call**(inputFiles: *[MagickInputFile](interfaces/magickinputfile.md)[]*, command: *`string`[]*): `Promise`<[CallResult](interfaces/callresult.md)>

*Defined in [magickApi.ts:58](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/magickApi.ts#L58)*

Low level execution function. All the other functions like [execute](https://github.com/KnicKnic/WASM-ImageMagick/tree/master/apidocs#execute) ends up calling this one. It accept only one command and only in the form of array of strings.

**Parameters:**

| Name | Type |
| ------ | ------ |
| inputFiles | [MagickInputFile](interfaces/magickinputfile.md)[] |
| command | `string`[] |

**Returns:** `Promise`<[CallResult](interfaces/callresult.md)>

___
<a id="clitoarray"></a>

###  cliToArray

▸ **cliToArray**(cliCommand: *`string`*): [Command](#command)[]

*Defined in [util/cli.ts:76](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/cli.ts#L76)*

Generates a command in the form of `string[][]` that is compatible with [call](#call) from given command line string. This works for strings containing multiple commands in different lines. and also respect `\` character for continue the same command in a new line. See [ExecuteCommand](#executecommand) for more information.

**Parameters:**

| Name | Type |
| ------ | ------ |
| cliCommand | `string` |

**Returns:** [Command](#command)[]

___
<a id="compare"></a>

###  compare

▸ **compare**(img1: * [MagickFile](interfaces/magickfile.md) &#124; `string`*, img2: * [MagickFile](interfaces/magickfile.md) &#124; `string`*, fuzz?: *`number`*): `Promise`<`boolean`>

*Defined in [util/imageCompare.ts:6](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/imageCompare.ts#L6)*

Compare the two images and return true if they are equal visually. Optionally, a margin of error can be provided using `fuzz`

**Parameters:**

| Name | Type | Default value |
| ------ | ------ | ------ |
| img1 |  [MagickFile](interfaces/magickfile.md) &#124; `string`| - |
| img2 |  [MagickFile](interfaces/magickfile.md) &#124; `string`| - |
| `Default value` fuzz | `number` | 0.015 |

**Returns:** `Promise`<`boolean`>

___
<a id="comparenumber"></a>

###  compareNumber

▸ **compareNumber**(img1: * [MagickFile](interfaces/magickfile.md) &#124; `string`*, img2: * [MagickFile](interfaces/magickfile.md) &#124; `string`*): `Promise`<`number`>

*Defined in [util/imageCompare.ts:11](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/imageCompare.ts#L11)*

**Parameters:**

| Name | Type |
| ------ | ------ |
| img1 |  [MagickFile](interfaces/magickfile.md) &#124; `string`|
| img2 |  [MagickFile](interfaces/magickfile.md) &#124; `string`|

**Returns:** `Promise`<`number`>

___
<a id="createimagehome"></a>

###  createImageHome

▸ **createImageHome**(): `ImageHomeImpl`

*Defined in [imageHome.ts:81](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/imageHome.ts#L81)*

**Returns:** `ImageHomeImpl`

___
<a id="execute"></a>

###  execute

▸ **execute**(configOrCommand: * [ExecuteConfig](interfaces/executeconfig.md) &#124; [ExecuteCommand](#executecommand)*): `Promise`<[ExecuteResult](interfaces/executeresult.md)>

*Defined in [execute.ts:162](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/execute.ts#L162)*

Execute all commands in given config serially in order. Output files from a command become available as input files in next commands. In the following example we execute two commands. Notice how the second one uses `image2.png` which was the output file of the first one:

```ts
const { outputFiles, exitCode, stderr} = await execute({
  inputFiles: [await buildInputFile('fn.png', 'image1.png')],
  commands: `
    convert image1.png -bordercolor #ffee44 -background #eeff55 +polaroid image2.png
    convert image2.png -fill #997711 -tint 55 image3.jpg
`
})
if (exitCode) {
  alert(`There was an error with the command: ${stderr.join('\n')}`)
}
else {
  await loadImageElement(outputFiles.find(f => f.name==='image3.jpg'), document.getElementById('outputImage'))
}
```

See [ExecuteCommand](#executecommand) for different command syntax supported.

See [ExecuteResult](interfaces/executeresult.md) for details on the object returned

**Parameters:**

| Name | Type |
| ------ | ------ |
| configOrCommand |  [ExecuteConfig](interfaces/executeconfig.md) &#124; [ExecuteCommand](#executecommand)|

**Returns:** `Promise`<[ExecuteResult](interfaces/executeresult.md)>

___
<a id="executeandreturnoutputfile"></a>

###  executeAndReturnOutputFile

▸ **executeAndReturnOutputFile**(configOrCommand: * [ExecuteConfig](interfaces/executeconfig.md) &#124; [ExecuteCommand](#executecommand)*, outputFileName?: *`string`*): `Promise`< [MagickOutputFile](interfaces/magickoutputfile.md) &#124; `undefined`>

*Defined in [execute.ts:107](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/execute.ts#L107)*

`execute()` shortcut that useful for commands that return only one output file or when only one particular output file is relevant.

**Parameters:**

| Name | Type | Description |
| ------ | ------ | ------ |
| configOrCommand |  [ExecuteConfig](interfaces/executeconfig.md) &#124; [ExecuteCommand](#executecommand)|
| `Optional` outputFileName | `string` |  optionally user can give the desired output file name |

**Returns:** `Promise`< [MagickOutputFile](interfaces/magickoutputfile.md) &#124; `undefined`>
If `outputFileName` is given the file with that name, the first output file otherwise or undefined
if no file match, or no output files where generated (like in an error).

___
<a id="executeone"></a>

###  executeOne

▸ **executeOne**(configOrCommand: * [ExecuteConfig](interfaces/executeconfig.md) &#124; [ExecuteCommand](#executecommand)*): `Promise`<[ExecuteResultOne](interfaces/executeresultone.md)>

*Defined in [execute.ts:59](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/execute.ts#L59)*

Execute first command in given config.

**Parameters:**

| Name | Type |
| ------ | ------ |
| configOrCommand |  [ExecuteConfig](interfaces/executeconfig.md) &#124; [ExecuteCommand](#executecommand)|

**Returns:** `Promise`<[ExecuteResultOne](interfaces/executeresultone.md)>

___
<a id="extractinfo"></a>

###  extractInfo

▸ **extractInfo**(img: * [MagickFile](interfaces/magickfile.md) &#124; `string`*): `Promise`<`ExtractInfoResult`[]>

*Defined in [util/imageExtractInfo.ts:8](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/imageExtractInfo.ts#L8)*

Execute `convert $IMG info.json` to extract image metadata. Returns the parsed info.json file contents

**Parameters:**

| Name | Type | Description |
| ------ | ------ | ------ |
| img |  [MagickFile](interfaces/magickfile.md) &#124; `string`|  could be a string in case you want to extract information about built in images like \`rose:\` |

**Returns:** `Promise`<`ExtractInfoResult`[]>

___
<a id="getbuiltinimage"></a>

###  getBuiltInImage

▸ **getBuiltInImage**(name: *`string`*): `Promise`<[MagickInputFile](interfaces/magickinputfile.md)>

*Defined in [util/imageBuiltIn.ts:25](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/imageBuiltIn.ts#L25)*

shortcut of [getBuiltInImages](#getbuiltinimages) to get a single image by name

**Parameters:**

| Name | Type |
| ------ | ------ |
| name | `string` |

**Returns:** `Promise`<[MagickInputFile](interfaces/magickinputfile.md)>

___
<a id="getbuiltinimages"></a>

###  getBuiltInImages

▸ **getBuiltInImages**(): `Promise`<[MagickInputFile](interfaces/magickinputfile.md)[]>

*Defined in [util/imageBuiltIn.ts:10](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/imageBuiltIn.ts#L10)*

Gets ImageMagick built-in images like `rose:`, `logo:`, etc in the form of [MagickInputFile](interfaces/magickinputfile.md)s

**Returns:** `Promise`<[MagickInputFile](interfaces/magickinputfile.md)[]>

___
<a id="getconfigurefolders"></a>

###  getConfigureFolders

▸ **getConfigureFolders**(): `Promise`<`string`[]>

*Defined in [util/support.ts:3](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/support.ts#L3)*

**Returns:** `Promise`<`string`[]>

___
<a id="getfilename"></a>

###  getFileName

▸ **getFileName**(url: *`string`*): `string`

*Defined in [util/file.ts:106](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/file.ts#L106)*

**Parameters:**

| Name | Type |
| ------ | ------ |
| url | `string` |

**Returns:** `string`

___
<a id="getfilenameextension"></a>

###  getFileNameExtension

▸ **getFileNameExtension**(filePathOrUrl: *`string`*): `string`

*Defined in [util/file.ts:118](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/file.ts#L118)*

**Parameters:**

| Name | Type |
| ------ | ------ |
| filePathOrUrl | `string` |

**Returns:** `string`

___
<a id="getinputfilesfromhtmlinputelement"></a>

###  getInputFilesFromHtmlInputElement

▸ **getInputFilesFromHtmlInputElement**(el: *`HTMLInputElement`*): `Promise`<[MagickInputFile](interfaces/magickinputfile.md)[]>

*Defined in [util/html.ts:35](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/html.ts#L35)*

Build `MagickInputFile[]` from given HTMLInputElement of type=file that user may used to select several files

**Parameters:**

| Name | Type |
| ------ | ------ |
| el | `HTMLInputElement` |

**Returns:** `Promise`<[MagickInputFile](interfaces/magickinputfile.md)[]>

___
<a id="getpixelcolor"></a>

###  getPixelColor

▸ **getPixelColor**(img: *[MagickFile](interfaces/magickfile.md)*, x: *`number`*, y: *`number`*): `Promise`<`string`>

*Defined in [util/image.ts:4](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/image.ts#L4)*

**Parameters:**

| Name | Type |
| ------ | ------ |
| img | [MagickFile](interfaces/magickfile.md) |
| x | `number` |
| y | `number` |

**Returns:** `Promise`<`string`>

___
<a id="isexecutecommand"></a>

###  isExecuteCommand

▸ **isExecuteCommand**(arg: *`any`*): `boolean`

*Defined in [execute.ts:84](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/execute.ts#L84)*

**Parameters:**

| Name | Type |
| ------ | ------ |
| arg | `any` |

**Returns:** `boolean`

___
<a id="isimage"></a>

###  isImage

▸ **isImage**(file: *[MagickFile](interfaces/magickfile.md)*): `Promise`<`boolean`>

*Defined in [util/file.ts:49](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/file.ts#L49)*

**Parameters:**

| Name | Type |
| ------ | ------ |
| file | [MagickFile](interfaces/magickfile.md) |

**Returns:** `Promise`<`boolean`>

___
<a id="isinputfile"></a>

###  isInputFile

▸ **isInputFile**(file: *[MagickFile](interfaces/magickfile.md)*): `boolean`

*Defined in [util/file.ts:26](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/file.ts#L26)*

**Parameters:**

| Name | Type |
| ------ | ------ |
| file | [MagickFile](interfaces/magickfile.md) |

**Returns:** `boolean`

___
<a id="isoutputfile"></a>

###  isOutputFile

▸ **isOutputFile**(file: *[MagickFile](interfaces/magickfile.md)*): `boolean`

*Defined in [util/file.ts:29](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/file.ts#L29)*

**Parameters:**

| Name | Type |
| ------ | ------ |
| file | [MagickFile](interfaces/magickfile.md) |

**Returns:** `boolean`

___
<a id="loadimageelement"></a>

###  loadImageElement

▸ **loadImageElement**(image: *[MagickFile](interfaces/magickfile.md)*, el: *`HTMLImageElement`*, forceBrowserSupport?: *`boolean`*): `Promise`<`void`>

*Defined in [util/html.ts:12](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/html.ts#L12)*

Will load given html img element src with the inline image content.

**Parameters:**

| Name | Type | Default value | Description |
| ------ | ------ | ------ | ------ |
| image | [MagickFile](interfaces/magickfile.md) | - |  the image to be loaded |
| el | `HTMLImageElement` | - |  the html image element in which to load the image |
| `Default value` forceBrowserSupport | `boolean` | false |  if true and the image extension is not supported by browsers, it will convert the image to png and return that src so it can be shown in browsers |

**Returns:** `Promise`<`void`>

___
<a id="newexecutioncontext"></a>

###  newExecutionContext

▸ **newExecutionContext**(inheritFrom?: *[ExecutionContext](interfaces/executioncontext.md)*): [ExecutionContext](interfaces/executioncontext.md)

*Defined in [executionContext.ts:88](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/executionContext.ts#L88)*

**Parameters:**

| Name | Type |
| ------ | ------ |
| `Optional` inheritFrom | [ExecutionContext](interfaces/executioncontext.md) |

**Returns:** [ExecutionContext](interfaces/executioncontext.md)

___
<a id="readfileastext"></a>

###  readFileAsText

▸ **readFileAsText**(file: *[MagickFile](interfaces/magickfile.md)*): `Promise`<`string`>

*Defined in [util/file.ts:40](https://github.com/KnicKnic/WASM-ImageMagick/blob/a45b8ff/src/util/file.ts#L40)*

Read files as string. Useful when files contains plain text like in the output file info.txt of `convert logo: -format '%[pixel:p{0,0}]' info:info.txt`

**Parameters:**

| Name | Type |
| ------ | ------ |
| file | [MagickFile](interfaces/magickfile.md) |

**Returns:** `Promise`<`string`>

___

