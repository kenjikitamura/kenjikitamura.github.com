---
layout: page
title: 
date: 2012-07-13 17:04
comments: false
sharing: true
footer: false
---

### 技術メモ
[Android](android)


### Octpress チートシート
* コード
``` java
class Hoge{
  private String fuga;
  public static void main(String[] args){
    // hoge
    System.out.println("FOOOOOOOO!");
  }
}
```

* ページ作成  
より新しいrakeのバージョンがインストールされていると、そのままrakeを実行するとエラーになります。bundle exec rakeすることで解決します。
```
bundle exec rake new_page\[hoge\]
```

* プレビュー  
以下のコマンドを実行した後、http://localhost:4000 でプレビューを確認することができます。

```
bundle exec rake preview
```

* デプロイ  
以下のコマンドで、githubにデプロイすることができます。

```
bundle exec rake deploy
```

### gitチートシート
#### commitの取り消し
コメントが書き足りていなかった、commitに含めるファイルが足りなかったなどは、次のコマンドを用いると直前のcommitをやり直せます。
```
git commit --amend
```

