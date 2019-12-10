# gulp導入手順
参考サイト  
https://ics.media/entry/3290/

### gulpとは
gulpはNode.jsのパッケージ（ライブラリ）のひとつです。gulpを利用するにはNode.jsのインストールが必要です。

### gulp.fileサンプル
```java
// gulpプラグインの読み込み
const gulp = require("gulp");
// Sassをコンパイルするプラグインの読み込み
const sass = require("gulp-sass");
 //ベンダープレフィックス
const autoprefixer =require('gulp-autoprefixer');
// browser-syncのプラグインの読み込み
const browserSync = require("browser-sync");


/// 自動リロード ////////////////////////////////////////////
gulp.task("browserSync", function() {
  browserSync({
    server: {
      baseDir : 'dist', // 対象となるディレクトリ
      index : 'html/index.html', // リロードするHTMLファイル
    }
  });
  // srcフォルダ以下のファイルを監視
  gulp.watch("src/**", function(done) {
    browserSync.reload();
    done(); // ファイルに変更があれば同期しているブラウザをリロード
  });
});  
  

/// ファイルをdistにコピー ////////////////////////////////////////////

gulp.task('html', function () {
  return gulp.src("src/**/*.html")
    .pipe(gulp.dest('dist/'))
})

gulp.task('css', function () {
  return gulp.src("src/**/*.css")
    .pipe(gulp.dest('dist/'))
})
gulp.task('js', function () {
  return gulp.src("src/**/*.js")
    .pipe(gulp.dest('dist/'))
})
  

/// cssコンパイル ////////////////////////////////////////////
gulp.task('sass', function() {
  return gulp.src("src/scss/**/**.scss")//sassファイルを読み込む
        .pipe(sass().on('error', sass.logError))
        .pipe( sass({ outputStyle: 'expanded' }) )//CSSの出力形式
        .pipe(autoprefixer( {cascade:false}))
        .pipe(gulp.dest("src/css"));//書き出し
});

/// 監視フォルダ ////////////////////////////////////////////
gulp.task('watch', function(){
  gulp.watch('src/scss/**/**.scss', gulp.task('sass'));
  gulp.watch('src/**/*.html', gulp.task('html'));
  gulp.watch('src/**/*.css', gulp.task('css'));
  gulp.watch('src/**/*.js', gulp.task('js'));
  
});

/// Gulpコマンドで動かすもの ////////////////////////////////////////////

gulp.task('default', gulp.series(
  gulp.parallel('watch','browserSync')
));
```



### gulp.fileサンプル ”task()なし”


```java  

// gulpプラグインを読み込みます
const { src, dest, watch, parallel } = require("gulp");
// Sassをコンパイルするプラグインを読み込みます
const sass = require("gulp-sass");
//ベンダープレフィックス
const autoprefixer = require('gulp-autoprefixer');
// browser-syncのプラグインの読み込み
const browserSync = require("browser-sync");

/// sassのコンパイル ////////////////////////////////////////////

const compileSass = () =>
  // style.scssファイルを取得
  src("src/scss/**/**.scss")
    .pipe(sass().on('error', sass.logError))
    // Sassのコンパイルを実行
    .pipe(
      // コンパイル後のCSSを展開
      sass({
        outputStyle: "expanded" //CSSの出力形式
      })
    )
    .pipe(autoprefixer({ cascade: false }))
    // cssフォルダー以下に保存
    .pipe(dest("src/css"))

/// ファイルをdistにコピー ////////////////////////////////////////////

const html = () =>
  src("src/**/*.html")
    .pipe(browserSync.reload({ stream: true })) 
    .pipe(dest('dist/'))

const css = () =>
  src("src/**/*.css")
    .pipe(browserSync.reload({ stream: true })) 
    .pipe(dest('dist/'))


const js = () =>
  src("src/**/*.js")
    .pipe(browserSync.reload({ stream: true })) 
    .pipe(dest('dist/'))


/// 監視 ////////////////////////////////////////////

const watchSassFiles = () =>
watch("src/**/**/**.scss", compileSass)
watch('src/**/*.html', html)
watch('src/**/*.css', css)
watch('src/**/*.js', js)


/// ファイルをdistにコピー ////////////////////////////////////////////

const browsersync = () =>
  browserSync({
    server: {
      baseDir: 'dist', // 対象となるディレクトリ
      index: 'html/index.html', // リロードするHTMLファイル
    }
  })

// npx gulpというコマンドを実行した時、watchSassFilesが実行されるようにします
exports.default = parallel(watchSassFiles,browsersync)

```

### browser-syncを入れる時の注意

package.jsonに下記を追加
```json
 "browser-sync": "^2.26.7",
```


