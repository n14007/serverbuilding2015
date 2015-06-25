## 6-0    




* AWSのコマンドインターフェースをsudo apt-get install awscliで取得


## 6-1



* インスタンス作成

* AWSのコンピューティングのEC2からインスタンスの作成→ Amazon Linux選択し作成

* デフォルトゲートウェイの確認(172.16.40.10)

* Access Key IDとSecret Access Keyを先生から受け取る

* aws configureを実行しAccessKeyとSecretAccessKeyを入力→ region nameをap-northeast-1に設定→ output formatをjsonに設定

* chmod 400 n14007.pemを実行

* ssh -i n14007.pem ec2-user@IPアドレス(インスタンスのIPアドレスを使用)

* sudo ansible-playbook -i hosts(インベントリファイル) -u ec2 playbook.yml --private-key n14007.pem(自分のpemファイル)を実行

* Ansibleを使ってwordpressが起動できるか確認


* セキュリティグループの作成

* インスタンス→ セキュリティグループ→ インバウンドの編集→ ルールの追加→ 
http選択

* イメージの作成

* 自分のインスタンスを右クリック→ イメージ作成→ イメージ名・イメージ説明を記入し作成

* AMIを作成し２つのマシーンが起動されているのとコピーされているのを確認


##  6-2 




* 他人の作ったサーバーからwordpressを起動

* インスタンス作成→ AWS marketplaceを選択→ 検索欄からAMIMOTOを検索→ WordPress porered by AMIMOTO(HHVM)を選択し作成

* AMIMOTOのwordpessを起動してちゃんと開けるか確認

##  6-3 Route53

* Hosted Zoneを作成

*  AWSサービス→ Route53→ DNS ManagementCreate→ Hosted Zone→　Create Record Set

* Section5-1で作ったzoneファイルの内容をコピペ

##  6-4 S3

* バケットを作成

* 端末でaws s3 mb s3://[bucket_name]

* aws s3 cp ./[file_name] s3://[bucket_name]

* aws s3 cp s3://[bucket_name]/[file_name] ./[ダウンロード場所指定]

* アップロード確認
