# 5ch(旧2ch)をスクレイピングして、過去はやったネットスラングの今

## 5chの過去ログをスクレイピングするには

### 幅優探索による過去ログのスクレイピング
 URL同士のリンクばネットワーク構造になります。  
 スクレイピングする際の戦略として、ネットワークをどうたどるか、という問題で、幅優先探索を行いました。  
 2chの過去ログから辿れるログは平面的に大量のリンクを2~3回たどれば目的のデータにアクセスできる構造で幅優先探索に適していたからという理由です。  
 
<div align="center">
  <img width="600px" src="https://user-images.githubusercontent.com/4949982/48311037-58ca0300-e5dc-11e8-93b7-b95144a20a2d.png">
</div>
<div align="center"> 図1. ドイツのフランクフルトからの路線図で幅優先探索をした場合(Wikipedia) </div>

#### 起点となる一点を決める
 かねてから2chの全ログ取得は夢でしたが、様々な方法を検討しましたが、ログが保存されているURLの一覧が存在しないということで諦めていたのですが、ついに発見するに至りました。  
 以下のURLからアクセスすることができ、多くのスレの過去ログサーバを参照しています。  
 そのためここからアクセスすることで2chの過去ログをスクレイピングすることができます。  
```
 http://lavender.5ch.net/kakolog_servers.html
```

#### レガシーなhtmlフォーマットに対応する
 Pythonのhtmlパーサーを前提に話しますが、旧2chのHTMLは正しいHTMLというわけでないようです。  
 tableタグを多用するデザインが2017年度半ばまで主流だったようで、このときのタグに閉じるの対応なく、lxml, html.parserなどを使うと失敗します。  
 そのため、一部の壊れたhtmlでもパースできるようにhtml5libパーサーを利用してパースすることができます[1]
 
　この問題は、BeautifulSoupのパーサを以下のようにhtml5libに設定すれば解決することができます。  
```python
soup = bs4.BeautifulSoup(html, 'html5lib')
```
 
#### 並列アクセスを行う
 2chの過去ログは、一つ一つのサーバに名前がついていて、各サーバが異なったサブドメインを持っています。  
 そのため、異なった実サーバをもっていると考えられるので、サーバごとにアクセスを並列化することで高速化することができます。加えて、もともとPythonのrequestsとBeautifulSoupを使ったhtml解析が重い作業なので、マルチコアリソースを最大限利用して、並列アクセスする意義があります。  
 
## ネットスラングの選定
一般的なネットスラングは時代の変遷の影響を受けるという感覚値がありました。  

過去、２０年間に存在してきた様々なネットスラングについて、様々な[まとめ[2]](https://dic.nicovideo.jp/a/%E3%83%8D%E3%83%83%E3%83%88%E3%82%B9%E3%83%A9%E3%83%B3%E3%82%B0%E3%81%AE%E4%B8%80%E8%A6%A7)があり、みているととても懐かしくなります。  

過去、一瞬見て違和感があったり、今でも使わているのだろうか？最近見ていないがどの程度減ったのか、という視点で選んだ単語がこれらになります。  

 - orz
 - 尊師
 - 香具師
 - '笑 '
 - 初音ミク
 - 結月ゆかり
 - コードギアス
 - hshs
 - iphone
 - うｐ
 - 自宅警備員
 - ワンチャン
 - ステマ
 - 情弱
 - チラ裏
 - 今北産業
 - 禿同
 - 'w '
 - メシウマ
 - まどマギ
 - ソシャゲ
 - ジワる
 - ナマポ
 - (ry
 - ggrks
 - オワコン

### htmlをjsonl化する
 スクレイピングしたhtmlをスレの内容を取り出し、jsonl(一行に一オブジェクトのjson)にしておくといろいろと集計が都合がよいです。  
`scan_items.py`というプログラムでパースできるので、参考にしてください。 
```console
$ python3 scan_items.py
```
 
### 結果
 
# 参考
 - [1] [How do I fix wrongly nested / unclosed HTML tags?](https://stackoverflow.com/questions/293482/how-do-i-fix-wrongly-nested-unclosed-html-tags)
 - [2] [ネットスラングの一覧](https://dic.nicovideo.jp/a/%E3%83%8D%E3%83%83%E3%83%88%E3%82%B9%E3%83%A9%E3%83%B3%E3%82%B0%E3%81%AE%E4%B8%80%E8%A6%A7)
