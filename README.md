# OSIsoftの製品のサイレントインストール例のご紹介

## 事前

PI Systemの管理者がよく、多くのマシンにPIの製品を自動にのインストールの作業をしたいですが、silent.iniの書き方の資料がすくないため、下記の例を作成いたしました。

## 作業と環境の条件

バッファリングの機能をインストールせずにPI SDK 2016の64ビットのみをサイレントインストールにてインストールします。

環境の条件：

1. PI-TestとPI-Mainという二つPI Data　Archiveがあり、規定サーバーはPI-Mainになり、「Production」というPI-Mainのアリアスがあります。
1. Domainはositestです。
1. PI Data Archiveとの接続ために5450の規定のポートの利用です。
1. bufferingの機能をインストールしない。
1. 事前条件の.Net 4.6がすでにインストールされています。

## KST_INI_FILEの準備

KST(Known Server Table)はこのマシンに登録されているPI Data Archiveの一覧リストになります。
PI SDKにインストールすると、このリストにいくつかのPI Data Archiveを登録できます。
登録するために、「KST\_INI\_FILE」の作成が必要です。

### KST\_INI\_FILE:

```ini
; PI Data Archiveの数
[NUM_SERVERS]
NUM = 2
; それぞれのPI Data Archiveの名前
[PI_SERVER]
1 = PI_Main
2 = PI_Test
; PI Data Archiveのネットワークのパス
[PI_PATH]
1 = PI_Main.ositest.com
2 = PI_test.ositest.com
; ポートを記入したい場合は、[PI_PORT]を使えますが規定の5450のポートの利用しているので、設定の必要がありません。
; 規定のPI Data Archiveは1です。
[PI_DEFAULT_SERVER]
１ = TRUE - should only be one server which is default
[PI_ALIAS]
1 = Production
```

### SILENT.INI
新規のインストールではSETUPKIT、NUMSETUPMODULES、SETUPMODULES、COMMANDLINEの四つのモジュールがあります。

#### SETUPKIT

```ini
[SETUPKIT]
;　インストールの表示名前
NAME = SetupPISDK
DISPLAYNAME = PI Software Development Kit (PI SDK) 2016 64 bit
; サイレントインストールを実行すると下記の四つの設定はすべてTRUEに設定する
SUPPRESSCOMPLETIONMESSAGE = TRUE
SUPPRESSPROGRESSMESSAGE = TRUE
SUPPRESSHEADERMESSAGE = TRUE
SUPPRESSDIALOGS = TRUE
```

```ini
[NUMSETUPMODULES]
; 下記にモジュールの数
NUM=4
```

```ini
[SETUPMODULES]
; 下記の三つのmsiのファイルは事前条件のアプリケーションになります。
1 = MSRuntimes\MSRuntimes.msi
2 = VS2008Redistrib\MSVC9SP1x64Redistrib.msi
3 = VS2015Redistrib\MSRuntimesVS2015u2_x64.msi

;PI SDK (64-bit)
4 = pisdk\PISDK_x64.msi
```

```ini
[COMMANDLINE]
;MSRuntimes
1 = ALLUSERS=1 REBOOT=Suppress /qn
;2008 SP1 Runtimes (64-bit)
2 = /q /norestart
;2015 update 2 Runtimes (64-bit)
3 = /q /norestart
;PI SDK (64-bit)
4 = ALLUSERS=1 REBOOT=Suppress /qn INSTALLDIR="C:\Program Files\PIPC" IMPORT_KST=1 KST_INI_FILE="kst.ini"
```

### 他の設定

PI SDKに含まれている

リソース：
1．https://techsupport.osisoft.com/Troubleshooting/KB/KB01034

２．
