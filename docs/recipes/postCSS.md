# Setting up postCSS

This is an easy way to set up postCSS, including integration with `watch` and LiveReload.


## Steps

### 1. Install postCSS:

Install [postCSS](https://github.com/postcss/gulp-postcss) and some plugins, let's start with replacing [minifyCSS](https://github.com/murphydanger/gulp-minify-css) with [cssnano](https://github.com/ben-eb/cssnano) and [autoprefixer](https://github.com/sindresorhus/gulp-autoprefixer) with [autoprefixer core](https://github.com/postcss/autoprefixer-core):

```sh
$ npm install --save-dev gulp-postCSS autoprefixer-core cssnano
$ npm uninstall --save-dev gulp-autoprefixer gulp-minify-css
```

### 3. Replace gulp tasks with postCSS tasks

##### Add list of postCSS processors:

```diff
 const $ = gulpLoadPlugins();
 const reload = browserSync.reload;
+
+const processors = [
+  require('autoprefixer-core')({ browsers: ['last 1 version'] }),
+  require('cssnano')
+];

gulp.task('styles', () => {
```

##### Remove autoprefixer from the 'styles' task:

```diff
 gulp.task('styles', () => {
   return gulp.src('app/styles/*.scss')
     .pipe($.plumber())
     .pipe($.sourcemaps.init())
-    .pipe($.autoprefixer({browsers: ['last 1 version']}))
     .pipe($.sourcemaps.write())
```

##### Replace minifyCSS with postCSS in 'html' task:

```diff
 gulp.task('html', ['styles'], () => {
   const assets = $.useref.assets({searchPath: ['.tmp', 'app', '.']});

   return gulp.src('app/*.html')
     .pipe(assets)
     .pipe($.if('*.js', $.uglify()))
-    .pipe($.if('*.css', $.minifyCss({compatibility: '*'})))
+    .pipe($.if('*.css', $.postcss(processors)))
     .pipe(assets.restore())
```

### 4. Check it's working

Verify that `gulp build` and `gulp serve` work correctly. While `gulp serve` is running you should be able to edit your stylesheet(s) and see the styles dynamically update in your browser.


## Extras

### Add some more useful [postCSS plugins](https://github.com/postcss/postcss#packs)

There is lots of awesome plugins to check out, like [precss](https://github.com/jonathantneal/precss),
[stylelint](https://github.com/stylelint/stylelint), and [css mqpacker](https://github.com/hail2u/node-css-mqpacker)
