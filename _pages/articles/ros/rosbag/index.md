---
permalink: articles/ros/rosbag
title: "rosbag"
header:
  overlay_image: /assets/images/coding.jpg
  caption: "Photo credit: [**Pexels**](Pixabay.com)"
  overlay_filter: rgba(0, 0, 0, 0.4)
toc: true
toc_sticky: true
categories:
  - single
tags:
  - Memo
author_profile: false
---

<div class="row">

<div class="medium-12  columns" markdown="1">
  
# rosbag
* ROSのTopicをタイムスタンプ付きで保存できる。
* 保存したbagfileを再生することも可能。
## 基本的な使い方
* 指定したトピックを保存。
```
  $ rosbag record (-o 出力パス) topic名1 topic名2 ...
```
  * 出力パスをしていしなければ、カレントディレクトリに日時のファイル名で保存される。
  * ファイルサイズが大きくなるので、外付けハードディスク等へ出力するといい。
* 記録したrosbagを再生
```
  $ rosbag play bagfile (-s 開始時間のオフセット) (-u 再生時間) (-l)
```
  * -sオプションで、最初から何秒の地点から再生開始するか指定できる（秒数）。
  * -uオプションで、再生する時間を指定できる（秒数）。
  * -lオプションをつけると、ループ再生できる。
  * 再生中は、キー入力で一時停止、再生が可能。
  
# rosbag関連のツール
## rqt_bag
* GUIでrosbagを再生する。
* topic出力のon、offや、タイムラインを使って、任意の時間のtopicを見ることもできる。
* 画像、文字、数値などはGUI上で表示して確認できる。
* 横軸を時間、縦軸を数値として、時系列グラフの描画も可能。
* タイムラインを見ることで、topicの抜け落ちも確認できる。

### 使い方
* 起動
```
  rosrun rqt_bag rqt_bag bagfileのパス
```
  * rosノードなのでroscoreが必要。
  * 起動したら、topic名を右クリックとかでいろいろできる。

## bagedit
* bagfileのトリミングや、複数のbagfileのマージができる。
### ソースコード
* https://github.com/MHarbi/bagedit
### 使い方
* トリミング
```
  rosrun bagedit bagtrim.py (-s 開始時間) (-e 終了時間) bagfileのパス
```
* マージ
```
  rosrun bagedit bagmerge.py (-o output_file) ベースのbagfileのパス マージするbagfileのパス
```

---
  
|[TOP](/) | <a href="javascript:history.back()">back</a> |
