# Harr-Likeの識別器をつくる
OpenCVを用いて画像認識ができるそうで色々試して遊んでいたのですが、認識できる部位（顔とか目とか上半身だとか）はデフォルトで用意されているものしかなかったです。これは面白くないので他の部位でも認識できるように探し回った結果、精度はおいといてできるようになったので記録しておきます。  

作者にコンタクト:@kinketsucom(twitter)

__注意__ 作成日時は2017/9/1です。気が向いたら直しますが、このドキュメントを通して行う各自の行為には責任を持ちません。また、削除要請には即座に応じます。

__重要__ おっぱいはAIなのです。なぜかわからないようなら機械学習をおやめなさい！！！(ジョークだよ)

# 使用した言語
Pythonですが、このREADMEでは関係ないです。OpenCVをPythonでぽちぽちしました。

# 結果
これでcascade.xmlファイルができたので画像認識を行うことができます。実際に実行したものが以下になります。学習用ファイルをあまりよういしていないので精度は現状良くはないですが、いい感じに認識できているのが実感できればいいのです。  

あくまでおっぱいはAIなのでこれを"Opp AI"(特許申請不可避)と名付けました。
https://twitter.com/kinketsucom/status/903659770707492865




# 作成手順
(OpenCVがあまりにも優秀すぎてほとんどなにもすることがないのです。)
ディレクトリ構成を以下のようにします。僕はmachineというディレクトリからスタートしています。最終的にcascade.xmlを読み込んで画像認識をします。

## ディレクトリ構成
machine  
  ├cascade  
  |　　└trained_data(学習されたcascade.xmlを入れるフォルダ)  
  ├pos(正解の画像を入れるフォルダ)  
  ├neg(不正解の画像を入れるフォルダ)  
  └vec(正解画像からつくるvecデータを入れるフォルダ)  

## ターミナルに入力するコマンド
### positive.datを作成
まず、正解の画像からpositive.datを作成するコマンドです。これでvecフォルダにimage.vecというファイルが作成されます。
```bash
opencv_createsamples -info ./positive.dat -vec ./vec/image.vec -bgcolor 255 -maxidev 40 -maxxangle 0.8 -maxyangle 0.8 -maxzangle 0.5
```

### nglist.txtを作成
つぎに、不正解の画像のファイル名のリストを作成します。
自力で作るのはめんどくさいので以下のコマンドを使用します。
```bash
ls neg | xargs -I {} echo neg/{} > nglist.txt
```

### cascade.xmlを作成
やっとほしかった学習データを取得するコマンドです。

* numPosは正解データ数
* numNegは不正解データ数

とあったのですが、この数は実際にある画像の８割くらいの数にするといいと何処かのサイトでみました。理由は指定した画像が読み込めなかった場合、その画像をスルーして次の画像を読み込み始めるために実際にある画像よりも数が足りないと解釈してしまってエラーを吐くからだそうです。__下にある1000や500はここに書くためにテキトーに書いたものです。__
```bash
opencv_traincascade -data ./cascade/trained_data/ -vec ./vec/image.vec -bg nglist.txt -numPos 1000 -numNeg 500
```
