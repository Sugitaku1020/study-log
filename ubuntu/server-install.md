# Ubuntu Serverのインストール記録

## 1. インストール対象

- **OS**: Ubuntu Server 26.04.1 LTS

## 2. 使用環境

- **CPU**: AMD Ryzen 5 PRO 3400GE with Radeon Vega Graphics
- **RAM**: 16 GB
- **SSD**: 128 GB
- **Network**: 有線LAN
- **PC**: Lenovo ThinkCentre M75q-1

---

## 3. インストール手順

### 3.1 Ubuntu ServerのインストールUSBを作成する

[Ubuntu公式サイト](https://jp.ubuntu.com/download)からUbuntu ServerのISOファイルをダウンロードする。

Windowsでは、Rufusなどのツールを使用してISOファイルをUSBメモリへ書き込む。

---

### 3.2 BIOS / UEFIを確認する

BIOS / UEFI設定画面は、PCの電源を入れた直後に `F1` を連打することで開くことができる。

#### 確認項目

- **Boot Mode**  
  `Startup -> Boot Mode` を確認し、`UEFI Only` であることを確認した。

- **Secure Boot**  
  `Enabled` であることを確認した。

- **SSD**  
  `SAMSUNG MZALQ128HBHQ-000L1` が認識されていることを確認した。

- **USB Boot**  
  `Startup -> Primary Boot Sequence` に `USB HDD` が存在することを確認した。

- **Storage Mode**  
  `Configure SATA as` が `AHCI` であることを確認した。

#### UEFIについて

UEFIは、PCの電源投入直後に動作し、主に以下の処理を行う仕組みである。

- CPUやメモリなどのハードウェアの初期化
- SSDやUSBなどの起動デバイスの検出
- どのデバイスからOSを起動するかの決定
- OSの起動処理への引き渡し

従来のBIOSの後継にあたる仕組みで、現在のPCではUEFIが一般的に使用されている。

#### Storage Modeについて

Storage Modeは `AHCI` であることを確認した。

AHCIは、SATA接続のストレージをOSから制御するための一般的な方式であり、Linuxでも広く利用されている。

今回はUbuntu Serverからストレージを正常に認識できる構成であるため、設定を変更せずそのまま使用した。

---

### 3.3 USBからUbuntu Serverを起動する

Boot Menuは、PCの電源を入れた直後に `F12` を連打することで開くことができる。

Boot Menuでは以下を確認した。

- USBメモリが `USB HDD: Generic Mass Storage` として認識されている
- UEFI起動用の `UEFI: Generic Mass Storage ...` が表示されている
- 内蔵M.2 SSD `SAMSUNG MZALQ128HBHQ-000L1` が認識されている

Ubuntu ServerはUSBメモリのUEFI起動項目を選択して起動した。

起動後、`Try or Install Ubuntu Server` を選択した。

`Try or Install Ubuntu Server` を選択すると、USBメモリ内のLinuxカーネルやインストーラ環境が起動し、ハードウェアの初期化・検出処理が行われる。

この時点では、まだ内蔵SSDへのUbuntu Serverのインストールは開始されていない。

---

### 3.4 Ubuntu Serverの初期設定

#### 設定内容

| 項目 | 設定 |
|---|---|
| Language | English |
| Keyboard Layout | Japanese |
| Keyboard Variant | Japanese |
| Base for the installation | Ubuntu Server |
| Network interface | `enp2s0f0` |
| Proxy | 未設定 |
| Mirror server | デフォルト |
| Storage layout | Guided |
| Installation target | `SAMSUNG MZALQ128HBHQ-000L1` |
| Use an entire disk | Yes |
| Ubuntu Pro | 使用しない |
| Install OpenSSH Server | Enabled |
| Featured Server Snaps | 未選択 |

#### Language

表示言語として `English` を選択した。

Ubuntu Serverではエラーメッセージやログを英語のまま確認できるため、問題発生時に検索しやすいという利点がある。

#### Keyboard Layout / Variant

- **Layout**: Japanese
- **Variant**: Japanese

Layoutは国・地域ごとの基本的なキーボード配列を指定する。

Variantは、そのLayoutの中に存在する細かな配列の違いを指定する。

今回は日本語JISキーボードを使用しているため、両方とも `Japanese` を選択した。

#### Base for the installation

通常の `Ubuntu Server` を選択した。

`Ubuntu Server (minimized)` は、標準構成よりも最初から導入されるパッケージを減らした軽量構成であり、必要なパッケージを後から追加することを前提としている。

今回はUbuntu Serverの基本的な環境を確認することが目的であるため、標準の `Ubuntu Server` を使用した。

#### Network interface

有線LANインターフェース `enp2s0f0` が認識されていることを確認した。

ネットワーク設定にはDHCPを使用し、ルーターからIPアドレスを自動取得する。

#### Proxy

自宅ネットワークではプロキシサーバーを使用していないため、未設定とした。

#### Mirror server

デフォルト設定を使用した。

MirrorはUbuntuのパッケージやアップデートを配布するコピーサーバーである。

通常はUbuntu側が適切なミラーサーバーを自動設定するため、特定のミラーサーバーは指定しなかった。

#### Storage layout

`Guided` を選択した。

Guided storage layoutでは、Ubuntu側にストレージ構成を自動で任せることができる。

インストール対象のSSDを指定すると、OSの起動や動作に必要なEFI領域、`/boot`、ルートディレクトリ `/` などが自動的に構成される。

今回は、

`SAMSUNG MZALQ128HBHQ-000L1`

をインストール対象として選択し、SSD全体をUbuntu Server用として使用した。

#### Ubuntu Pro

Ubuntu Proは使用しなかった。

今回はUbuntu Serverのインストール練習が目的であり、長期的な追加セキュリティサポートは必要ないためである。

#### OpenSSH Server

OpenSSH Serverを有効化した。

OpenSSH Serverは、別のPCからUbuntu ServerへSSHを使用してリモートログインできるようにするための機能である。

今後、Windows PCなどからUbuntu Serverへ接続することを想定しているため、有効化した。

#### Featured Server Snaps

追加のSnapパッケージは選択しなかった。

Featured Server Snapsでは、Ubuntu Serverでよく使われるソフトウェアをインストール時に追加できる。

今回はUbuntu Serverの基本構成を確認することを目的としているため、追加パッケージは導入しなかった。

必要なソフトウェアは、インストール完了後に個別に導入する方針とした。
