# TSUBAME-4.0-hands-on

## はじめに

### 利用の前に

[GitHub: 新しいSSHキーを生成する](https://docs.github.com/ja/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)を参考にSSHキーを作成してください。
次にTSUBAMEポータルにログインします。
[利用者情報] > SSH公開鍵登録 から 公開鍵をuploadしてください。
(コピー&ペーストが上手くいかない場合は、uploadをお試しください)

### ログインノードに接続

`user-name`, `key`を置き換えて接続してください。

```bash
ssh user-name@login.t4.gsic.titech.ac.jp -i ~/.ssh/key
```

以下のように出れば成功です。

```bash
-----------------------------------------------------------------
Last modified: Mon Apr  1 10:00:09 JST 2024


    (The current TSUBAME 4.0 operational status)
https://www.t4.gsic.titech.ac.jp/      X(Twitter):@Titech_TSUBAME
-----------------------------------------------------------------
```

ssh configを設定されたい方は以下のようにして設定してください。

```bash
Host tsubame
  HostName login.t4.gsic.titech.ac.jp
  User <username>
  IdentityFile ~/.ssh/ssh-key
```

**注意** : ログインノードは多数のユーザが同時に利用しているため、CPUを占有するプログラムを実行しないでください。llm-jpのｍdxでは、login nodeにて処理をするユーザーが見受けられますが、そのような行為はやめてください。

詳しい禁止事項については以下をご覧ください
https://www.t4.gsic.titech.ac.jp/docs/all/handbook.ja/start/#login_limit
vscode, jupyter等でlogin nodeに接続することも厳禁です。(上述のリンクにもあるように)
