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


## ストレージについて

ホームディレクトリは25GiBが上限です。
cacheの保存先などにしていて、ジョブが実行できない等の現象が起きた場合は、保存先を変えたり、いらないファイルを削除したりしてください。

以下のコマンドでhome directoryの現在の使用状況が確認できます(最大1日程度の遅延が発生することがありますが、稀です)

```bash
$ t4-user-info disk home
  uid name         b_size(GB) b_quota(GB)    i_files    i_quota
---------------------------------------------------------------
 2011 TESTUSER              7          25     101446    2000000
```

NIIのプロジェクトには768,000GBのグループ領域が割り当てられています。
`/gs/bs/tgh-NII-LLM`がディレクトリになります。


こちらの領域は、大容量ストレージ領域と呼ばれるストレージになり、ソースコード、データセット、チェックポイント等の保存先として利用します。

## ソフトウェア環境

Environment Modulesが導入されています。
使い慣れていない方は、[こちら](https://modules.readthedocs.io/en/latest/)をご覧ください。

```bash
module avail
```

で利用可能なmodule環境が表示されます。

- cuda/x.x.x : CUDA Toolkitのことを指します。(Driverではありません)
- cudnn/x.x.x : 使用する CUDA Toolkit version に合ったversionを選択してください
- nccl/x.x.x : 使用する nccl version に合ったversionを選択してください

TSUBAME環境の`cudnn/8.9.7`は`cuda/11.8.0`に依存していますので、cuda versionがauto loadされます。
そのため以下のような組み合わせが可能です。

```bash
 1) cuda/11.8.0   2) cudnn/8.9.7   3) nccl/2.20.5
```

## ジョブスケジューリング

利用可能な資源タイプは以下のとおりです。[参考](https://www.t4.gsic.titech.ac.jp/docs/handbook.ja/jobs/#resource_type)

![image](https://github.com/okoge-kaz/TSUBAME-4.0-hands-on/assets/68278821/634eafe6-c55f-4f20-898f-b94d21de4897)

CPUしか利用しないtokenize作業などはcpu_4-160をご利用ください。
また、install作業などでGPUが必要な場合も`gpu_h`または、`gpu_1`をご利用ください。

例えばインタラクティブジョブを取る例は以下のとおりです。

```bash
qrsh -g tgh-NII-LLM -q prior -l gpu_h=1 -l h_rt=1:00:00
```

**注意**: `-q prior`をつけるのをお忘れなく


バッチジョブを投入する場合は、以下のような job scriptを記載します。

```bash
#!/bin/sh
#$ -cwd
#$ -l [資源タイプ] =[個数]
#$ -l h_rt=[経過時間]
#$ -p [プライオリティ]

[module環境のロード]

[プログラム実行]
```

その後、`qsub`コマンドでにてジョブを投入してください。
以下は例です。

```bash
qsub -g tgh-NII-LLM -q prior scripts/tsubame/sample.sh
```

なお、`-q prior`は定額制ジョブを示すオプションです。詳細は[こちら](https://www.t4.gsic.titech.ac.jp/docs/handbook.ja/jobs/#subscription_job)をご覧ください。

