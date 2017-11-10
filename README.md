# Zooids: 群れになって作業をお手伝いする小さなロボット
![Teaser](/Images/Teaser3.png)
ここではスウォームユーザインタフェースについて説明します。 これは新しいヒューマンインタフェースのかたちであり、複数の自律ロボットがディスプレイ表示や対話を手がけます。 我々がデザインしたZooidsは卓上型のスウォームユーザインタフェースのための、オープンソース・オープンハードなプラットフォームです。 そのプラットフォームは、直径2.6cmのカスタムデザインなホール付きマイクロロボット、無線のベースステーション、光学式トラッキングのためのDLPという仕組みの高速なプロジェクター、アプリケーション開発と制御のためのソフトウェアフレームワークからなっております。我々はこのZooidsの、卓上型スウォームユーザインタフェースとしての能力とそのデザインについて説明します。

[ここ](https://www.youtube.com/watch?v=ZVdAfDMP3m0)にデモンストレーション動画がありますのでご覧ください。

## ハードウェアについて
<p align="center">
<img src="Images/exploded.PNG" alt="exploded" width="400">
</p>

上に示すように、Zooidsは小さなカスタムメイドロボットです。直径は26mmで高さが21mm、重さが12gとなってます。各ロボットは100mAhのLiPoバッテリーにて駆動しています。また、それには静電容量式タッチセンサーとメインコンピュータのNRF24L01+が含まれています。

### トラッキングについて
我々のシステムでは、下に示すようなプロジェクターベースのトラッキングシステムを用いています。このプロジェクターはテキサス・インスツルメンツ株式会社のメーカーのものを用いています。この製品は、DLPライトクラフターという仕組みによって、高速に（3000Hz）グレイコードのパターンを順番に映し出すことができます。またそのとき、各ロボットに搭載されたフォトダイオードがそれぞれ独立にデコードを行い、プロジェクターで移されたエリア内の位置を認識します。このプロジェクターベースのトラッキングシステムのセットアップ方法はレポジトリの中にあります。

## ソフトウェアについて
<p align="center">
<img src="Images/architecture.PNG" alt="architecture" width="700">
</p>

本システムの通信の構成は４つのレベルの層で形成されています。上から下の順で、アプリケーション、シミュレーション、サーバー、ハードウェアとなってます。

アプリケーションレベルでは、ロボットたちの行きたい場所を計算します。所望の場所をネットワークソケットを介してシミュレーション層に送信されます。アプリケーションプログラマは２つの制御方法を選ぶことができます。Proportional-Integral-Derivative(PID)かHybrid Reciprocal Velocity Obstacles(HRVO)とPIDとの組み合わせ、のどちらかになります(それぞれの説明は後述します)。その制御方法に基づいて、シミュレーション層ではロボットたちの目的地を計算します。PIDの場合は最終位置を、HRVOの場合は中間点を算出します。またこれらの情報をサーバーに送信します。最後にサーバー層にてコマンドを各ロボットに送信します。その間同時に、各ロボットのステータスと位置をモニタリングします。

各ロボットは下にしめすような制御を独立に行います。ゴール位置を与えられ、ロボットは最初に正しい方向へ回転します。一度整列されると、ユーザが定義したスピードになるまで加速します。そのスピードに達すると、それを維持しながら最終目的位置にPID制御を行いながら向かいます。新たな中間位置が与えられてもロボットはスピードを維持したまま且つPID制御を行いながら最終目的位置に向かいます。最終目的位置5cm以内となったときスピードは半分になり、最終目的位置1cmとなったときアプリケーション層からストップコマンドが送られます。中間位置をスムースに送るために送信を60Hzの速さで行います。

<p align="center">
<img src="Images/local_control.PNG" alt="control" width="700">
</p>

## Acknowledgments 

This is a joint work between the Shape Lab at Stanford University (USA) and the Aviz team at Inria (France).
It was partially funded by the Région Ile de France, DIM ISC-PIF. We would also like to thank Alexa Siu, Shenli Yuan, Ernesto Ramirez and Pham Minh Hieu for investing so much time and efforts in making this work possible. 

<p align="center">
<img src="Images/logos.png" alt="logos" width="700">
</p>

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
