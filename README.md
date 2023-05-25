# Bundle 登録プログラム サブアカウント対応

このプログラムは、Twilio Japan に対して、Bundle の登録を自動化するものです。  
マスターアカウントの AccountSid、AuthToken を設定していただければ、保有しているサブアカウント（マスターを含む）を自動的にクロールして、Bundles がないすべてのサブアカウントに登録します。  
**[注意]**  
有効期限が設定されていなくて、かつすでに承認されている Bundle があるアカウントには登録をしません。  
ただし、レビュー中の Bundle がある場合は新しい Bundle を作成します。そのため、レビュー中に再度実行すると複数の Bundle を申請することになります。

## 前提条件

本プログラム以下の条件でのみ動作します。

- 法人での申請
- 必要な書類を用意してあること
- Node.js がインストールされていること

必要書類については、[こちらの記事](https://qiita.com/mobilebiz/items/83eba66e7ed9ca339501)を確認してください。個人の証明書には、運転免許証とマイナンバーカードの組み合わせにのみ対応しています（パスポートは非対応）。  
なお、本プログラムはリセラー事業者にも対応しています。

## 準備

- Bundle 申請書を**PDF 形式**で準備してください。リセラーとして申請する場合は、申請書の最後に TNUP をマージしてください。TNUP については、[こちらの記事](https://qiita.com/mobilebiz/items/c63e9ca4f102bc46cf25)を参考にしてください。
- 謄本ならびに委任状（必要な場合のみ）を**PDF 形式**で準備してください（サイズは 5MB 以内）。謄本は発行日から 6 ヶ月以内のものに限ります。
- 申請者の運転免許証のコピー、ならびにマイナンバーカード（表面のみ）のコピーを**JPEG 形式**で準備してください（サイズは 5MB 以内）。 スマホのカメラで撮影したものでも大丈夫ですが、免許証やマイナンバーカードのみが写るように周りの画像は削除してください。

## インストール

適当なフォルダに移動し、GitHub リポジトリを取得します。

```sh
git clone https://github.com/twilioforkwc/create-bundle.git
cd bundle-create
npm install
mv .env.example .env
```

## 書類の格納

Bundle 申請書、謄本、委任状（必要な場合）、運転免許証、マイナンバーカードの各ファイルを、images フォルダにコピーしておきます。

## 環境変数の準備

`.env`ファイルをエディタで開き、以下の項目をすべて申請者の内容に置き換えます。

| 項目名                   | 内容                                                                                                                   |
| :----------------------- | :--------------------------------------------------------------------------------------------------------------------- |
| ACCOUNT_SID              | Twilio アカウントのマスターアカウントの AccountSid（AC から始まる文字列）                                              |
| AUTH_TOKEN               | AccountSid に対応する AuthToken                                                                                        |
| NUMBER_TYPE              | national もしくは toll-free を指定                                                                                     |
| RESELLER                 | リセラーとして申請をだすには、true、通常の申請は false のまま                                                          |
| BUSINESS_NAME            | 登記簿謄本に記載されいている商号を記載通りに（社名に「・」が入っている場合は、API がエラーを出すので削除してください） |
| BUSINESS_DESCRIPTION     | 登記簿謄本に記載されいている会社法人等番号（例：0100-01-012345）                                                       |
| BUSINESS_ADDRESS         | 登記簿謄本に記載されている本店住所の町村名と丁目番地を記載されている通りに（例：大手町一丁目１番地１号）               |
| BUSINESS_CITY            | 登記簿謄本に記載されている本店住所の市区名を記載されている通りに（例：千代田区）                                       |
| BUSINESS_REGION          | 登記簿謄本に記載されている都道府県名を記載されている通りに（例：東京都）                                               |
| BUSINESS_POSTAL_CODE     | 会社の郵便番号をハイフンなしで（例：1000001）                                                                          |
| BUSINESS_ISO_COUNTRY     | 会社の住所が日本の場合は JP のままで                                                                                   |
| BUSINESS_FIRST_NAME      | 代表者の名前（名）を登記簿謄本に記載されている通りに（日本語 OK）                                                      |
| BUSINESS_LAST_NAME       | 代表者の名前（姓）を登記簿謄本に記載されている通りに（日本語 OK）                                                      |
| BUNDLE_APPLICATION_FILE  | Bundle 申請書の PDF ファイル（リセラーの場合は、最後に TNUP をマージすることを忘れずに）                               |
| CORPORATE＿REGISTRY_FILE | 登記簿謄本の PDF ファイル名                                                                                            |
| POWER_OF_ATTORNEY_FILE   | 委任状の PDF ファイル名（なしの場合は未指定）                                                                          |
| FIRST_NAME               | 申請者の名前（名）を免許証に記載されている通りに（日本語 OK）                                                          |
| LAST_NAME                | 申請者の名前（姓）を免許証に記載されている通りに（日本語 OK）                                                          |
| BIRTH_DATE               | 申請者の誕生日を YYYY-MM-DD 形式で                                                                                     |
| STREET                   | 住所（町丁目と建物名）を免許証に記載されている通りに                                                                   |
| CITY                     | 住所（市区町村）を免許証に記載されている通りに                                                                         |
| REGION                   | 住所（都道府県）を免許証に記載されている通りに                                                                         |
| POSTAL_CODE              | 郵便番号をハイフンなしで                                                                                               |
| ISO_COUNTRY              | 日本の場合は JP のままで                                                                                               |
| DRIVERS_LICENSE_FILE     | 免許証の画像ファイル名（拡張子は.jpg）                                                                                 |
| MYNUMBER_CARD_FILE       | マイナンバーカードの画像ファイル名（拡張子は.jpg）                                                                     |
| EMAIL                    | 申請者のメールアドレス（審査結果が通知されます）                                                                       |

## プログラムの実行

```sh
npm start

...最初にテストコードが走って`.env`の内容をチェックします。
...テストがすべてPASSすると申請が始まります。

アカウント名 [AC6c061ba1b2b7a938ae2dbf64816fxxxx]==============
BUba40a23d409b7cadf48dd835ca0120a4 => pending-review
BU1728e396de39b604c0cfba77e5b18c29 => draft
BUcabb988722cd47f1a3363a028c6b31be => draft
🐞 Business Address created. AD1d83b176779275e81835594b8bb4f293
🐞 User Address created. ADb5320e697d943cf6c5d1f16168b04452
🐞 Bundle created. BU9672a4cd0b189ded690377f9d150acc1
🐞 End-User created. IT6609096bc23621e3e7ac817803c85972
🐞 Corporate Registry Document uploaded. RD926bd7b35834ad108e44dd39e616f8dd
🐞 Bundle Application Document uploaded. RD20b3d59e71b997c27f55ad1315cf5163
🐞 Power of Attorney Document uploaded. RD84ecae69831ccc2557db19b1862876b7
🐞 Drivers License Document uploaded. RD9182f35576de463ab95c5e2038a58634
🐞 MyNumber Card Document uploaded. RD2e20c8312e445d3da699ae60809651b4
🐞 End-User assigned. BVa980a05ace23e03f64b47903790e14f6
🐞 Corporate Registry Document assigned. BVbf0c3210a1d1f88f6e57c184ff17a6bc
🐞 Bundle Application Document assigned. BVd4608ef7d4e18d24aa2e335adf93db4b
🐞 Power Of Attorney Document assigned. BV220fcc049afa0aa013a6d657c47020e9
🐞 Drivers License Document assigned. BV74fd0278894ed56f592de9b255bd1e1a
🐞 MyNumber Card Document assigned. BV1b15af9293c1877efc9bb5fd1f8083aa
🐞 Bundle requested. BU9672a4cd0b189ded690377f9d150acc1
AccountSid: AC6c061ba1b2b7a938ae2dbf64816xxxx BundleSid: BU9672a4cd0b189ded690377f9d150acc1

メールの宛先：numbers-regulatory-review@twilio.com
件名：Please review Bundles.
本文：
Hello Twilio Regulatory Compliance Team,

Please review the following Bundles.
Best regards,


BU9672a4cd0b189ded690377f9d150acc1
```

上記のように表示されれば成功です。また、レビューを受け取った旨のメールが Twilio 届きます。  
最後に表示されたのは、Bundle を早く確認してもらうためのメールの雛形になります。この文面で`numbers-regulatory@twilio.com`にメールを送信すれば、比較的早くレビューをしてくれます。

審査が通れば STATUS が`Twilio Approved`になります。  
すでに番号を保有している人は、審査済みの Bundle と既存の番号の紐づけをしておく必要があります。
