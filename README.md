# Getting Started with TypeScript

Practicing Typescript with the Toy Robot exercise.

## How to run this project?

Please, use Node.js v6 and install `yarn` as package manager.

```shell
$ yarn
$ yarn start
```

**For development**, run the following commands in separate shells.

```shell
$ yarn watch
```

```shell
$ yarn watch:launch
```

```shell
$ yarn watch:test
```

**Coverage report**

```shell
$ yarn test:coverage
```

## Ideal development environment

Great scaffolder: https://github.com/ospatil/generator-node-typescript

### Install packages

Globals
```
$ yarn global add tslint typescript
```

Dev packages

```
$ yarn add --dev tslint mocha chai typescript typings ts-node rimraf sinon-chai
```

Typing packages
```
$ yarn add --dev @types/chai @types/mocha @types/node @types/sinon-chai
```

### Setup `tsconfig.json`

```json
{
  "compilerOptions": {
    "declaration": true,
    "outDir": "./lib",
    "sourceMap": true,
    "noImplicitAny": true,
    "module": "commonjs",
    "moduleResolution": "node"
  },
  "include": [
    "src/**/*"
  ],
  "compileOnSave": true
}
```

* The `exclude` property only affects the files included via the `include` property.

### Setup `tslint.json`

```json
{
  "extends": "tslint:latest",
  "rules": {
    "curly": false,
    "quotemark": [true, "single", "avoid-escape"]
  }
}
```

### Notes for type declaration

* Doesn't need `typings`, because TypeScript 2 manages it automatically, however we have to install declaration file as node modules.
* Search on: https://microsoft.github.io/TypeSearch/, for example search for `sinon-chai`.

### Setup `.vscode` shorthands

`.vscode/settings.json` - VS Code will use the installed TypeScript version
```json
{
  "typescript.tsdk": "./node_modules/typescript/lib"
}
```

`.vscode/tasks.json`
```json
{
  "version": "0.1.0",
  "command": "npm",
  "isShellCommand": true,
  "args": ["run"],
  "showOutput": "always",
  "tasks": [{
      "taskName": "build",
      "isBuildCommand": true
    },
    {
      "taskName": "clean"
    },
    {
      "taskName": "lint"
    },
    {
      "taskName": "test",
      "isTestCommand": true
    }
  ]
}
```

### Other setup

* `.editorconfig` 
* `.jsbeautifyrc`

### Add script to package.json

Reading: `npm help scripts`

```
$ yarn run clean
$ yarn lint
$ yarn build
$ yarn test
$ yarn watch
$ yarn watch:test
$ yarn watch:launch
```

In `package.json`:

```json
"scripts": {
    "clean": "rimraf lib .nyc-output coverage",
    "lint": "tslint --force --fix \"src/**/*.ts\" \"test/**/*.ts\"",
    "prebuild": "yarn lint",
    "build": "yarn run clean && echo Using TypeScript && tsc --version && tsc --pretty",
    "pretest": "yarn build",
    "test": "mocha",
    "testOnly": "mocha",
    "test:coverage": "nyc yarn test && open coverage/index.html",
    "prepublish": "yarn test",
    "watch": "yarn build -- --watch",
    "watch:test": "yarn test -- --watch",
    "watch:launch": "nodemon lib/main",
    "prestart": "yarn build",
    "start": "node ./lib/main"
  },
```

We can install as a command line line tool: `npm install -g .`

### Relaunch application after code change

```sh
$ yarn global install nodemon

$ nodemon lib/main
```

### Test with mocha

* Mocha default test folder is `./test`
* Settings can be saved in `mocha.opts`, so a simple `mocha` command can run all the test
* Debug mocha:

```json
// launch.json
    {
      "name": "Run mocha test",
      "type": "node2",
      "request": "launch",
      "program": "${workspaceRoot}/node_modules/.bin/_mocha",
      "cwd": "${workspaceRoot}"
    }
```

* `Chai` is a simple expectation library [web](http://chaijs.com/guide/styles/#expect)

### Code coverage

https://github.com/istanbuljs/nyc

```sh
$ yarn add --dev nyc
```

.nycrc
```json
{
  "include": [
    "src/**/*.ts"
  ],
  "exclude": [
    "typings",
    "@types",
    "node_modules",
    "coverage",
    ".nyc_output",
    "lib",
    "test/**/*.ts"
  ],
  "extension": [
    ".ts"
  ],
  "require": [
    "ts-node/register"
  ],
  "reporter": [
    "json",
    "html"
  ],
  "all": true
}
```

# Toy Robot Simulator

[Original example](https://github.com/DiscoverRedwolf/toyrobot)

- A toy robot moving on a square tabletop.
- Table top dimensions: 5 units x 5 units.
- Robot cannot move out from the tabletop.

Commands:

```
PLACE X,Y,F
PLACE will put the toy robot on the table in position X,Y and facing NORTH, SOUTH, EAST or WEST.
The origin (0,0) can be considered to be the SOUTH WEST most corner.
The first valid command to the robot is a PLACE command, after that, any sequence of commands may be issued, 
in any order, including another PLACE command. The application should discard all commands in the sequence until 
a valid PLACE command has been executed.
```
```
MOVE
MOVE will move the toy robot one unit forward in the direction it is currently facing.
```
```
LEFT, RIGHT
LEFT and RIGHT will rotate the robot 90 degrees in the specified direction without changing the position of the robot.
```
```
REPORT
REPORT will announce the X,Y and F of the robot. This can be in any form, but standard output is sufficient.
```
Restrictions:
* A robot that is not on the table can choose the ignore the MOVE, LEFT, RIGHT and REPORT commands.
* Input can be from a file, or from standard input.
* The toy robot must not fall off the table during movement. This also includes the initial placement of the toy robot. Any move that would cause the robot to fall must be ignored.

Example Input and Output:
* `PLACE 0,0,NORTH MOVE REPORT` Output: `0,1,NORTH`
* `PLACE 0,0,NORTH LEFT REPORT` Output: `0,0,WEST`
* `PLACE 1,2,EAST MOVE MOVE LEFT MOVE REPORT` Output: `3,3,NORTH`

## Notes

* Add lodash to the project
* Useful lodash methods: flattenDeep, sum, fill
* Add nice table presentation in console: [cli-table]()https://github.com/jamestalmage/cli-table2)

Dealing with non-typed packages:

* https://github.com/Microsoft/TypeScript/issues/9184
* Create a `@types` folder for type definitions
* Extend `tsconfig.json` with `"typeRoots": ["@types"]` to `compilerOptions`

```
declare class SomeClass {
  constructor(options?: Object);
}

declare module 'module_name' {
  export = SomeClass
}
```

### Robot

Requirements:
* Robot exists (creating an instance)
* Robot can turn LEFT, RIGHT (actions)
* Robot instance stores the direction where its facing (state holder)

Implementation notes:
* Using interface for options
* Using enums for constans
* Has to `export` them, so they can be used during initialization
* Flexible interface: all params with `?`
* Constructor with an empty object, so initialization can be done without any params
* Merging passed parameters with the default values in constructor

### Debug 

Mocha with node --inspector
```sh
$ node --inspect --debug-brk node_modules/.bin/_mocha --watch
```

Compiled lib/main.js
```sh
$ node --inspect --debug-brk lib/main
```

### Moving robot

* Where should store the actual position coordinates of a robot? Robot responsibility is direction and turning left/right only.
* In our simplified example, Table class can store the state of a robot, however the best option if we have a Traffic manager, a controller, which can connect together our table with our robot... maybe later we would like to add more robot to our program.
* Table instance can store: which coordinate is not empty and who is on that position. (out of scope)
 
### TTY and Readline notes

```js
import * as readline from 'readline';

// Move the cursor top-left corner
readline.cursorTo(process.stdout, 0, 0)

// We can watch each keypress
readline.emitKeypressEvents(process.stdin);

// Clear the whole console
readline.clearScreenDown(process.stdout);

// Watching resize
process.stout.on('resize', () => {
      console.log(`${process.stdout.columns}x${process.stdout.rows}`);
})

// Watching keystrokes
/* key: {
 *   sequence: '',
 *   name: '',
 *   ctrl: false,
 *   meta: false,
 *   shift: false
 * }
 */
process.stdin.on('keypress', (str, key) => {
  if (key.ctrl && key.name === 'c') {
    process.exit();
  }
  
})
```
TTY console exists only when the app run natively with node command. Using `nodemon` or `forever` run the app as a separate process, the console is not TTY anymore, so watching `keypress` is not a solution.

Changing the requirement: using standard readline for sending commands, so there will be always an Enter at the end.
(Secondary option to control with keystrokes... Interesting package: https://github.com/SBoudrias/Inquirer.js)

### Creating a ScreenWriter class for managing drawing

- [x] should be a singleton
- [x] should write out given content
- [x] should clear the page

Useful readings: https://www.joyent.com/node-js/production

### Notes about Streams

Important: https://nodejs.org/api/stream.html#stream_types_of_streams

Setup a stream from a `ChildProcess`:
```js
const mainApp: ChildProcess = childProcess.execFile('node', ['lib/main.js']);
```

Watch and read console content:
```js
mainApp.stdout.on('data', (data) => {
  content = content + data;
});
```

Watch end event:
```js
mainApp.stdout.on('end', () => {
  const aRow: string = '│  │  │  │  │  │';
  expect(content).to.contains(aRow);
  done();
});
```

* Watching `data` and `end` is an old way to manage Streams.
* New way: `pipe` it or watch `readable` event and deal with the Buffer.

Send a `ctrl-c` keystroke to the app:
```js
mainApp.stdin.write('\x03');
```

**Mocking stdout.write stream**

* https://www.npmjs.com/package/stdio-mock
* https://github.com/TylorS/stdio-mock

We can create a dummy Writable stream as a mock, overwrite the original process.stdout.write with this mock and we can watch the data on this mocked stream. We can test the diverted content.

Finally, after refactoring ScreenWriter a little bit, actually adding an optional parameter to the constructor, we can inject the writable stream, so we can directly test on that injected stream.
 
### Controller

Need a place where we can merge together robots with table, etc...

Let's use the `main` method first and we can refactor later.

stdin https://github.com/sindresorhus/get-stdin/blob/master/index.js

### Notes about Promise

Option 1:
Use native Promise if target is "es6"

Option 2:
Add @types/es6-promise if target is "es5"

### Command Line parser

1. Loop until quit command
2. Do what the command say

#TODO

- [x] Create a Table top class
- [x] Create a Robot class
- [x] Controller connect together Table with Robot
- [x] Command line parser

Probably a better option if the robot knows about its position
