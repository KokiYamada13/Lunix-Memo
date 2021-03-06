# Lunix メモ

- Lunixとは？
    - Operationg Ststemの一つ

- ディレクトリの役割
    - / ファイルシステムの頂点に当たるディレクトリ
        - /etc システム管理用。各種ソフトウェアの設定ファイルを保存
        - /boot rootユーザーのホームディレクトリ
        - /var　システム運用中にファイルサイズが変化するファイルを保存
            - /log システムやアプリケーションのログファイルを保存
        - /bin 一般ユーザー、管理者が使用するコマンドが配置
        - /dev デバイスファイルを保存
        - /user ユーザーが共有するファイルを保存。ユーティリティ、ライブラリ、コマンド等を配置
            - /bin 一般ユーザー、管理者が使用するコマンドが配置
        - /home ユーザーのホームディレクトリが配置

- メモ
    - tabキーで補完してくれる

- コマンド
    - pwd 現在の場所を表す
    - clear 画面をクリアする
    - mkdir ディレクトリを作成
        - -p ナンタラ/ナンタラ　サブディレクトを含んだディレクトリの作成
    - ls ディレクトの中身をリスト表示
        - -l 詳細
        - -a 隠しファイル
    - cd ディレクトリ移動
    - mv ディレクトの移動（例： 移動前/　移動後）やファイルの移動・リネーム
    - rimdir ディレクトリ削除(中身がある場合削除できない)
    - rm ファイルやディレクトリを削除
        - -r
        - -f　矯正コマンド。あまりよくない
    - cp ディレクトリ,ファイルのコピー
    - touch 空ファイル作成
    - vi テキストエディタ
        - :q 終了
        - :q! 保存しないで狩猟
        - :wq 保存して終了
        - i インサートもーど
        - etc 戻る
    - cat ファイルの中身を確認
    - less 大きめのファイルの中身を確認するのによし
    - exit ログアウト
    - useradd ユーザーの作成
    - password パスワードを設定する
    - su ユーザーの切り替え
    - userdel ユーザーの切り替え
    - sudo スーパーユーザー
        - 管理者権限がある
    - reboot システムの再起動
    - shutdown シャットダウン
        -　バリエーションあり
    - history コマンド履歴
    - which コマンドの場所を探してフルパスで探す
    - man コマンドのヘルプ表示
    - コマンド補完はtab
    - chmod アクセス権限の確認
        - -rw-rw-r--
        - ファイル種別
            - -　ファイル
            - d ディレクトリ
            - l シンボリックリンク
        - 権限 モードの数字
            - r　読み取り 4
            - w 書き込み 2
            - x　実行 1
            - -権限なし
        - -R 指定以下のファイル・ディレクトリにも付与したいとき
        - 所有者、所有グループ、その他
    - chown 所有者を変更
    - グループ

## LAMP環境構築
- yum パッケージ管理システム

- EPELリポジトリ
    - RHEL向け追加パッケージ
- Remiリポジトリ
    - mysqlやPHPの新しいバージョン等を集めているリポジトリ

- sudo yum update システムアップデート
- sudo yum install epel-release 
- sudo yum install https://rpms.remirepo.net/enterprise/remi-release-8.rpm
- yum repolist 有効なリポジトリ確認

### Apach
- sudo yum install httpd
    - httpd -v
- sudo systemctl enable httpd 起動時 立ち上がるようにする

- ファイヤーウォールの設定
    - sudo firewall-cmd --permanent --zone=public --add-service=http
    - sudo firewall-cmd --permanent --zone=public --add-service=https
    - sudo firewall-cmd --reload
    - getenforce
    - sudo vi /etc/selinux/configsudo
        - selunixをdisableに設定
    - sudo reboot 再起動
    - ip addr アドレス確認
        - ip a　でもおけ
    - デフォルトでは　ls /var/www/htmlだと思うが、設定ファイル確認　less /etc/httpd/conf/httpd.confで確認。→/DocumentRootで確認
    - ※動作確認1
        - sudi vi /var/www/html/index.html 作成
        - ls -la /var/www/html
        - sudo chown apache:apache /var/www/html/index.htmlで所有者変更

### PHP
- yum list available | grep php71-php-common
- yum --enablerepo=remi-php71 install php php-devel php-mysql php-gd php-mbstring
    - ※centOS8だとどうなる？？ → centOS8 にはremi-php~はない
    - sudo dnf module install -y php:remi-7.4
    - インストールしたらapach再起動　sudo systemctl restart httpd
    - ※動作確認1 で実施

### Mysql8.
- sudo dnf info mysql
- sudo dnf install @mysql:8.0
- sudo systemctl start mysqld
- sudo ststemctl enable mysqld
- sudo cat /var/log/mysqld.log | grep 'temporary password'
- mysql_secure_installation
- sudo vi /etc/my.cnfで　character-set-server = utf8追加
- sudo systemctl restart mysqld
- sudo ststemctl enable mysqld
- systemctl list-unit-files -t service | grep mysqld
#### ユーザー作成
- create user 'mybloguser'@'localhost' identified with musql_native_password by '';
    - だめなら
    - SHOW VARIABLES LIKE 'validate_password%';
    - set global validate_password_length=6;
    - set global validate_password_policy=LOW;
    - set global validate_password.check_user_name = OFF;
- create database wp_myblog;
- grant all privileges on wp_myblog.* to 'mybloguser'@'localhost';
- flush privileges;

### wordpress
- yum list | grep wget
- sudo yum install wget
- wget https://ja.wordpress.org/latest-ja.tar.gz
- sudo tar -zxvf latest-ja.tar.gz -C /var/www/
- sudo chown -R apache:apache wordpress/
- sudo cp /etc/httpd/conf/httpd.conf /etc/httpd/conf/httpd.conf.org
- sudo vi /etc/httpd/conf/httpd.conf

####　※お使いのサーバーの PHP では WordPress に必要な MySQL 拡張を利用できないようです。
- php -m | grep mysql
    - 何も出なかったら、sudo yum install php-mysql
    - メモ：　　　Sl(qK5UakI2KwU3@4%
