---
title: "gulpのsetupのひととおり"
date: 2020-11-27T16:28:17+09:00
draft: true
---
![gulp](../gulp.jpg)


お仕事でgulpを使うようになるので、set upの覚書。  



#### 何はともあれインストール  
node.jsがインストールされているていで進めます。  
これからgulpを導入したい作業ディレクトリを作り、そのディレクトリに移動。
```
 mkdir test-gulp
```
```
cd test-gulp
```
package.jasonをディレクトリに作成。
```
npm init -y
```
![jason](../first-jason.png)
jasonファイルの中身はこんな感じに。   
これからここに設定などが色々入ってきます。

## Gulpのインストール。
```
 npm install -D gulp
```
 これでgulpが使える状態になった様子。

## Sassをgulpでコンパイルする

このためには、モジュールが必要。  
インストールします。
```
npm install -D gulp-sass
```
gulp-sassは、sassをコンパイルするためのプラグイン 。

sassをコンパイルするために、gulpfile.jsというファイルをプロジェクト直下につくり、その中に設定を記述していく。

![chuuyaku](../chuuyaku.jpg)

▼gulpfile.js
```js
const gulp = require("gulp"); //本体の読み込み
const sass = require("gulp-sass");  // Sassをコンパイルするプラグインの読み込み

gulp.task("default", function() { // タスクを作成する
  
  return (  // style.scssファイルを取得
    gulp
      .src("sass/style.scss") //タスクの対象となるファイルを取得
      .pipe(sass()) // Sassのコンパイルを実行
      .pipe(gulp.dest("css")) // cssフォルダー以下にcssを吐き出す
  );
});
```

### 実行
ターミナルに戻って実行！
```
npx gulp
```
これでcssディレクトリにsassからコンパイルされたcssファイルが作成されます。  
※もし`command not found`みたいに出たら、`npm install gulp-cli -g`でgulpコマンドが聞くようになったが、このコマンドは非推奨らしい。

[こちら : gulp-cliはインストールすべきじゃないと、思うよ](https://qiita.com/sawa-zen/items/413bab0ec738a272c0b0)

うーん、あしからず。よい方法あれば追記しますor教えてください。  


## sassでリセットcssを読み込む

sassを使う前にリセットcssを読み込む設定をする。  
[node-sass-package-importer](https://www.npmjs.com/package/node-sass-package-importer) なるものを使うと順調な模様。  
一緒にインストール。  

```
$ npm i -D node-sass-package-importer
$ npm i -S html5-reset normalize.css
```

sassのオプションに指定する
▼gulpfile.js
```js
const gulp = require("gulp");
const sass = require("gulp-sass");
const packageImporter = require('node-sass-package-importer');  //node-sass-package-importer

gulp.task("default", function() { 
  
  return (  
    gulp
      .src("sass/style.scss") 
      .pipe(sass({   //オプションに指定
         importer: packageImporter({
           extensions: ['.scss', '.css'],
          })
      })) 
      .pipe(gulp.dest("css")) 
  );
});
```



### リセットcssをインポートする  
▼style.scss  
ファイルのtopで読み込んで完了！
```
@import "~normalize.css";
@import "~html5-reset";
```
これでリセットcssの反映はできているはず。

### コンパイルあとのインデントをきれいにする  
コンパイルされてcssに吐き出されたコードのインデントが、scssのネストをそのまま引き継いでしまうため、  
これをきれいにします。  
```js
gulp.task("default", function() { 
  
  return (  
    gulp
      .src("sass/style.scss") 
      .pipe(sass({
         outputStyle: 'expanded',  //ここを追加
         importer: packageImporter({
           extensions: ['.scss', '.css'],
          })
      })) 
      .pipe(gulp.dest("css")) 
  );
});
```
![indent](../indent.jpg)
これできれいに整形されます。ok!

### コンパイルを自動化する  

毎回`npx gulp`と打ってコンパイルしてたらかなりだるいですね。  
自動化します。  
これでリロードすればコンパイルされてます。  
本当はリロードしなくてもコンパイルされてるのが理想なんだけど、方法あるかな‥  

