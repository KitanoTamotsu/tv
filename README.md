[サンプル動画](https://user-images.githubusercontent.com/40127279/126053119-aac3540b-9642-47f3-86ef-1d5fe4007a29.mp4)
#### 開発メモ
ワークフロー
<br><img width="600" src="https://user-images.githubusercontent.com/40127279/126859172-605ef5f8-5826-44a4-a1ce-50f9db25e380.png">
<br>
<br>参考ワークフロー(Lesson10)
<br><img width="600" src="https://user-images.githubusercontent.com/40127279/126859210-ea34493a-90cf-4914-8274-b089d1645bca.png">

### 1.TV番組の取得と表示
　TV王国のRSSを利用して放送中の番組を取得します
<br>　このワークフローでは東京エリア地デジのRSSを利用していますので適宜修正してください　
<br>
<br>　Alfredの出力フォーマットへのセットですが
<br>　タイトルに番組名、サブタイトルに放送時間、引数にチャンネルとしてみました
<br>　タイトルには思いの外、記号が含まれており、おかしな振る舞いをするかもしれません
<br>　そんなときには修正してくださいね
<br>　ロジックでわかりにくいのは、下記の部分だけだと思います
```
　link=(1 ${link[@]})
```
<br>　これは、配列の添字をあわせるためのコーティングで、${link[@]}は配列全体なので、
<br>　その前に、要素として"1"を追加しています
<br>　つまり、配列の先頭の要素を追加するコードです
<br>　
<br>　Script Filter
<br>　<img width="600" src="https://user-images.githubusercontent.com/40127279/126859248-af370be1-ceca-4195-9542-b5b3ec957bf1.png">

### 2.別のワークフローを呼び出す
　TVのチャンネル変更は、Lesson10で既に作成しているTVのリモコン with NatureRemoという
<br>　ワークフローを流用します
<br>　そのためには、呼び出されるワークフローに入口を設定する必要があります
<br>
<br>　TVのリモコンワークフローVer1.1の青い部分を追加しています
<br>　Externalオブジェクトが入口になります
<br>　IdentifierにChannelという名前をつけています
<br>
<br>　さらにワークフロー自体のIDも必要です
<br>　ワークフロー一覧でダブルクリックするとワークフローの概要が表示されますが、
<br>　中央付近のBundle IDが、ワークフローのIDとなります
<br>```
<br>　com.alfredapp.kitanotamotsu.tv
<br>```
<br>　com.alfredapp.ユーザー名.ワークフロー名という感じが推奨れる表記ルールですが
<br>　一意であればなんでもOKです
<br>　
<br>　External (Lesson10のワークフロー）
<br>　<img width="600" src="https://user-images.githubusercontent.com/40127279/126859289-6d5172e0-2288-4766-96d9-4e966f24189e.png">
<br>　なにやらサンプルコードが目立ちますね。。気になったら下の方のおまけを見てね。
<br>　
<br>　ここまで設定したら、呼び出す側のワークフローを作ります
<br>　Call Externalオブジェクトを配置して、
<br>　Workflow IDに、Bundle IDを、Trigger IDにIdentifierを記入すれば接続可能です
<br>
<br>　Call External
<br>　<img width="600" src="https://user-images.githubusercontent.com/40127279/126859354-058a36dd-cc72-40d3-a7d0-2203afbed662.png">
<br>　
<br>　またTVのリモコンワークフローのKeywordオブジェクトと、
<br>　このワークフローのScriptFilterオブジェクトのキーワドを一致させることでシームレスな
<br>　利用をサポートしています
<br>　つまり、tvと入力するとScriptFilterオブジェクトのリターンである番組表を表示しつつも
<br>　そのまま ++で音量をあげるといった別ワークフローの機能も使えます（入力を受け付けます）
<br>
<br>　なお、今回は別のワークフローを呼び出すサンプルとしてこのような形にしましたが、
<br>　同じワークフローでも呼び出しは可能です
<br>
### 3.エラーを通知させる
　呼び出すワークフローを少しだけ修正しました
<br>　外から呼び出しようにしたので、その際発生したエラーを通知します
<br>　引数として受け取ったチャンネルがcaseのどれにも該当しない場合、エラーとしています
<br>
<br>　PostNotificationオブジェクトで{query}に何か入っている時だけ通知する機能があるので
<br>　それを利用したのですが、1点誤算がありました
<br>　正常なチャンネル変更の場合でも、CurlでNatureRemoのAPIをPOSTした際に
<br>　"{}"が引数となってしまうのです
<br>　仕方なくRunScriptオブジェクトでコントロールしましたが、ちょっと不恰好
<br>
<br>　Post Notification (Lesson10のワークフロー）
<br>　<img width="600"  src="https://user-images.githubusercontent.com/40127279/126859390-62d85dfe-3446-4d7d-9a22-7ffd9832738f.png">
<br>
<br>　Run Script (Lesson10のワークフロー）
<br>　<img width="600" src="https://user-images.githubusercontent.com/40127279/126859405-80e1a267-6c8f-42b6-bf4c-874fa3ec2f08.png">

#### 背景
　RSS関連のワークフローを作っている時に思いつきました
<br>　そうだ、TV番組一覧から好きな番組を選んで、チャンネル変更すると便利かも
<br>　思いついて、ちょっといじってみると、簡単にできてびっくりです
<br>　なお、ワークフロー接続は、アップルスクリプトからの接続も可能です
<br>　Alfredってよくできてますね
<br>　
#### おまけ
　ということで、スクリプトからの呼び出しを試してみました
<br>　[RandomTV.app](https://github.com/KitanoTamotsu/tv/files/6835528/RandomTV.app.zip)というドロップレットです
<br>　適当にファイルをドロップすると、そのパス＋ファイル名の文字数（9で割っ余り+1）でTVのチャンネル変更をします
<br>　RandomTV.appをautomatorで開くとスクリプトが見れますが、短いので以下に貼り付けます
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
<br>　　前提として、[TVのリモコン with NatureRemo.workflow](https://kitanotamotsu.github.io/natureremo)のインストールが必要です
### 使い方:
　キーワード『tv』で起動
#### 修正履歴

### ver1.1(2021-04-04)：
　シェルスクリプトをbashからzshに変更しました
<br>
<br>
[トップページに戻る](https://kitanotamotsu.github.io/)

