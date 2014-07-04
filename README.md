## Sails.js setup with Sass and Jade

#### Setup

```
=> sudo npm -g install sails    
``` 
Create a new Sails application. Here, we're specifying Jade as the templating langauage and adding a `--linker` flag so we can use the asset injection, minfication, and concatentation that's provided by Sails.

```
=> sails new testProject --template=jade --linker
```

Move into your new Sails project and install Jade

```
=> cd testProject
=> npm install jade --save
```

Now you'll need to edit your `layout.jade` file.

```
=> vi views/layout.jade
```
Within that file, you should see something similar to this.

```
!!!
head
  title= title
  meta(name="viewport",content="width=dive-width, initial-scale=1, maximum-scale=1")
  script(src="/js/sails.io.js") // Remove this line
body
  block body
```
Apply the changes shown below and save your file.

```
doctype
head
  title= title
  meta(name="viewport",content="width=dive-width, initial-scale=1, maximum-scale=1")
  // SCRIPTS
  // SCRIPTS END
  // STYLES
  // STYLES END
body
  block body
```

Jade is now configured to work with your application. Now to get Sass working.

First, install Sass.


```
=> npm install grunt-contrib-sass --save
```

Load the Sass plugin in `Gruntfile.js`.

```
var depsPath = grunt.option('gdsrc') || 'node_modules/sails/node_modules';
grunt.loadTasks(depsPath + '/grunt-contrib-clean/tasks');
grunt.loadTasks(depsPath + '/grunt-contrib-copy/tasks');
grunt.loadTasks(depsPath + '/grunt-contrib-concat/tasks');
grunt.loadTasks(depsPath + '/grunt-sails-linker/tasks');
grunt.loadTasks(depsPath + '/grunt-contrib-jst/tasks');
grunt.loadTasks(depsPath + '/grunt-contrib-watch/tasks');
grunt.loadTasks(depsPath + '/grunt-contrib-uglify/tasks');
grunt.loadTasks(depsPath + '/grunt-contrib-cssmin/tasks');
grunt.loadTasks(depsPath + '/grunt-contrib-less/tasks');
grunt.loadTasks(depsPath + '/grunt-contrib-coffee/tasks');
grunt.loadTasks('node_modules/grunt-contrib-sass/tasks'); // ADD THIS LINE
```

Now add the `Sass:Dev` task.

```
sass: {
  dev: {
    options: {
      style: 'expanded' 
    },
    files: [
      {
      expand: true,
      cwd: 'assets/styles/',
      src: ['*.scss', '*.sass'],
      dest: '.tmp/public/styles/',
      ext: '.css'
    }, {
      expand: true,
      cwd: 'assets/linker/styles/',
      src: ['*.scss', '*.sass'],
      dest: '.tmp/public/linker/styles/',
      ext: '.css'
    }
    ]
  }
},
```
Add the `compileAssets` task.

```
grunt.registerTask('compileAssets', [
  'clean:dev',
  'jst:dev',
  'less:dev',
  'sass:dev',  // ADD THIS LINE
  'copy:dev',`
  'coffee:dev'
]);
```
And finally the `prod` task.

```
grunt.registerTask('prod', [
  'clean:dev',
  'jst:dev',
  'less:dev',
  'sass:dev', // ADD THIS LINE
  'copy:dev',
  'coffee:dev',
  'concat',
  'uglify',
  'cssmin',
  'sails-linker:prodJs',
  'sails-linker:prodStyles',
  'sails-linker:devTpl',
  'sails-linker:prodJsJADE',
  'sails-linker:prodStylesJADE',
  'sails-linker:devTplJADE'
]);
```
Now, create a new .scss file to test it all.

```
=> touch assets/linker/styles/base.scss
```
Open `views/home/index.jade` and add the styling beneath `style .` to your new Sass file at `assets/linker/styles/base.scss`.


After saving those changes run `sails lift` and you should see a styled "Getting started" page for Sails.js.
