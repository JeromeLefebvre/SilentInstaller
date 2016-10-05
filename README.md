# OSIsoft製品のサイレントインストール例のご紹介

## 事前

PI Systemの管理者は、多くのマシンにPIの製品をインストールしなくてはならず、多くの場合、自動インストール（サイレントインストール）を利用すると、効率よく作業できます。
そのサイレントインストールに使用するsilent.iniの書き方は、資料がとても少ないため、今回、その例を作成いたしました。

## 作業と環境の条件

今回はPI Buffer SubsystemはインストールせずにPISDK2016の64ビットのみをサイレントインストールにてインストールします。
(*注意：平常は32ビットと64ビットの両方をインストールします。短い例を作成のため、64ビットのみのサイレントインストールさせて頂きます。*)

環境の条件：

1. PI-TestとPI-Mainという二つPI Data　Archiveがあり、規定サーバーはPI-Mainになり、「Production」というPI-Mainの別名があります。
1. Domainはositestです。
1. PI Data Archiveとの接続は5450の規定ポートを利用します。
1. PI Buffer Subsystemをインストールしない。
1. 事前条件の.Net 4.6がすでにインストールされているものとします。

## KST_INI_FILEの準備

KST(Known Server Table)はこのマシンに登録されているPI Data Archiveの一覧リストになります。
PI SDKにインストールすると、このリストにいくつかのPI Data Archiveを登録できます。
登録するために、「KST\_INI\_FILE」の作成が必要です。

### KST\_INI\_FILE

```ini
[NUM_SERVERS]
; PI Data Archiveの数
NUM = 2
[PI_SERVER]
; それぞれのPI Data Archiveの名前
1 = PI-Main
2 = PI-Test
; PI Data Archiveのネットワークのパス
[PI_PATH]
1 = PI-Main.ositest.com
2 = PI-Test.ositest.com
; ポートを記入したい場合は、[PI_PORT]を使えますが規定の5450ポートの利用しているので、設定の必要がありません。
; 規定のPI Data Archiveは1です。
[PI_DEFAULT_SERVER]
１ = TRUE
; それぞれのPI Data Archiveのアリアスです。
[PI_ALIAS]
1 = Production
```

## SILENT.INI

新規のインストールではSETUPKIT、NUMSETUPMODULES、SETUPMODULES、COMMANDLINEの四つのモジュールがあります。

### SETUPKIT

```ini
[SETUPKIT]
;　インストールの表示名前
; 下記にあるNAMEとDISPLAYNAMEのパラメーターはインストールのログにでます。
NAME = SetupPISDK
DISPLAYNAME = PI Software Development Kit (PI SDK) 2016 64 bit
; サイレントインストールを実行すると下記の四つの設定はすべてTRUEに設定する
SUPPRESSCOMPLETIONMESSAGE = TRUE
SUPPRESSPROGRESSMESSAGE = TRUE
SUPPRESSHEADERMESSAGE = TRUE
SUPPRESSDIALOGS = TRUE
```

### NUMSETUPMODULES

```ini
[NUMSETUPMODULES]
; 下記にモジュールの数
NUM=4
```

### SETUPMODULES

setup.exeは、以下のモジュールを使用し、COMMANDLINEの下にリストされている与えられたパラメータを使用してインストーラーを実行します。

```ini
[SETUPMODULES]
; 下記の三つのmsiのファイルは事前条件のアプリケーションになります。
1 = MSRuntimes\MSRuntimes.msi
2 = VS2008Redistrib\MSVC9SP1x64Redistrib
3 = VS2015Redistrib\MSRuntimesVS2015u2_x64
;PI SDKの64ビットのインストーラー
4 = pisdk\PISDK_x64.msi
```

### COMMANDLINE

下記の四つのインストーラーではマシンを再起動しないで済むので、
「REBOOT=Suppress」と「/norestart」のパラメーターを使い、無駄な再起動が起きないようにインストールします。

```ini
[COMMANDLINE]
; MSRuntimes
1 = ALLUSERS=1 REBOOT=Suppress /qn
; 2008 SP1 Runtimes (64-bit)
2 = /q /norestart
; 2015 update 2 Runtimes (64-bit)
3 = /q /norestart
; PI SDK (64-bit)
; KST_INI_FILEのパラメーターはKSTのファイルのフルパスです。
; INSTALLDIRは%pihome64%のフォルダーになります。
4 = ALLUSERS=1 REBOOT=Suppress /qn INSTALLDIR="C:\Program Files\PIPC" IMPORT_KST=1 KST_INI_FILE="c:\kst.ini"
```

## インストーラーを実行します

kst.iniのファイルをKST\_INI\_FILEに保存し、
PISDK\_2016\_が展開されたフォルダーにsilent.iniのファイルを入れ替え後に、下記のコマンドを実行します。

    silent.bat -install

実行後、インストーラーの結果が表示されます。
無事にインストールしたかどうか確認します。

### 他の製品のサイレントインストール

PI SDKに含まれているsilent.iniのファイルにはPI Buffer Subsystemなど、他の製品の設定例もあるので、参照になります。

下記のKB（英)もあります。

1. [KB01034 - How to Silently Install OSIsoft Software](https://techsupport.osisoft.com/Troubleshooting/KB/KB01034)
1. [3044OSI8 - How to perform a silent install of PI ProcessBook](https://techsupport.osisoft.com/Troubleshooting/KB/3044OSI8)
1. [3045OSI8 - How to perform a silent install of PI DataLink](https://techsupport.osisoft.com/Troubleshooting/KB/3045OSI8)