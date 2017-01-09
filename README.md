# aws-lambda-ansible
AWS Lambdaのローカル開発環境です。

基本的に[ServerlessFramework](https://github.com/serverless/serverless)での開発を前提としています。
以下がインストールされているミドルウェアの一覧です。

- Node(v4.3.2)
- serverless(1.5.0)

[ServerlessFramework](https://github.com/serverless/serverless)に関しては、開発がある程度安定するまでは最新版をインストールします。

## 時刻同期がズレている事が原因で起こるトラブル

```serverless deploy``` 等を実行した際に下記のようなエラーが表示される事があります。

```text
ServerlessError: ServerlessError: Signature not yet current: 20160316T092850Z is still later than 20160316T092236Z (20160316T091736Z + 5 min.)
```

サーバの時刻が大きくズレている時に発生します。

### Chronyが起動していない場合

下記のコマンドでChronyが起動しているか確認を行います。

```bash
sudo systemctl status chronyd
```

下記のように表示されれば、正常に起動しています。

```text
● chronyd.service - NTP client/server
   Loaded: loaded (/usr/lib/systemd/system/chronyd.service; enabled; vendor preset: enabled)
   Active: active (running) since 月 2017-01-09 10:58:12 JST; 7min ago
  Process: 23196 ExecStartPost=/usr/libexec/chrony-helper update-daemon (code=exited, status=0/SUCCESS)
  Process: 23192 ExecStart=/usr/sbin/chronyd $OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 23194 (chronyd)
   CGroup: /system.slice/chronyd.service
           └─23194 /usr/sbin/chronyd
```

もし起動していない場合は ```systemctl start chronyd``` でChronyを起動させて下さい。

※基本的にAnsibleで自動起動を行うようにしていますので、起動していないという事はないと思います。

### ChronyとNTPサーバの同期が遅れている

以下のコマンドで同期状況を確認します。

```bash
sudo chronyc sources
```

`*`が付いているサーバが時刻同期を行っているサーバになります。

```text
210 Number of sources = 1
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^* ntp-a3.nict.go.jp             1   6    17    36   -113us[ -135us] +/- 3257us
```

同期が遅れている事で時刻がズレている場合は強制的に以下のコマンドで同期を行います。

```bash
sudo chronyc -a makestep
```

```date```コマンドで時刻を確認すると同期が出来ている事が確認出来るかと思います。

```
2017年  1月  9日 月曜日 11:19:22 JST
```
