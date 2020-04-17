# Gem Text Editor

A vue package makes it easy to create and edit the best editor.

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

## Installation

You can install the npm package in save mode to save it into dependencies

```shell
npm install --save gem-text-editor
```

## Getting Started

You can start with a basic template
```html
<template>
  <GemTextEditor ref="editor" />
  <button @click="getContent()">Get content</button>
</template>

<script>
  import GemTextEditor from "gem-text-editor";
  components: {
    GemTextEditor
  },
  methods: {
    getContent() {
      let content = this.$refs.editor.getContent();
      console.log("Content: ", content);
    }
  }
</script>
```

#### Init Settings

You need to add an **init** attribute that includes the definition
```html
<template>
  <GemTextEditor ref="editor" :init="initSettings" />
</template>

<script>
  import GemTextEditor from "gem-text-editor";
  components: {
    GemTextEditor
  },
  data() {
    return {
      initSettings: {
        options: [
          "bold",
          "italic",
          "underline"
        ]
      }
    };
  },
</script>
```

#### options
You can change the position of the options to change the display
```html
"bold",
"italic",
"underline",
"justifyLeft",
"justifyCenter",
"justifyRight",
"orderedList",
"unorderedList",
"color",
"link",
"image"
```
#### minHeight
```html
200px (default)
```
#### maxHeight
```html
200px (default)
unset (If not to maxHeight, the height will automatically increase)
```


## Questions
If you have any questions, please contact via email: haicaodac@gmail.com
