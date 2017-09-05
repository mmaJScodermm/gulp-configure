# gulp-configure
a gulp config for routine work
```
//devDependencies:  npm install gulp gulp-less gulp-autoprefixer gulp-clean-css gulp-concat gulp-uglify gulp-imagemin gulp-notify gulp-rename gulp-livereload gulp-path gulp-cache del gulp-rev gulp-rev-collector --save-dev
// 依赖
var gulp = require('gulp'), 
    less = require('gulp-less'),
    path = require('path'),
    autoprefixer = require('gulp-autoprefixer'),
    minifycss = require('gulp-clean-css'),
    uglify = require('gulp-uglify'),
    imagemin = require('gulp-imagemin'),
    rename = require('gulp-rename'),
    clean = require('gulp-clean'),
    concat = require('gulp-concat'),
    notify = require('gulp-notify'),
    cache = require('gulp-cache'),
    livereload = require('gulp-livereload')
    //- 对文件名加MD5后缀       
    rev = require('gulp-rev'),     
    //- 路径替换                 
    revCollector = require('gulp-rev-collector'); 
 
// 样式
gulp.task('styles', function () {
  return gulp.src('./ui_v5/h5-new/css/invite_friends.less')
    .pipe(less({
      paths: [ path.join(__dirname, 'less', 'includes') ]
    }))
    .pipe(gulp.dest('./ui_v5/h5-new/css/'))
    .pipe(minifycss())
    .pipe(rename({ suffix: '.min' })) 
    .pipe(rev()) 
    .pipe(gulp.dest('./build/public/css'))
    .pipe(rev.manifest())                                  
    .pipe(gulp.dest('./build/rev/css')); 
});
 
// 脚本
gulp.task('scripts', function() { 
  return gulp.src(['./ui_v5/h5-new/js/buttom.js','./ui_v5/h5-new/js/count.js'])
    // .pipe(concat('main.js'))
    .pipe(gulp.dest('./build/public/js'))
    .pipe(uglify())
    .pipe(rev())
    .pipe(rename({ suffix: '.min' }))
    .pipe(gulp.dest('./build/public/js'))                          
    .pipe(rev.manifest())                                
    .pipe(gulp.dest('./build/rev/js'));
});

// 图片
gulp.task('images', function() { 
  return gulp.src('./ui_v5/h5-new/images/**/*')
    .pipe(cache(imagemin({ optimizationLevel: 3, progressive: true, interlaced: true })))
    .pipe(gulp.dest('./build/public/images'))
});
 
// 清理
gulp.task('clean', function() { 
  return gulp.src(['./build/public/css', './build/public/js', './build/public/images'], {read: false})
    .pipe(clean());
});
 
// 预设任务
gulp.task('default', ['clean'], function() { 
    gulp.start('styles','scripts');
});

// 替换html文件
gulp.task('rev', function() {
    //- 读取 rev-manifest.json 文件以及需要进行css名替换的文件
    return gulp.src(['./build/rev/**/*.json', './cunguan_test/resources/views/static/annual_report.blade.php']) 
     //- 执行文件内css名的替换  
        .pipe(revCollector())     
         //- 替换后的文件输出的目录                             
        .pipe(gulp.dest('./build'));                    
});
 
//监听
gulp.task('watch', function() {
   livereload.listen();
  // 监听所有.less
  gulp.watch('./ui_v5/h5-new/css/*.less', ['styles']);
 
  // 监听所有.js档
  gulp.watch('./ui_v5/h5-new/js/*.js', ['scripts']);
 
  // 监听所有图片档
  gulp.watch('./ui_v5/h5-new/images/**/*', ['images']);

});
```

