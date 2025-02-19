# Gulp

下面是经过格式化、整理后的内容，并对部分内容进行了更新和补充说明：

***

### **Gulp 简介**

Gulp 是一个基于任务的构建工具，通过定义任务（task）来组织开发过程。它使用流（stream）机制来处理文件，核心概念包括获取文件流、处理文件流、写入文件流等。

***

### **gulp.src()**

* **功能**：\
  用于获取文件流，但获取到的不是原始文件，而是包含文件路径、文件名、内容等信息的虚拟文件对象（Vinyl 文件）。
*   **语法**：

    ```javascript
    gulp.src(globs[, options])
    ```

    * **globs**：文件匹配模式，可以是一个字符串或字符串数组，用于匹配文件路径。
    * **options**：可选参数，通常情况下可忽略。
* **文件匹配**：\
  Gulp 内部使用 node-glob 模块实现文件匹配，支持以下特殊字符：
  * `*`：匹配路径中 0 个或多个字符，但不会匹配路径分隔符（除非路径分隔符出现在末尾）。
  * `**`：匹配路径中的 0 个或多个目录及其子目录；当单独出现时可匹配任意深度的目录。
  * `?`：匹配单个字符（不匹配路径分隔符）。
  * `[ ... ]`：匹配括号内任一字符；若第一个字符为 `^` 或 `!`，则表示不匹配括号中的字符。
  * `!(pattern|pattern|pattern)`：匹配不符合括号中任一模式的文件。
  * `?(pattern|pattern|pattern)`：匹配括号中任一模式 0 次或 1 次。
  * `+(pattern|pattern|pattern)`：匹配括号中任一模式至少 1 次。
  * `*(pattern|pattern|pattern)`：匹配括号中任一模式 0 次或多次。
  * `@(pattern|pattern|pattern)`：匹配括号中任一模式恰好 1 次。
*   **示例**\
    使用数组匹配多种文件类型，并排除特定文件：

    ```javascript
    // 匹配所有 js 文件，但排除以 b 开头的 js 文件
    gulp.src(['*.js', '!b*.js']);

    // 注意：排除模式不能出现在数组的第一个元素中，否则不会生效
    gulp.src(['!b*.js', '*.js']); // 这种写法不会排除任何文件
    ```

***

### **gulp.dest()**

* **功能**：\
  将处理后的文件流写入到指定目录中。
*   **语法**：

    ```javascript
    gulp.dest(path[, options])
    ```

    * **path**：写入文件的目标目录（注意不能直接指定文件名）。
    * **options**：可选参数，通常情况下不需要特殊配置。
* **文件路径生成规则**：\
  gulp.dest() 会根据 gulp.src() 中匹配的文件与其 base 路径生成最终的输出路径。
  * 默认情况下，base 值为通配符出现之前的那部分路径。
  *   举例：

      ```javascript
      gulp.src('app/src/**/*.css')
        .pipe(gulp.dest('dist'));
      // 若匹配到文件 app/src/css/normal.css，
      // 最终生成的文件路径为 dist/css/normal.css
      ```
  * 通过配置 gulp.src() 的 `base` 选项，可以灵活控制生成文件的目录结构。
* **注意**：\
  即使在 gulp.dest() 中指定了文件名，该方法也只会将其当做目录名使用，生成的文件名仍取决于 gulp.src() 中获取的文件名。

***

### **gulp.task()**

* **功能**：\
  用于定义构建任务。
*   **语法**：

    ```javascript
    gulp.task(name[, deps], fn)
    ```

    * **name**：任务名称。
    * **deps**：可选，任务依赖数组，任务将在所有依赖任务执行完后执行。
    * **fn**：任务执行函数，可以是同步或异步函数。
*   **控制任务执行顺序**：\
    在 Gulp 4 中，建议使用 `gulp.series` 和 `gulp.parallel` 来组织任务依赖和顺序，而不再使用 Gulp 3 的依赖数组写法。例如：

    ```javascript
    const { series, parallel } = require('gulp');

    function taskA(cb) {
      // 异步操作
      setTimeout(() => {
        console.log('Task A is done');
        cb();
      }, 5000);
    }

    function taskB(cb) {
      console.log('Task B is done');
      cb();
    }

    // 按顺序执行 taskA 和 taskB
    gulp.task('my-tasks', series(taskA, taskB));
    ```
* **异步任务处理方式**：
  * 通过任务函数中的回调（如 `cb()`）
  * 返回流对象（适用于文件处理任务）
  * 返回 Promise 对象（例如使用 Q 库或原生 Promise）

***

### **gulp.watch()**

* **功能**：\
  用于监视文件变化，当匹配的文件发生变化时自动执行相应任务或回调函数。
*   **语法**：

    ```javascript
    gulp.watch(glob[, opts], tasks)
    ```

    * **glob**：文件匹配模式，与 gulp.src() 中的用法类似。
    * **opts**：可选配置对象。
    * **tasks**：文件变化后要执行的任务数组，或者直接传入回调函数。
* **两种用法**：
  1.  **任务数组模式**：

      ```javascript
      gulp.watch('js/**/*.js', ['uglify', 'reload']);
      ```
  2.  **回调函数模式**：

      ```javascript
      gulp.watch('js/**/*.js', function(event) {
        console.log(event.type); // added, changed, 或 deleted
        console.log(event.path); // 变化的文件路径
      });
      ```

***

### **Del：删除文件**

（注：一般使用第三方模块 `del` 来删除文件，示例不在此展开）

***

### **自动刷新（LiveReload）**

* 使用 `gulp-livereload` 插件实现代码变化时自动刷新页面。
*   **安装**：

    ```bash
    npm install --save-dev gulp-livereload
    ```
*   **示例**：

    ```javascript
    const gulp = require('gulp');
    const less = require('gulp-less');
    const livereload = require('gulp-livereload');

    gulp.task('less', function() {
      return gulp.src('less/*.less')
        .pipe(less())
        .pipe(gulp.dest('css'))
        .pipe(livereload());
    });

    gulp.task('watch', function() {
      livereload.listen(); // 启动 livereload 服务器
      gulp.watch('less/*.less', gulp.series('less'));
    });
    ```

***

### **总结**

* **gulp.src()** 用于获取文件流，生成的是包含文件信息的虚拟文件（Vinyl 文件），支持复杂的 glob 模式匹配与排除规则。
* **gulp.dest()** 用于写入文件流，根据 base 路径生成最终输出路径；传入的路径只用于指定目标目录。
* **gulp.task()** 用于定义构建任务，推荐使用 gulp.series 与 gulp.parallel 来控制任务顺序与并行执行。
* **gulp.watch()** 用于监视文件变化，支持任务模式和回调模式。
* 此外，还可结合其他插件（如 gulp-livereload）实现自动刷新等功能。

