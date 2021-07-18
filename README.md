## 　　Lesson18.　別のワークフローを呼び出す  
#### 開発メモ

[サンプル動画](https://user-images.githubusercontent.com/40127279/126053119-aac3540b-9642-47f3-86ef-1d5fe4007a29.mp4)

### 1.TV番組の取得と表示
　TV王国のRSSを利用して放送中の番組を取得します
<br>　このワークフローでは東京エリア地デジのRSSを利用していますので、適宜修正してください　
<br>
<br>　Alfredの出力フォーマットへのセットですが、タイトルに番組名、サブタイトルに放送時間、引数にチャンネルとしてみました
<br>　タイトルには結構記号が含まれており、おかしな振る舞いをするかもしれません
<br>　そんなときには修正してくださいな
<br>　ロジックでわかりにくいのは、下記の部分だけだと思います
```
　link=(1 ${link[@]})
```
<br>　これは、配列の添字をあわせるためのコーティングで、
<br>　このようにすると、配列のはじめの要素として"1"を追加できます
### 2.別のワークフローを呼び出す
　TVのチャンネル変更は、Lesson10で既に作成しているTVのリモコン with NatureRemoワークフローで実行させましょう
<br>　そのためには、呼び出されるワークフローに入口を設定する必要があります
<br>
<br>　TVのリモコンワークフローVer1.1の青い部分を追加しています
<br>　Externalオブジェクトが入口になります
<br>　IdentifierにChannelという名前をつけています
<br>
<br>　さらにワークフロー自体のIDも必要です
<br>　ワークフロー一覧でダブルクリックするとワークフローの概要が表示されますが、
<br>　中央付近のBundle IDが、ワークフローのIDとなります
<br>
<br>　com.alfredapp.kitanotamotsu.tv
<br>
<br>　com.alfredapp.ユーザー名.ワークフロー名という感じが推奨れる表記ルールです
<br>　一意であればなんでもOKです
<br>　
<br>　ここまで設定したら、呼び出す側のワークフローを作ります
<br>  Call Externalオブジェクトを配置して、
<br>  Workflow IDに、Bundle IDを、Trigger IDにIdentifierを記入すれば接続可能です
<br>
<br>　またTVのリモコンワークフローのKeywordオブジェクトと、
<br>　このワークフローのScript Filterオブジェクトのキーワドを一致させることでシームレスに使えます
<br>　つまり、tvと入力するとScript Filterオブジェクトのリターンである番組表を表示しつつも
<br>　tv ++で音量をあげるといった別ワークフローの機能も使えます（入力を受け付けます）
<br>
<br>　なお、今回は別のワークフローを呼び出すサンプルとしてこのような形にしましたが、ひとつのワークフローでも可能です
<br>
### 3.エラーを通知させる
　呼び出すワークフローを少しだけ修正しました
<br>　外から呼び出しようにしたので、その際発生したエラーを通知します
<br>　引数として受け取ったチャンネルがcaseのどれにも該当しない場合、エラーとしています
<br>
<br>　PostNotificationオブジェクトで{query}に何か入っている時だけ通知する機能があるので
<br>　それを利用したのですが、1点誤算がありました
<br>　正常なチャンネル変更として、CurlでNatureRemoのAPIをPOSTした際に、"{}"が引数となってしまうのです
<br>　仕方なくRun Scriptオブジェクトでコントロールしましたが、ちょっと不恰好
<br>
#### 背景
　RSS関連のワークフローを作っている時に思いつきました
<br>　そうだ、TV番組一覧から好きな番組を選んで、チャンネル変更すると便利かも
<br>　思いついて、ちょっといじってみると、簡単にできてびっくりです
<br>　なお、ワークフロー接続は、アップルスクリプトからの接続もできますし、そのワークフロー自身への接続も可能です
<br>　Alfredってよくできてますね
<br>　
#### おまけ
　[RandomTV.app.zip](https://github.com/KitanoTamotsu/tv/files/6835528/RandomTV.app.zip)というドロップレットを作成しました 
<br>　適当にファイルをドロップすると、そのパス＋ファイル名の文字数（9で割っ余り+1）でTVのチャンネル変更をします
<br>　RandomTV.appをautomatorで開くとスクリプトが見れますが、短いので、以下に貼り付けます
<br>　tell部分がAlfredワークフローの呼び出し方です
``` 
     on run {input, parameters}	
	set letters to input as text	
	set channel to (length of letters) mod 9 + 1
	-- display dialog letters & " " & (length of letters)  & " " & channel
	tell application id "com.runningwithcrayons.Alfred" to run trigger "channel" 
	in workflow "com.alfredapp.kitanotamotsu.tv" with argument channel	
     end run
``` 

#### 取扱説明
### 機能:
　現在放送中のTV番組を表示させ、選択した番組へチャンネルを切り替える
<br>　
### インストール:
　1.[Alfredworkflow](https://github.com/KitanoTamotsu/tv/releases/download/1.0/TV.alfredworkflow.zip)をダウンロード 
<br>　2.ファイルをダブルクリックしてワークフローに登録
<br>　前提として、[TVのリモコン with NatureRemo.workflow](https://kitanotamotsu.github.io/natureremo)のインストールが必要です
### 使い方:
　キーワード『tv』で起動
#### 修正履歴

### ver1.1(2021-04-04)：
　シェルスクリプトをbashからzshに変更しました

<br>
<br>
[トップページに戻る](https://kitanotamotsu.github.io/)

