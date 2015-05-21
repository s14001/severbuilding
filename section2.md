#2-1 .Vagrantを使用したCentOS6.5環境の起動  

###Vagrantで起動できるCentOS6.5のイメージを登録  
1.  以下のコマンドを用いCentOS6.5を登録した  
    `vagrant box add CentOS65 centos65-x86_64-20240116.box --force`  

###Vagrantの初期設定  
1.  作業用ディレクトリを作成し `vagrant init Centos65`を実行し｢Vagrantfile｣  
    を作成した。  

2.  viでVagrantfileを開き ｢config.vm.box = "CentOS65"｣の下に｢config.vm.
    network:private_network, ip:"192.168.56.129"｣と記述し仮想マシンのNIC2  
    にIPアドレスを割り振った。  

3.  [公式サイト](http://nginx.org/en/linux_packages.html#stable)から  
    Cenos6.0のrpmファイルをダウンロードしインストールした。  

#2−2wordpressを動かす  

1.  コマンド`vachant up`で仮想マシンを起動し`vagrant ssh`を用いて仮想マシンに接続した
2.  接続したらyumのproxy設定をし、`yum update`でシステムアップデートした。※
    ※vi /etc/yum/pluginconf.d/fastestmirror.confを開き｢prefer=ftp.riken.jp｣と書き換え
    インストールを早くする

3.  yumを用いNingix,mariadb,php関連ファイルをインストールした。  
    コマンドを以下のようにした  
    ・`yum -y install niginx`   
    ・`yum -y install php-mysql php-common php php-cgi php-fpm php-gd php-mbstring` 
    ・`yum -y mariadb mariadb-sever mariadb`※  
    ※インストール前にetc/yum.repos.d/MariaDB.repo(MariaDB.repoは作成する)に以下を記述する  
    [mariadb]  
    name = MariaDB  
    baseurl = http://yum.mariadb.org/5.5/centos6-amd64  
    gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB  
    gpgcheck=1  

###Ninginx,mariadb,php-fpmの設定  

    1   mariadb
    1-1 mariadbを起動する前にログインパスワードを設定(  
    	mysql_secure_installationを入力)しservice mysql startを入力する  
   
    1-2 ログインしたらcreate database 'name'でデータベース名を入力,  
        grant all privileges on name.* to user@localhost identified by 'pass';でユーザー名パスワードを設定  

    2  php  
    2-1 viで/etc/php-fpm.d/www.conf を開き「user = nginx」,「group = n
        iginx」にする

    3  nginx    
    3-1 viで/etc/nginx/conf.d/default.confを開location / {｝の中のindexを
    	｢index.php｣と記述する.  
    3-2 location ~ \.php$ {}の中のrootは｢/usr/share/nginx/html  
        astcgi_paramは｢$document_root$fastcgi_script_name｣と記述する    

##wordpressの動作
1.  wordpressをwgetでダウンロードし、展開する。#  
    #wgetはyumでインストールし、proxy設定する 
2.  mvでwordpressを/usr/share/nginx/htmlに移動する  
3.  worpressでsqlで設定したユーザー名、パスワード、データベース名を入力する  

4.  以下のコマンドを入力したら、wordpressにgoogle-Chromeでアクセスする  
    service iptables stop  
    service nginx start 
    service php-fpm start  

#2−3wordpressを動かす
1.  2−2のvagrantの初期設定と同じ手順で行う。※  
※ただし2−2のipアドレスを変更して設定する

2.  vagrant sshで起動したら、yumのproxy設定をしwgetをinstallする。  
wgetのproxy設定もする。  


3.  以下のコマンドでapache、phpをダウンロードして展開したディレクトリに移動しインストールする  
    apache       
    1  `wget http://www.apache.org/dist/apr/apr-1.5.2.tar.gz`  
    ->`tar zxvf apr-1.5.2.tar.gz`->` cd apr-1.5.2`->` ./configure`  
    ->`make`->`make install`  
    
    2  `wget http://www.apache.org/dist/apr/apr-util-1.5.4.tar.gz`  
    以降の手順は1と同様にする。  
    ただしapr-utilは`./configure --with-apr=/usr/local/apr`にする    
    
    3  `wget http://www.apache.org/dist/httpd/httpd-2.2.29.tar.gz`  
    以降の手順は1同様にする。  
    
    php  
    [phpの公式サイト](http://php.net/manual/ja/index.php)からダウンロードしVgrantfileのところにcpする。次に仮想サーバーのホームディレクトリに移動させる  
    1.  ファイルは`tar lxvf`で展開し、以降の手順でインストールする  
    `yum install libxml2-devel`  
    ->`./configure --with-apxs2=/usr/local/apache2/bin/apxs --with-mysql`    
    ->`make`->`make install`  
    
    mysql 
    `yum install mysql`でインストールする  


###apache,php,mysqlの設定:  
    apache:
    vi /usr/local/apache2/conf/httpd.confで開き  
    <IfModule dir_module>のところに｢index.php」を追記する  
    次に以下の記述を追加する  
    `<FilesMatch "\.ph(p[2-6]?|tml)$">  
       SetHandler application/x-httpd-php  
    </FilesMatch>  
     
    <FilesMatch "\.phps$">  
       SetHandler application/x-httpd-php-source  
    </FilesMatch>`  

    php  
    1.  以下のコマンドを使いファイルをコピーする   
        `cp php-5.5.25/php.ini-development  /usr/local/lib/php.ini`    
    2.  viで先程コピーした場所を開きファイルを以下のように編集する   
        場所：http://php.net/mysql.default-socket  
        mysql.default_socket =/var/lib/mysql/mysql.sock  

    mysql  
    1.  mysqlをスタートしユーザーでログインしパスワードを設定する  
        `update mysql.user set password=password('root用の任意パスワード') where user = 'root'`  
    2.  mysql -u root -p でログインし、データベース名、ユーザー名、パスワード
        を設定する。  

##wordpressの動作

1.  2-2のwordpressの動作の手順と同様にする※  
    ※wordpressの場所はmvで/usr/local/apache2/htdocs/に移動する  

2.  以下のコマンドを入力したら、wordpressにgoogle-Chromeでアクセスする  
    `service mysqld start`  
    `/usr/local/apache2/bin/apachectl start`  


#2-4ベンチマークをとる

1.  viで/usr/share/nginx/html/wordpress/wp-config.phpを開きdefine('FS_METHOD', 'direct');を追記する
2.  chown -R nginx:nginx /usr/share/nginx/html
3.  wordpressディレクトリに移動し,wp-contentの下にuploadディレクトリを作成する  
    `chomod 777 upload`で所有権を変える

4.  abコマンドをUbuntu側(ホスト側)にインストールしてじっこうする  
5.  以下のコマンドで1,000回リクエストする ※  
    ※2-2で使用した仮想サーバー(nginx)で測定した  
    `ab -c 1000 -n 1000 http://192.168.56.131/wordpress`  
    
    実行した結果以下の値が得られた。  
    Percentage of the requests served within a certain time (ms)  
    50%    109    
    66%    280    
    75%    302    
    80%    308  
    90%    528  
    95%    533  
    98%    534  
    99%    535  
    100%   536  

    次にwordpressにプラグイン(WP Super Cache)インストール、実装し実行して
    同じ条件で測定した。  
    
    以下の結果が得られた  
    Percentage of the requests served within a certain time (ms)
    50%    286  
    66%    498  
    75%    518  
    80%    522  
    90%    967  
    95%    974  
    98%    980  
    99%    1856  
    100%   1858  
    ある程度の改善が確認できた。  