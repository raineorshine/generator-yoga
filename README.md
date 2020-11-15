# yogini
[![npm version](https://img.shields.io/npm/v/yogini.svg)](https://npmjs.org/package/yogini)

**yogini** is a prompt-driven scaffolding system. It makes it easy to create and maintain personal boilerplates that evolve over time.

What makes **yogini** different?

- Prompt-driven (via [Inquirer](https://github.com/SBoudrias/Inquirer.js)) with [easy prompt configuration](https://github.com/raineorshine/yogini/blob/master/app/yogini.json)
- Embedded file-copying logic, e.g. `{useSass}main.scss`
- Templating with [striate](https://github.com/raineorshine/striate), a superset of [ejs](https://github.com/mde/ejs) with better handling of multi-line blocks

Generators created by yogini are [yeoman](http://yeoman.io/) generators, so they can be published and consumed them like any other yeoman generator. You do not need to understand yeoman to use yogini.

## Usage

### 1. Create your generator

**yogini** requires npm, which comes with [node](https://nodejs.org/en/download/).

Your initial generator is itself generated by yogini. You only need to do this once.

```sh
$ npm install -g yo                 # install yeoman
$ npm install -g yogini             # install yogini
$ mkdir generator-mygen             # create a directory for your new generator
$ cd generator-mygen            
$ yo yogini                         # generate your personal generator
$ npm link                          # alias your generator to your globally
                                    # installed npm modules so that you can run
                                    # it with yeoman.
```

### 2. Generate projects to your heart's content

Yay! Now you can run `yo mygen` in an empty directory to generate a new project.

```sh
$ yo mygen
? Does your project use Javascript? Yes
? Does your project use css? Yes
...
```

An initial **yogini** generator just consists of a blank README, so you have to customize it to generate something useful.

#### Customizing your generator

You can customize your **yogini** generator without writing any generator code:

- Drop files into `app/templates`. All files from this directory will be copied into your project directory when you run the generator.
- Edit the [Inquirer](https://github.com/SBoudrias/Inquirer.js) prompts in `app/yogini.json`. These will determine which files get copied (via [prefixnotes](https://github.com/raineorshine/prefixnote)) and what code gets copied (via [striate](https://github.com/raineorshine/striate)).

For example, if you have a prompt in your yogini.json that inquires about Javascript being included in the project:

```js
{
  prompts: [{
    "type": "confirm",
    "name": "js",
    "message": "Does your project use Javascript?",
    "store": true
  }]
}
```

...and you have a file in `app/templates` which specifies the prompt `name` in a [prefixnote](https://github.com/raineorshine/prefixnote):

```
.
└── {js}main.js
```

...then when you run the generator, you will be prompted with `Does your project use Javascript?`. If you choose yes, `main.js` will be copied into the project directory. If you choose no, it will not be copied.

Check out [generator-yogini-sample](https://github.com/raineorshine/generator-yogini-sample) for an example yogini generator.

## More detailed usage

[Inquirer](https://github.com/SBoudrias/Inquirer.js) prompts drive everything in a yogini generator.

Sample yogini.json file:

```js
{
  "prompts": [
    {
      "type": "confirm",
      "name": "js",
      "message": "Does your project use Javascript?",
      "store": true
    },
    {
      "type": "confirm",
      "name": "css",
      "message": "Does your project use css?",
      "store": true
    }
  }
}
```

The above yogini.json file would prompt you with two questions every time you run your generator and store them in `js` and `css` variables. These variables drive the main two aspects of scaffolding: file copying and templating.

### 1. File Copying

You can control which files in `/app/templates` get copied into your new project by prefixing filenames with expressions that include prompt variables.

```
.
├── index.html
├── {js}scripts
│   └── main.js
└── {css}styles
    └── main.css
```

In the above example, the scripts folder will only be copied if `js` (as declared in yogini.json) is true, and the `styles` folder will only be copied if `css` is true.

Some notes about file copying:

- Empty expressions are a great way to include system and hidden files in your templates folder without them having an effect until they are copied:
  - `{}package.json`
  - `{}.gitignore`
- If a folder name only consists of an expression, all files will be copied to the parent folder:

  ```
  main.js
  {js}
    ├── 1.js
    ├── 2.js
    └── 3.js
  ```

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;⇨

  ```
  main.js
  1.js
  2.js
  3.js
  ```

- Expressions can be any boolean Javascript statement:

  ```
  {js && gulp}gulpfile.js
  ```

See [prefixnote](https://github.com/raineorshine/prefixnote) for the nitty-gritty.


### 2. Templating

You can use [striate](https://github.com/raineorshine/striate), a superset of [ejs](https://github.com/mde/ejs), to control which code gets generated within the files. The answers given to the prompts in yogini.json are available as variables within the scope of your template files.

```html
<!DOCTYPE html>
<html>
<head>
  >> if(css) {
  <link rel='Stylesheet' type='text/css' href='styles/main.css'>
  >> }
</head>
<body>
  >> if(js) {
  <script src='scripts/main.js'></script>
  >> }
</body>
</html>
```

You can see a complete yogini generator with prompts, file prefixes, and templating at [generator-yogini-sample](https://github.com/raineorshine/generator-yogini-sample).

## License

ISC © [Raine Revere](https://github.com/raineorshine)
