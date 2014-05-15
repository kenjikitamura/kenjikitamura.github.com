---
layout: page
title: "Android"
date: 2014-02-15 20:27
comments: true
sharing: true
footer: true
---

## Androidについてのメモです
### AndroidManifest
Androidアプリで一番重要な設定XMLファイルです。

* アプリの権限
* Activityの定義
* Serviceの定義
* Receiverの定義

などなどを行います。
ActivityやServiceのクラスを作成して、AndroidManifestに定義を追加するのを忘れて「Activity起動しないよー」となっちゃうので注意しましょう。

#### いろいろな"Version"
AndroidManifestには、いろいろバージョンを指定する部分があります。何がどのような意味を持っているのかを確認してみましょう。

* manifest要素のversionCode属性

アプリケーションの内部的なバージョンを表します。versionCodeは通常ユーザには見えないバージョンで、システムがより新しいアプリケーションかどうかを判断する際に使用します。Play Storeでアプリ公開時に1からスタートし、バージョンアップを公開するたびに1ずつ上げるのが基本的な使い方です。

場合によっては、後述するversionNameとそろえて、1.0.0だと10000、1.3.2だと10302みたいにつけるとわかりやすかったりもします。

新しいバージョンを公開する際は、必ずversionCodeの増加が必要です。でないと新しいバージョンと認識されませんし、そもそもPlay Storeへのアップロードができなかったかと思います。

versionCodeは[integerと定義](http://developer.android.com/guide/topics/manifest/manifest-element.html)されているので、最大値は2147483647となっています。
[Androidの内部的](http://tools.oesf.biz/android-4.4.2_r1.0/xref/frameworks/base/core/java/android/content/pm/PackageInfo.java)にもintとして持っていますね。

versionCodeがintの最大値である2147483647に達してしまうと、バージョンアップするときにはどうなっちゃうんでしょうか。

* manifest要素のversionName属性

ユーザに見えるバージョンです。stringなので、文字列を指定します。"1.0.1"とか、"0.0.2 Beta"とかですね。システム的にはユーザに見せる以外には使っていないので、ユーザにわかりやすい値を指定します。最大文字数は特にドキュメントに定義されていませんでした。Androidのフレームワーク的にも[AndroidManifest読み込み時](http://tools.oesf.biz/android-4.4.2_r1.0/xref/frameworks/base/core/java/android/content/res/TypedArray.java#getNonConfigurationString)に特に文字数制限はしておりません。

* uses-sdk要素のminSdkVersion属性

uses-sdk要素は、アプリケーションが要求するAPIレベルを指定します。APIレベルとは、Androidプラットホームで提供されるフレームワークAPIのリビジョンを一意に識別する数値です。APIレベルについて詳しくは[こちら](http://www.techdoctranslator.com/android/appendix/api-levels)。minSdkVersion属性はアプリケーションが必要とする一番小さいAPI

* uses-sdk要素のtargetSdkVersion属性
* uses-sdk要素のmaxSdkVersion属性

### Activity
#### Activityの定義
#### Activityの起動方法
#### Activityのライフサイクル
### 設定画面の作り方

### その他
#### AttributeSetでgetAttributeValueしても値が取れないよ！と言うとき
Viewのコンストラクタで、AttributeSetの値を取りたいことがあります。
attrs.getAttributeValue("android", "onClick");
してもnullしか返らないよ！と思ったら、Namespaceのandroidはエイリアスなので、元の長いものを指定しないとダメです。

attrs.getAttributeValue("http://schemas.android.com/apk/res/android", "onClick");
とすればいけます。

#### Activityを前面に移動させる。
あるActivity内で処理を開始し、バックグラウンドで処理を実行する事があります。
その際、当然ホーム画面や他の画面に移動することができますが、処理が完了した後に元のActivityを表示したいことがあります。そんな時は次のIntentを投げてあげればOK。


``` java
Intent intent = new Intent(context, HogeActivity.class);
intent.setFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
startActivity(intent);
```

### BroadcastReceiver
BroadcastReceiverを使い、BroadcastされたIntentを受け取るには、AndroidManifest.xmlに記述する静的なものと、コード上で定義する動的なものがあります。
一部のIntentは動的に登録しないとレシーブできないものがありますので、注意が必要です。

#### 画面のON/OFFを検知する
##### 現在画面が着いているかどうか
現時点で画面が点灯しているかどうかは、PowerManagerのisScreenOnメソッドで取得できます。
``` java
PowerManager pm = (PowerManager) getSystemService(Context.POWER_SERVICE);
boolean isScreenOn = pm.isScreenOn();
```

##### 画面ON/OFF Intentを受け取る
画面のON/OFFを検知したい場合は、  
android.intent.action.SCREEN_ON  
android.intent.action.SCREEN_OFF  
のIntentをBroadcastReceiverで受け取ります。

### サービス
Activityは画面を持つアプリケーションですが、画面を持たないアプリケーションを作ることができるのがServiceです。
Serviceは3つの利用方法があり、それぞれ特徴があります。

 - Activity.startService(Intent)
一番単純なServiceの利用方法です。
Serviceに対しIntentを送信し処理の起動を行う事ができます。
Serviceがまだ起動していなければ起動します。

startServiceすると、そのたびにService.onStartCommandが呼ばれます。
この方法ではServiceからActivityにコールバックすることができません。

Intent serviceIntent = new Intent(context, MyService.class);
serviceIntent.putExtra("Hoge", true);
context.startService(serviceIntent);

 - bind
二つ目はbindする方法です。
Serviceをbindすることで、Serivceのメソッドを自由に呼ぶことができ、リスナをServiceに登録するなどしてServiceがわからActivityに対してコールバックするなどが可能です。

Activity
    onCreateなど
        context.bindService(new Intent(this,
                MyService.class), connection, Context.BIND_AUTO_CREATE);


    private final ServiceConnection connection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName className, IBinder binder) {
            service = ((MyService.ServiceLocalBinder)binder).getService();
        }

        @Override
        public void onServiceDisconnected(ComponentName className) {
            service = null;
        }
    };


Service
    public class ServiceLocalBinder extends Binder {
        //サービスの取得
        MyService getService() {
            return MyService.this;
        }
    }
    private final IBinder binder = new ServiceLocalBinder();

    @Override
    public IBinder onBind(Intent intent) {
        return binder;
    }


 - AIDLを用いたプロセス間通信が可能なbind
最後はAIDLを定義し、プロセス間通信でサービスをbindして利用する方法です。
AIDLを用いると、メソッド呼び出しでやりとりされる情報(引数、戻り値)をシリアライズし、通信することでプロセス間通信を実現することができます。

AIDLについてはここが詳しい
http://www.techdoctranslator.com/android/appendix/aidl

