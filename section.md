#section 0
### VirtualBox のインストール

1. [VirturlBox公式サイト](https://www.virtualbox.org/wiki/Linux_Downloads)に移動する。
2. ubuntu14.0のAMD64をダウンロードする。
3. ダウンロードしたファイルを端末を開いてdpkgコマンドを使ってインストールする。
   sudo dpkg -i virtualbox-4.3_4.3.26-98988~Ubuntu~raring_amd64.deb

### Vagrantのインストール

1.  [Vagrant公式サイト](http://www.vagrantup.com/downloads)に移動する。
2.  LINUX(DEB)の64ビットをダウンロードする。
3.  ダウンロードしたファイルを端末を開いてdpkgコマンドを使ってインストールする。
    sudo dpkg -i vagrant_1.7.2_x86_64.deb


# section1-1 基本サーバーの構築

###CentOS 7 Minimal ISOファイルのダウンロード

1.  [CentoOSの公式サイト](http://www.centos.org/download/)に移動する
2.  CentOS-7-x86_64-Minimalのファイルをダウンロードする

###VirtualBoxにCentOS7(64bit)の仮想マシンを作成

1.  Oracle VM VirtualBox マネージャーの『新規』をクリック。

2.  名前とオペレーションシステム画面で次の設定をおこない『続ける』をクリック  
    ･名前:任意の名前を入力  
    ･タイプ:Linuxを選択  
    ･バージョン:Red Hat(64bit)を選択  

3.  メモリーサイズ画面で仮想マシンに割り当てる容量を指定(1024MBを指定)
    し『続ける』をクリック

4.  ハードドライブ画面で仮想ハードドライブを作成すを選択し『作成』をクリック
5.  ハードドライブタイプの画面でVDIを選択し『続ける』をクリック  

6.  物理ハードドライブにあるストレージ画面で可変サイズを選択し『続ける』を    
    クリック  

7.  ファイルの場所とサイズ画面で仮想ハードドライブファイルの名前とサイズを指定し
   『作成』をクリック。以下のように設定した  
    ･名前:手順2で入力した名前(centos7)を設定した  
    ･サイズ:8GBを指定した  
   
###ネットワークの設定

1.  作成した仮想マシンを選択して『設定』をクリック
2.  設定画面でネットワークを選択しアダプターの画面を
    開き割り当てを以下のようにした  
    ･アダプター1:デフォルト(NAT)を設定  
    ･アダプター2:ホストオンリーアダプターを設定 ※  

    ※ 設定して名前がない場合はOracle VM VirtualBox画面のファイルの環境設定をク
    リックしネットワーク画面のホストオンリーネットワークにある追加アイコンを
    クリックし'vboxnet0'を追加する

3.  設定画面で『ストレージ』を選択し以下のことを行う
    ･ストレージツリーからコントロールにある「空」を選択  
    ･属性のCD/DVDドライブにあるCDアイコンをクリック  
    ･仮想CD/DVDデイスクファイルでCentOS-7-x86_64-Minimal-1503.isoを選択  

###CentOS 7 x86_64 のインストール

1.  作成した仮想マシンの電源を入れる
2.  Centos7の画面がでてくるのでInstall CentOS7を選択し、Enterキーを押す
3.  WELCOME TO CENTOOS7画面が表示されるのでインストール時に使用する言語を
   『日本語』を選択し、右下の『続行』をクリック

4.  クリックするとインストール概要画面が表示されるので以下のことを設定する  
    ･キーボード:英語(US)  
    ･インストール先:デフォルト  

5.  インストールの概要を設定し終えたら,画面右下のインストール開始をクリック  
6.  Centos7のインストール設定画面でrootパスワードとユーザー作成が表示れる以下
    の設定をおこなう。  
    ･rootパスワード:任意のパスワードを設定  
    ･ユーザーの作成:任意のユーザ名(s14001)とパスワードを設定  

7.  インストールが完了したら、画面右下の『再起動』をクリックする。  

###ネットワークアダプター1/2の設定とSSHの確認

1.  作成した仮想マシンを起動する
2.  ログインしてパスワードとユーザ名を入力する
3.  コマンドsuを入力しrootでログインする

4.  ネットワークアダプター1/2の設定のために以下のファイルを次のように編集する  
    viで/etc/sysconfig/network-script/ifcfg-enp0s3を開き次のように入力する  
    ONBOOT = yes
   
    viで/etc/sysconfig/network-script/ifcfg-enp0s8を開き次のように入力する  
    ONBOOT = yes  
       
    それぞれの編集が終わったらそれぞれwqを入力しファイルを保存終了する

###SSH接続の確認

Ubuntuの端末からsshで以下のコマンドを入力し仮想マシンに接続する  
   ･ssh s14001@192.168.56.101 ※  
   ※@マーク以下のアドレスは仮想サーバーの端末を開き
   コマンドip aでアドレスを確認する

###インストール後の設定

1.  yumやwget,profileのproxyを以下のように設定する  
    ･yum:viで/etc/yum.confを開き次のように追記する  
    ･http_proxy=http://172.16.40.1:8888  
  
2.  wget:viで/etc/wgetrcを開き次のように追記する  
    ･http_proxy=http://172.16.40.1:8888  
    ･https_proxy=http://172.16.40.1:8888  
  
3.  profile:viでetc/profileを開き次のように追記する  
    ･PROXY='172.16.40.1:8888'  
    ･export http_proxy=$PROXY  
    ･export HTTP_PROXY=$PROXY  
    ･export HTTPS_PROXY=$PROXY  
    ･export https_proxy=$PROXY  
    
###アップデート

  コマンドyum updateを実行しアップデートする
  
#1-2 Wordpressを動かす(1)

###Apache HTTP Sever,MySQL,PHPのインストール

1.  インストール前にrebootし再起動したらrootでログインする  
2.  Apache HTTP Sever,MySQL,PHPを以下のコマンドを入力しインストールする  
    ･Apache HTTP Sever  
    `yum install httpd`  
    ･MySQL  
    `yum -y install http://dev.mysql.com/get/mysql-community-release-el
    7-5.noarch.rpm`  
    `yum -y install mysql mysql-devel mysql-server mysql-utilities`  
    ･PHP  
    `yum -y install php-mysql php php-gd php-mbstring`  

###MySQLの編集 
    
    以下のコマンドを入力しmysqlを起動させ、mysqlにログインし、パスワード設定する
    `systemctl start mysql`  
    `mysql -u root`  
    `mysqladmin -u root password your_password`  

1.  mysqlに以下のコマンドを使いLoginする  
    `mysql -u root -p`※  
    ※ ユーザーのログインパスワードは以下のコマンドで設定する  
 
2.  以下のコマンドでデータベース名とユーザ名、パスワード名を作成する  
    データベース名:`CREATE database database_name;`   
    ユーザ名とパスワード:`CREATE USER 'user_name'@'localhost' IDENTIFIED BY 'your_password';`  
 
3.  作成したユーザに作成したデータベースの以下のコマンドを入力して
    操作権限を付与する  
    `GRANT ALL PRIVILEGES ON database_name.* TO 'your_name'@'localhost';`  
 
4.  Mysqlからログアウトする  
 
###wordpressのインストール

1.  以下のコマンドでインストールし、zipファイルを解凍する  
    `wget https://ja.wordpress.org/wordpress-4.2.2-ja.zip`  
    `unzip wordpress-4.2.2-ja.zip`  
 
2.  次のコマンドでwordpressを次のディレクトリに移動させ,wordpress
    ディレクトリに移動する  
    `mv wordpress /var/www/html/.`  
    `cd /var/www/html/wordpress`  
 
3.  次のコマンドでwp-config-sample.phpファイルをwp-config.phpにコピーする  
    `cp wp-config-sample.php wp-config.php`  
 
4.  viでwp-config.phpを開きmysqlで設定したデータベース名,ユーザー名,  パ
    スワードを編集する  
 
5.  コマンドviで/etc/sysconfig/selinuxを開き  
    `SELINUX = disabled`  
 
###wordpressの動作

1.  以下のコマンドでapacheを起動し、firewallを無効にする  
    apache:`systemctl start httpd`  
    firewwall:`systemctl stop firewalld`   
 
2.  Google Chromeのブラウザーで以下のアドレスを入力し表示されるか確認する  
    http://192.168.56.101/wordpress/wp-admin/install.php    
    http://vicsfactory.com/?p=52  
    https://tamosblog.wordpress.com/2012/06/12/centos_ssh/  
    http://qiita.com/wynnkengeofu/items/89d2454fd92b9cfd932f  