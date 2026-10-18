# WireGuard ラボ: 1 台の Ubuntu VM の中で 2 つの拠点を VPN トンネルでつなぐ

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md)

[![check](https://github.com/mitsurutech/lab-wireguard/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-wireguard/actions/workflows/check.yml)

授業でそのまま使える **WireGuard ラボ**です。学校が生徒一人ひとりに専用のマシンとブラウザー上のターミナルを提供する [MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)のラボです。生徒は自分専用の Ubuntu VM を受け取ります。その中には、ゲートウェイとホストを 1 台ずつ持つ 2 つの小さな拠点が作られていて、どちらのプライベートネットワークも知らない「インターネット」のリンクでつながっています。生徒は各ゲートウェイで **WireGuard の鍵ペア**を生成し、`wg0.conf` を書き、**VPN トンネル**を立ち上げて 2 つのネットワークをそのトンネル経由にし、一方の拠点のホストからもう一方の拠点のホストに ping が通るようにします。生徒のコンピューターには何もインストールしません。

ゲートウェイには専用のマシンが必要ですが、生徒の VM は 1 台です。そのため、すべてのホストとゲートウェイは**ネットワーク名前空間**（network namespace）で、すべてのリンクは仮想ケーブルのペアです。実際のコマンドがそのまま動くのが肝心な点です: `wg genkey`、`wg pubkey`、`wg show`、`wg-quick`、`ip route`、`tcpdump`。

## ラボ

| ラボ | ファイル | 生徒がすること | ヘルプページ |
| --- | --- | --- | --- |
| 2 つの拠点の間の WireGuard トンネル | [`wireguard.lab.yaml`](wireguard.lab.yaml) | 各ゲートウェイで鍵ペアを作り、秘密鍵を秘密に保ち、設定を書き、トンネルを立ち上げ、2 つの LAN をそのトンネル経由にし、インターネットのリンク上の暗号化されたパケットを観察し、再起動しても動き続けるようにする | [WireGuard ラボ](https://mitsurutech.co.uk/ja/help/lab-wireguard) |

ラボは、何をするかを説明し、答えは一切書かない README を生徒のホームフォルダーに置きます。**チェック**もあります。これは、提出された VM を教員が中身を読まなくても採点するスクリプトです。新しく起動し直した保存済みマシンのコピーの上で実行されるので、見るのは生徒が `wg0.conf` に保存した内容だけです。実際の状態が正しいときだけ合格します: 両方のゲートウェイで最近のハンドシェイクがあること、ping のトラフィックが `wg0` を通っていること、インターネットのリンク上には UDP しかなくプライベートアドレスが見えないこと、秘密鍵が他の誰にも読めないこと、プライベートネットワークをインターネットのリンクに送るルートがないこと。2 つのネットワークの間に単純なスタティックルートを引いただけでは合格しません。

## ファイルの中身

`wireguard.lab.yaml` は、プラットフォームがそのまま実行する **VM ラボ定義**の全体です:

| キー | 内容 |
| --- | --- |
| `cpus`、`memory`、`disk` | 生徒一人ひとりの VM のサイズ: 1 CPU、1024 MB、10 GB |
| `open` | ブラウザーで開くポート: なし。ラボはターミナルです |
| `setup` | 各生徒の VM が初めて起動したときに root として一度だけ実行されるスクリプト: Ubuntu のアーカイブから `wireguard-tools` と `tcpdump` をインストールし、`wglab` コマンドとラボの README を書きます |
| `check` | 採点スクリプト。新しく起動し直した保存済みディスクのコピーの上で root として実行され、終了ステータスが結果で、`0` なら合格です |

インストールされる `wglab` コマンド: `wglab status` はマシンの一覧を表示し、`wglab shell gwA` はゲートウェイの中でシェルを開き、`wglab down` に続けて `wglab up` を実行すると、ラボを作り直して、保存したファイルからトンネルを再び立ち上げます。

## プラットフォームでの使い方

**クラスの課題として設定するには:**

1. モジュールで課題を追加し、**Ubuntu VM ラボ**のカードを選びます。
2. サンプルを押すか、ここにあるファイルの内容を貼り付けます。サンプルを使うとテキストが置き換わり、元に戻すで自分のテキストに戻せます。
3. 公開して、まず自分のコピーで試します。生徒にはそれぞれ専用の VM があり、セッションをまたいで保持されます。
4. 通常の課題と同じように配布します。提出されたら、クラス一覧の生徒の行でチェックを実行します。保存済みマシンのコピーが起動し（保存し直されることはありません）、合格したかどうかと出力内容が表示されます。

残りは、ヘルプマニュアルの [Ubuntu VM ラボのガイド](https://mitsurutech.co.uk/ja/help/labs)と[サンプルラボのページ](https://mitsurutech.co.uk/ja/help/sample-labs)で説明しています。

学校のアカウントはまだですか？ 50 人までの生徒なら[最初の 1 年は無料](https://mitsurutech.co.uk/ja/pricing)で、[アカウント作成](https://mitsurutech.co.uk/ja/signup)に必要なのはメールアドレスだけです。

## ファイルのチェック

push のたびに、`.github/workflows/check.yml` が、ファイルが上の 5 つのキーを持つ有効な YAML であること、`setup` と `check` がそれぞれプラットフォームの 16 KB の上限に収まっていることを確認します。VM は起動しません。それはプラットフォーム自身のマシンで行われます。

## ラボでできないこと

VM が届くのは Ubuntu のパッケージアーカイブ、PyPI、npm、GitHub だけで、それ以外には届きません。そのため、ラボをインターネット上のホストに向けることはできません。VM は 1 台なので、2 つの拠点はその中に作られ、間の「インターネット」は仮想リンクです。

## このラボについて

[Mitsuru Technologies](https://mitsurutech.co.uk) が [MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)のために作りました。Python、Java、Go、Node、SQL、Linux をブラウザーで学べる、学校向けのプラットフォームで、コンテナラボ、VM ラボ、GitHub からデプロイする Web アプリ、自動で採点されるクイズがあります。アカウントなしでコードを試せる[プレイグラウンド](https://mitsurutech.co.uk/ja/playground)もあります。

同じところが作っている他のラボ: [lab-netlab](https://github.com/mitsurutech/lab-netlab)、[lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop)、[lab-dvwa](https://github.com/mitsurutech/lab-dvwa)、[lab-webgoat](https://github.com/mitsurutech/lab-webgoat)、[lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin)、[lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea)。

## ライセンス

この作品は [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) でライセンスされています。Mitsuru Technologies Ltd のクレジットを表示し、商用に使わず、これをもとに作ったものを同じライセンスで共有するなら、学習と授業のために共有・改変できます。全文は `LICENSE` ファイルにあります。

[WireGuard](https://www.wireguard.com) は GPL-2.0 でライセンスされており（ツールと、Ubuntu カーネルのモジュール）、VM の起動時に Ubuntu のアーカイブからインストールされます。WireGuard のものは何もここでは配布していません。
