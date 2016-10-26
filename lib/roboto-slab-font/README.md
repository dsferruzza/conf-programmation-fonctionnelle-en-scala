# Roboto fontface Bower package

Roboto web font package with font files, less, scss, and CSS.

A simple bower package providing the [Roboto Slab](https://www.google.com/fonts/specimen/Roboto+Slab) fontface.

The font was created by [Christian Robertson](https://plus.google.com/110879635926653430880/about).

## Installing

Assuming you have [NodeJS](http://nodejs.org/) and [Bower](https://github.com/bower/bower) installed globally just open up a terminal, navigate to your projects root directory and then execute

## Use ##

1. Use Bower to install the roboto-slab package
  ```sh
  bower install roboto-slab-font --save
  ```

2. Import the roboto-slab.less, roboto-slab.scss or roboto-slab.css file into your project
  ```css
  @import: "../some/path/roboto-slab-font/roboto-slab.css";
  ```

  ```less
  @import: "../some/path/roboto-slab-font/roboto-slab.less";
  ```

  ```scss
  @import: "../some/path/roboto-slab-font/roboto-slab";
  ```

3. Override the Roboto-slab font path if nessesary

  ```less
  @roboto-slab-font-path: '../font';
  ```

  ```scss
  $roboto-slab-font-path: '../font';
  ```
