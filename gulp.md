###gulp的优点：
+ 基于流的构建系统，不会产生中间文件，只有一次I/O操作
+ 插件更纯粹，功能单一
+ 通过写程序而不是像grunt写配置来完成任务

###grunt任务与gulp任务比较
***grunt文件***

	module.exports = function(grunt) {
	grunt.initConfig({
	    concat: {
	        'dist/all.js': ['src/*.js']
	    },
	    uglify: {
	        'dist/all.min.js': ['dist/all.js']
	    },
	    jshint: {
	        files: ['gruntfile.js', 'src/*.js']
	    },
	    watch: {
	        files: ['gruntfile.js', 'src/*.js'],
	        tasks: ['jshint', 'concat', 'uglify']
	    }
	});


	// Load Our Plugins
	grunt.loadNpmTasks('grunt-contrib-jshint');
	grunt.loadNpmTasks('grunt-contrib-concat');
	grunt.loadNpmTasks('grunt-contrib-uglify');
	grunt.loadNpmTasks('grunt-contrib-watch');


	// Register Default Task
	grunt.registerTask('default', ['jshint', 'concat', 'uglify']);
	};

***gulp文件***

	var gulp = require('gulp');
	var jshint = require('gulp-jshint');
	var concat = require('gulp-concat');
	var rename = require('gulp-rename');
	var uglify = require('gulp-uglify');

	// Lint JS
	gulp.task('jshint', function() {
		return gulp.src('src/*.js')
		    .pipe(jshint())
		    .pipe(jshint.reporter('default'));
	});

	// Concat & Minify JS
	gulp.task('minify', function(){
		return gulp.src('src/*.js')
		    .pipe(concat('all.js'))
		    .pipe(gulp.dest('dist'))
		    .pipe(rename('all.min.js'))
		    .pipe(uglify())
		    .pipe(gulp.dest('dist'));
	});

	// Watch Our Files
	gulp.task('watch', function() {
		gulp.watch('src/*.js', ['lint', 'minify']);
	});

	// Default
	gulp.task('default', ['lint', 'minify', 'watch']);

###gulp任务体模式：
	gulp.task('任务名称', function () {
	    return gulp.src('文件')
	        .pipe(...)
	        .pipe(...)
	        // 直到任务的最后一步
	        .pipe(...);
	});

1.获取要处理的文件,src方法读入文件产生数据流,dest方法将数据流写入文件

2.传递给下一个环节处理,然后把返回的结果继续传递给下一个环节……直到所有环节完成。

3.把整个任务的 stream 对象返回出去，以便任务和任务可以依次传递执行。pipe是stream模块里负责传递流数据的方法

4.执行任务，使用命令 gulp missionName

###gulp方法
+ ####task()定义具体任务
	gulp.task('task',[Array],function(){

	});
#####第二个参数为任务依赖列表，这些任务会在当前任务运行之前完成

+ ####src()产生数据流	
 	 - ######js/app.js：指定确切的文件名。
	 - ######js/*.js：某个目录所有后缀名为js的文件。
	 - ######js/**/*.js：某个目录及其所有子目录中的所有后缀名为js的文件。
	 - ######!js/app.js：除了js/app.js以外的所有文件。
	 - ######*.+(js|css)：匹配项目根目录下，所有后缀名为js或css的文件
	 - ######数组：gulp.src(['js/**/*.js', '!js/**/*.min.js'])
	 - ######src()产生数据流
#####参数为所要处理的文件，这些文件会转换为数据流。参数形式见上。
+ ####des()产生数据流
#####将pipe的输出写入文件，可接受第二个参数，表示配置对象，可多次调用
	gulp.dest('build', {
	  cwd: './app', //路径的基准目录
	  mode: '0644' //文件权限
	})
	gulp.src('./client/templates/*.jade')
	  .pipe(jade())
	  .pipe(gulp.dest('./build/templates'))
	  .pipe(minify())
	  .pipe(gulp.dest('./build/minified_templates'));
+ ####watch()定义具体任务(added, changed or deleted)
	gulp.watch('js/**/*.js', function (event) {
	   console.log('File ' + event.path + ' was ' + event.type + ', running tasks...');
	});
	gulp.task('watch', function () {
	   gulp.watch('templates/*.tmpl.html', ['build']);//一旦文件发生变化就出发build任务
	});
#####另一种方法为监听change事件
	var watcher = gulp.watch('templates/*.tmpl.html', ['build']);

	watcher.on('change', function (event) {
	   console.log('Event type: ' + event.type);
	   console.log('Event path: ' + event.path);
	});
