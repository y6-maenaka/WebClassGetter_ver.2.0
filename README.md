<h1>WebClassGetter ver.2.0</h1>

<br>

<h2>前バージョン(<a href="https://github.com/y6-maenaka/WebClassGetter">WebClassGetter ver.1.0</a>)からの追加機能</h2>
  <ol>
  <li>コンテンツページからダウンロード機能 (ver.1.0では デベロッパーツール → hls player からリンクを直接取得する必要があった)</li>
  <li>SSO認証(SP認証も含む)の自動Pass</li>
  <li>複数動画の並列ダウンロード機能</li>
  </ol>
<br/><br/>

## 必要ライブラリ / モジュール
  ライブラリ　: requirements.txt を参照<br>
  モジュール　: <a href="https://phoenixnap.com/kb/ffmpeg-mac">ffmpeg</a>
<br><br>

## 使い方
  **urls.yml** ファイルに 
  ```yaml
  動画保存名: URL
  ```
   < 例 >
  ```yaml
  サンプル1: https://videos/content/0123
  サンプル1: https://videos/content/0124
  ```

  以上のフォーマットで動画保存名と動画が配信されているWebClassのURLを記載&保存後
  ```bash
  $ python downloader.py
  ```
  でプログラムを起動するとダウンロードが始まる



<br/><br/><br/><br/>
-------
<br/><br/><br/><br/>





## 備忘録
※SSOサーバやWebClassの構造・仕様が公開されていない為,以降の説明は推測. 正しいとは限らない</p>

## SSO認証(SP認証)の流れ
![image_sso_structure](https://github.com/y6-maenaka/WebClassGetter-mk2/blob/main/sso_structure.jpeg)
<ol>
<li>ログインページの取得</li>
<li>応答URLに含まれるクエリパラメータのjsessionidとexecutionを抽出（SSO認証サーバへの問い合わせに必要）</li><br>

<li>SSO認証サーバへユーザ情報(payload)と共にPOST(クエリパラメータには上記取得のjsessionidとexecutionを指定)</li>
<li>SAML(XML)を取得(RelayStateとSAMLResponseを抽出)</li><br>

<li>SPでの認証用トークン(cookie)の取得要求(RelayStateとSAMLResponseをpayloadに指定)</li>
<li>SPでの認証用トークン(cookie)の取得</li><br>

<li>SPへのコンテンツ要求</li>
<li>SPからのコンテンツ応答</li>
<p>その他必要なcookie等も適宜収集される</p>
</ol>
<br/><br/>

## WebClassの仕様
<ul>
<li>SSO認証が済んでいないと,どのWebClassコンテンツにアクセスしてもログインページへリダイレクトするスクリプトが埋め込まれたhtmlが返却される</li>
<li>ユーザが最後に踏んだ講義ページをWebClassサーバ側で記憶している</li>
<li>コンテンツページを開くことができる条件は,コンテンツページが配布されている講義ページを最後にユーザが踏んでいること
(最後に踏んだ講義ページが,指定したコンテンツページの配布元講義以外の講義ページの場合,セッション切れエラーが発生する)</li><br>
<p>※つまり,コンテンツページへの直リンクを有効にするには,コンテンツページの配布元である講義ページを一旦踏む必要がある</p>
</ul>
