---
layout: post
title: Typescript frontend ecosystem
date: 2022-05-31 10:43:29 + 0800
categories: [frontend, javascript]
tag: [frontend]
---

# {{page.title}}


## required modules

| # | Name           | 备注          |
|--| ------ | --------------------------- |
| 1    | node      |   |
| 2    | git      |   |
| 3    | webpack  |   |
| 4    | rollup   | simpler than webpack, mostly used in library packaging rollup.config.js  |


### devDependiency

| Name   | 备注             |
| ------ | --------------------------- |
| typescript    | tsc   |
| jest  | transform, testRegex, npm test, jest --watch  |
| ts-jest    | jest for typescript   |
| prettier    |    |
| husky | precommit in package.json |
| lint-staged | lint check |

### dependiencies


### files and folders

/src // store all the source files

readme.md // npm init

package.json  // npm init 

.gitignore // can get default template from gitignore.io

tsconfig.json // tsc --init


## command
```js
npm init  // initialiaze the project, will create package.json

npm i -D typescript jest ts-jest @types/jest // add necessary packages

tsc --init // will create tsconfig.json

tsc -p tsconfig.json // run tsc with selected config file

npm test // in package.json ... "scripts":{"test":"jest", "test:watch": "npm test -- --watch" ...}...
```

## package.json configrurations

```json

"script":{
  "test":"jest",
  "test:watch": "npm test -- --watch",
  "build": "tsc -p tsconfig.json && rollup -c rollup.config.js",
  "precommit": "npm test && lint-staged",
}

...

"lint-staged":{
  "src/**/*.ts":[
    "prettier --write",
    "git add"
  ]
}

```