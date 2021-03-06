##########
サーバ要件
##########

`PHP <http://php.net/>`_ バージョン 5.6 以上を推奨します。

5.3.7 でも動作しますが、そのような古いバージョンを使わないことを強く
アドバイスします。潜在的なセキュリティおよびパフォーマンス上の問題や
機能不足があるためです。

ほとんどの Web アプリケーションプログラミングにおいてデータベースが必要です。
現在サポートされているデータベース:

  - MySQL（5.1+）、*mysql*（廃止予定）、 *mysqli* そして *pdo* ドライバを利用
  - Oracle、 *oci8* と *pdo* ドライバを利用
  - PostgreSQL、 *postgre* と *pdo* ドライバを利用
  - MS SQL、 *mssql*、 *sqlsrv*（バージョン 2005 以上のみ）そして *pdo* ドライバを利用
  - SQLite、 *sqlite*（バージョン 2）、 *sqlite3*（バージョン 3）そして *pdo* ドライバを利用
  - CUBRID、 *cubrid* と *pdo* ドライバを利用
  - Interbase/Firebird、 *ibase* と *pdo* ドライバを利用
  - ODBC、 *odbc* と *pdo* ドライバを利用（ODBC は実際には抽象化レイヤーです）
