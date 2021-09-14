# お名前.com で取得したドメインを GitHub Pages で使う

[参考](https://mae.chab.in/archives/60095)

## CNAME or A レコード ?

- A レコード（Address Record）: IP アドレスを指定。独自ドメインに IP アドレスを紐付けます。直接独自ドメインのドメイン名のみ（Zone Apex）で指定した IP アドレスが割り当てられた Web サーバーにアクセスできるようになります。
- CNAME（Canonical NAME）: 転送したいホスト名つきドメイン（FQDN）を指定。独自ドメインから指定したドメインへの転送を行います。A レコードを設定したドメインを指定することで、独自ドメインから Web サーバーにアクセスができるようになります。

CNAME でやるほうが何かと楽そうな感じがしたから CNAME でやる

## お名前.com で登録する

[お名前.com のドメイン設定ページ](https://www.onamae.com/domain/navi/dns_manage)で設定する。

設定したいドメインを契約済みドメインから選択し、"DNS レコード設定を利用する" メニューを選ぶ。

- ホスト名: www
- TYPE: CNAME
- TTL: 変更なし
- VALUE: sugitlab.github.io

で登録する

## GitHub の Settings を変更する

Settings にある Pages の Custom domain に先程登録したドメインを入力する

- www.sugitlab.work
