---

copyright:
  years: 2017, 2019

lastupdated: "2019-03-06"

keywords: IBM Cloud, monitoring

subcollection: cloud-monitoring

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}
{:important: .important}
{:note: .note}



# 通知の削除
{: #delete_notifications}

[アラート API](https://console.bluemix.net/apidocs/940-ibm-cloud-monitoring-alerts-api?&language=node#introduction){: new_window} を使用して、{{site.data.keyword.monitoringshort}} サービスから通知を削除することができます。
{:shortdesc}

[{{site.data.keyword.Bluemix_notm}}](/docs/services/cloud-monitoring/qa?topic=cloud-monitoring-cli_qa#login) の地域にログインした後で、以下の手順を実行して通知を削除します。


## 手順 1: セキュリティー・トークンの取得
{: #step1_delete_notifications}

UAA トークン、IAM トークン、または API キーを使用することができます。 

以下のいずれかの方法を選択して、セキュリティー・トークンを取得します。
	
* UAA トークンを取得するには、[{{site.data.keyword.Bluemix_notm}} CLI を使用した UAA トークンの取得](/docs/services/cloud-monitoring/security?topic=cloud-monitoring-auth_uaa#uaa_cli)をご覧ください。
* IAM トークンを取得するには、[{{site.data.keyword.Bluemix_notm}} CLI を使用した IAM トークンの取得](/docs/services/cloud-monitoring/security?topic=cloud-monitoring-auth_iam#auth_iam)をご覧ください。
* API キーを取得する場合は、
[API キーの取得](/docs/services/cloud-monitoring/security?topic=cloud-monitoring-auth_api_key#auth_api_key)を参照してください。
	
例えば、IAM トークンを使用するには、以下のコマンドを実行します。

```
ibmcloud iam oauth-tokens
```
{: codeblock}
	
このコマンドの結果は以下のようになります。
	
```
IAM token:  Bearer djn.._l_HWtlNTibmcloudslBXs0qjBI9GqCnuQ
    UAA token:  Bearer eyJhbGciOiJIUz..Ky6vagp3k_QcIcKJ-td83qXhO5Uze43KcplG6PzcGs8
```
{: screen}
	
次に、以下のようにして変数 *Token* をエクスポートします。
	
```
export Token="djn.._l_HWtlNTibmcloudslBXs0qjBI9GqCnuQ"
```
{: screen}
	
**注:** このトークンは *Bearer* を除外します。
	

## 手順 2: スペース ID の取得 
{: #step2_delete_notifications}

この手順は、スペース・ドメインで使用可能な通知を削除する場合にのみ適用されます。

スペース GUID を取得するには、以下のコマンドを実行します。
	
```
ibmcloud iam space SpaceName --guid
```
{: codeblock}
	
ここで、*SpaceName* は、通知を定義するスペースの名前です。 
	
例えば、*dev* という名前のスペースの GUID を取得するには、以下のコマンドを実行します。
	
```
ibmcloud iam space dev --guid
```
{: screen}
	
このコマンドの結果は以下のようになります。
	
```
667fadfc-jhtg-1234-9f0e-cf4123451095
```
{: screen}
	
次に、変数 *Space* をエクスポートします。 この GUID には、スペースを識別するための *s-* の接頭部が付いている必要があります。
	
```
export Space="s-667fadfc-jhtg-1234-9f0e-cf4123451095"
```
{: screen}

	

## 手順 3: 通知のリスト
{: #step3_delete_notifications}


以下の cURL コマンドを実行して、スペース・ドメイン内の通知をリストします。

```
curl -H "X-Auth-Scope-Id: ${Space}" -H “X-Auth-User-Token: Auth_Type ${TOKEN}" -XGET METRICS_ENDPOINT/v1/alert/notifications

```
{: screen}

以下の cURL コマンドを実行して、アカウント・ドメイン内の通知をリストします。

```
curl -H "X-Auth-User-Token: apikey ${TOKEN}" -XGET METRICS_ENDPOINT/v1/alert/notifications
```
{: screen}

ここで、
	
* *X-Auth-User-Token* は、{{site.data.keyword.Bluemix_notm}} UAA トークン、IAM トークン、または API キーを渡すパラメーターです。
	
* *Auth_Type* は、トークンまたは API キーのタイプを定義する接頭部です。 以下のリストに、有効値 *apikey*、*iam*、または *uaa* の概要を示します。詳細は次のとおりです。

    * *apikey* は、トークンが API キーであることを識別します。
	* *iam* は、指定されたトークンが IAM 生成トークンであることを識別します。
	* *uaa* は、指定されたトークンが UAA 生成トークンであることを識別します。
	
* *X-Auth-Scope-Id* は、スペースの GUID を渡すパラメーターです。 この GUID には、スペースを識別するための *s-* の接頭部が付いている必要があります。 
	
* Token は、UAA 認証トークン、IAM 認証トークン、または API キーです。
	
* Space は、スペースの GUID です。 
	
* METRICS_ENDPOINT はサービスへのエントリー・ポイントを示しています。 各地域の URL は異なります。 地域ごとのエンドポイントのリストを取得するには、
[エンドポイント](/docs/services/cloud-monitoring?topic=cloud-monitoring-send_retrieve_metrics_ov#endpoints)を参照してください。


## 手順 4: 通知の削除
{: #step4_delete_notifications}
  

以下の cURL コマンドを実行して、スペース・ドメイン内の通知を削除します。

```
curl -H "X-Auth-Scope-Id: ${Space}" -H “X-Auth-User-Token: Auth_Type ${TOKEN}" -XDELETE METRICS_ENDPOINT/v1/alert/notification/{name} 
```
{: screen}

以下の cURL コマンドを実行して、アカウント・ドメインから通知を削除します。

```
curl -H "X-Auth-User-Token: apikey ${TOKEN}" -XDELETE METRICS_ENDPOINT/v1/alert/notification/{name} 
```
{: screen}

	
ここで、
	
* *X-Auth-User-Token* は、{{site.data.keyword.Bluemix_notm}} UAA トークン、IAM トークン、または API キーを渡すパラメーターです。
	
* *Auth_Type* は、トークンまたは API キーのタイプを定義する接頭部です。 以下のリストに、有効値 *apikey*、*iam*、または *uaa* の概要を示します。詳細は次のとおりです。

    * *apikey* は、トークンが API キーであることを識別します。
	* *iam* は、指定されたトークンが IAM 生成トークンであることを識別します。
	* *uaa* は、指定されたトークンが UAA 生成トークンであることを識別します。
	
* *X-Auth-Scope-Id* は、スペースの GUID を渡すパラメーターです。 この GUID には、スペースを識別するための *s-* の接頭部が付いている必要があります。 
	
* Token は、UAA 認証トークン、IAM 認証トークン、または API キーです。
	
* Space は、スペースの GUID です。 
	
* METRICS_ENDPOINT はサービスへのエントリー・ポイントを示しています。 各地域の URL は異なります。 地域ごとのエンドポイントのリストを取得するには、
[エンドポイント](/docs/services/cloud-monitoring?topic=cloud-monitoring-send_retrieve_metrics_ov#endpoints)を参照してください。

* *name* は、削除する通知の名前です。
	