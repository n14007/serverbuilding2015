### section0




VirtualBoxのインストール

* [公式サイト](https://www.virtualbox.org/wiki/Linux_Downloadsr)からubuntu13.04のAMD64をダウンロード

* 端末で sudo dpkg -i virtualBox-4.3_4.3.26-98988-Ubuntu-raring_amd64.debをダウンロード

* 端末からvirtualboxの起動

## Vagrantのインストール



* [公式サイト](http://www.vagrantup.com/downloads)からvagrantをダウンロード

* sudo dpkg -i vagrant_1.7.2_x86_64.debでVagrantをインストール

* 端末でvagrant -vを実行してvagrantコマンドが使えることを確認


====

### section1




* http://www.centos.org/download/でCentOS7をダウンロードしてコマンドでvirtualboxを開き新規でタイプ：Linux　バージョン：Red Hat(64bit)を選択
* メモリサイズ：1G　ストレージ容量：8Gに設定
* ネットワークのとこにあるネットワークアダプタ２の割り当てをホストオンリーアダプターに設定
* 名前をvboxnet0に設定
* 作成後起動してインストール画面中にインストール先を変更せず完了しrootパスワードの設定とユーザーを作成
* ユーザー作成のとき「このユーザーを管理者にする」を選択
* インストールが終わったら再起動
* /etc/sysconfig/network-scriptにあるicfg-enp0s3のファイル(sudo vi /etc/sysconfig/network-script/ifcfg-enp0s3)にONBOOT=noと書いてるのでyesに変更→ 保存終了。　




## yum wget profile の設定




* yum
* /etcのyum.confをルート権限で開きhttp_proxy=http://172.16.40.1:8888を書く
* wget
* /etcのwgetrcをルート権限で開きhttp_proxy=http://172.16.40.1:8888とhttps_proxy=http://172.16.40.1:8888を書く
* profile
* /etcのprofileをルート権限で開きPROXY='172.16.40.1:8888'とexport http_proxy=$PROXYとexport HTTP_PROXY=$PROXYとexport HTTPS_PROXY=$PROXYとexport https_proxy=$PROXYを書く
* それぞれ保存終了したらsudo yum updateコマンドを実行
* virtualboxを再起動
## wordpress 動作確認



* yum install httpdとyum -y install http://dev.mysql.com/get/mysql-community-release-el 7-5.noarch.rpmとyum -y install mysql mysql-devel mysql-server mysql-utilitiesとyum -y install php-mysql php php-gd php-mbstringを実行してApache,MYSQL,PHPをインストールし動作環境をつくる
* systemctl start mysql→ mysql -u root→ mysqladmin -u root password your_password→ exitの順に実行しMYSQLにログインするためのEnterpassword入手
* mysql -u root -pでもう一度ログインしCREATE database database_name;→ CREATE USER 'user_name'@'localhost' IDENTIFIED BY 'your_password';を実行してデータベース作成を行う
* GRANT ALL PRIVILEGES ON database_name.* TO 'your_name'@'localhost';を実行し権限を追加後exit
## wordpress インストール



* wget https://ja.wordpress.org/wordpress-4.2.2-ja.zip→ unzip wordpress-4.2.2-ja.zip　インストール→ 解凍
* mv wordpress /var/www/html/でwordpressを移動させcdでwordpressの移動先にとぶ
* cp wp-config-sample.php wp-config.php cpコマンドでwp-config-sample.phpをwp-congif.phpにコピー コピーしたファイルをルート権限で開きMYSQLのデータベース変更を行う
* /etc/sysconfig/selinuxをルート権限で開き「SELINUX= 」→ 「SELINUX=disabled」を書く
*sudo systemctl strat httpdでapachサーバー起動→ sudo systemctl stop firewalldでfirewallを止める
* ブラウザでhttp://192.168.56.101/wordpress/wp-admin/install.phpとhttp://vicsfactory.com/?p=52とhttps://tamosblog.wordpress.com/2012/06/12/centos_ssh/とhttp://qiita.com/wynnkengeofu/items/89d2454fd92b9cfd932fを開く




======



### section2




## centOS7のイメージ登録・Vagrantの初期設定




* vagrant box add CentOS7 CentOS7-1503.boxコマンドでCentOS7を登録

* mkdir ディレクトリ名で作業用ディレクトリを作りそこに移動

* vagrant init CentOS7でvagrantfile作成→ vi vagrantfileで開き設定を行う

* 開いたvagrantfileにconfig.vm.network "private_network", ip:"xxx.xxx.xxx.xxx"とconfig.vm.box = "base"の"base"をCentOS7と書く

* vagrant reload でVagrantfileの設定を更新

* vagrant upで起動しssh xxx.xxx.xxx.xxx（ipアドレス）でサーバに接続

* vi /etc/yum.confで開きyumのプロキシ設定を行う

* http_proxy=http://172.16.40.1:8888

* vi /etc/profileを開きプロキシ設定を行う
* PROXY='172.16.40.1:8888'
* export http_proxy=$PROXY
* export HTTP_PROXY=$PROXY
* export HTTPS_PROXY=$PROXY
* export https_proxy=$PROXY

* 再起動しyum updateを行う→ yum install wgetでwgetをインストールしwgetrcの設定を行う

* vi /etc/wgetrcで開き記入
* http_proxy=http://172.16.40.1:8888
* https_proxy=http://172.16.40.1:8888

* yum install nginx / yum -y install php-mysql php-common php php-cgi php-fpm php-gd php-mbstring ・yum -y mariadb mariadb-sever mariadbをインストール

* php-fpmの設定

* vi /etc/php-fpm.d/www.confを開きphpの設定を行う

* listen = 127.0.0.1:9000→ listen = 127.0.0.1

* user = apache→ user = nginx

* group = apache→ group = nginx

* ;pm = dynamic→ pm = static※ セミコロンは消す

* ;pm.max_children = 50→ pm.max_children = 3

* pm.max_requests = 500

* php-fpmの起動

* cat /etc/passwd|grep nginxでグループがあるか確認

* なかった場合

* groupadd nginx

* getent group nginxと実行するとnginx:x:1001が出てくる

* useradd -s /usr/sbin/nologin -g nginx -d /var/www -c nginx nginx

* systemctl list-units |grep php-fpmで起動確認

* systemctl list-unit-files |grep php-fpmで登録状態確認

* systemctl start php-fpm.serviceでデーモン起動

* systemctl list-units |grep php-fpmで起動確認

* systemctl enable php-fpm.serviceでenableに設定

* systemctl list-unit-files |grep php-fpmで起動確認

* mariadb(mysql)の設定

* yum -y install mariadb mariadb-serverでmariadbをインストール

* systemctl list-units |grep mariadbで起動確認

* systemctl list-unit-files |grep mariadbでデーモンの登録状態確認

* systemctl start mariadb.serviceでデーモンを起動

* systemctl enable mariadb.serviceで登録状態を設定

* systemctl list-unit-files |grep mariadbで登録状態確認

* mysqlの設定

* sudo mysql -u rootを実行して入る

* GRANT SELECT,INSERT,UPDATE,DELETE ON データベース名.* TO ユーザ名@localhost IDENTIFIED BY 'パスワード';

* FLUSH PRIVILEGES;

* exit

* nginxの設定

* rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpmでnginxリポジトリをインストール

* yum -y install nginxでnginxをインストール

* vi /etc/nginx/nginx.confで開きuser nginx;→ user nginx nginx;に変更

* worker_connections 1024;に変更

* http{}の中のaccess_log /var/log/nginx/access.log;とerror_log /var/log/nginx/error.log;に変更

* inclide /etc/nginx/conf.d/*.conf;に変更

* :wqで保存終了しchown -R nginx: /var/log/nginxとchmod +r+w /var/log/nginxを実行しディレクトリの所有者をnginxに変更

* /etc/nginx/conf.d/default.confを開きphpのコメントアウトを消す

* rootディレクトリにしたい場所のURLを記入→ phpのrootディレクトリのURLを$document_root$fastcgi_script_name;に変更

* wordpressのインストール

* wget http://wordpress.org/latest.tar.gzを実行

* tar -xzvf latest.tar.gzを実行

* default.confでrootディレクトリに指定した場所にwordpressを移動

* wordpressに移ってcp wp-config-sample.php wp-config.phpを実行

* コピーしたファイル(wp-config.php)を開きmysqlで設定したデータベースに変更

* systemctl start mariadb→ systemctl start nginx→ systemctl start php-fpm→ systemctl stop firewall

* ブラウザでwordpressを開き確認


## 2-3

* apache2.2インストール

* yum -y install wget gcc openssl openssl-devel

* wget http://ftp.kddilabs.jp/infosystems/apache//httpd/httpd-2.2.29.tar.gz

* tar zxvf ./httpd-2.2.29.tar.gz

* cd ./httpd-2.2.29

* ./configure --enable-suexec

* make

* make install

* apacheの起動

* /usr/local/apache2/bin/apachectl start

* PHPインストール

* sudo yum install libxml2 libxml2-devel

* sudo wget --trust-server-names http://jp2.php.net/get/php-5.5.25.tar.gz/from/this/mirror

* tar zxvf php-5.5.25.tar.gz

* cd php-5.5.25

* ./configure --enable-so

* make

* make install

* ./configure --with-apxs2=/usr/local/apache2/bin/apxs --with-mysql

* make

* make install

* php.ini作成

* cp php.ini-development /usr/local/lib/php.ini

* 作ったファイルを開きmysql.default_socket = /var/lib/mysql/mysql.sockに変更

* vi /usr/local/apache2/conf/httpd.confを実行

* ServerName ユーザー名

* <FilesMatch \.php$>
*   SetHandler application/x-httpd-php
* </FilesMatch>

* index.phpを追加

* <IfModule dir_module>   
*     DirectoryIndex index.html index.php   
* </IfModule>


* データベースの作成

sudo yum install mriadb mariadb-server

sudo systemctl start mariadb.service

* sudo mysql -uroot -p

* GRANT SELECT,INSERT,UPDATE,DELETE ON データベース名.* TO ユーザ名@localhost IDENTIFIED BY 'パスワード';

* FLUSH PRIVILEGES;

* exit

* デーモンの再起動

* sudo  /usr/local/apache2/bin/apachectl restart

* sudo systemctl start mariadb.service

* wordpressをインストール

* wget http://wordpress.org/latest.tar.gzを実行

* tar -xzvf latest.tar.gzを実行

* default.confでrootディレクトリに指定した場所にwordpressを移動

* wordpressに移ってcp wp-config-sample.php wp-config.phpを実行

* コピーしたファイル(wp-config.php)を開きmysqlで設定したデータベースに変更

* wordpress起動確認
