<properties 
	pageTitle="音声と SMS に Twilio を使用する方法 (Java) | Microsoft Azure" 
	description="Azure で Twilio API サービスを使用して通話や SMS メッセージの送信を行う方法について学習します。コード サンプルは Java で記述されています。" 
	services="" 
	documentationCenter="java" 
	authors="devinrader" 
	manager="twilio" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="microsofthelp@twilio.com"/>

# Java で音声および SMS 機能に Twilio を使用する方法

このガイドでは、Azure の Twilio API サービスを使用して一般的なプログラミング タスクを実行する方法を紹介します。電話の発信と Short Message Service (SMS) メッセージの送信の各シナリオについて説明します。Twilio の詳細、およびアプリケーションで音声と SMS を使用する方法については、「[次のステップ](#NextSteps)」を参照してください。

## <a id="WhatIs"></a>Twilio とは
Twilio は、既存の Web 言語およびスキルを使用して音声および SMS アプリケーションの作成を可能にするテレフォニー Web サービス API です。Twilio は、サードパーティ製のサービスです (Azure の機能および Microsoft 製品ではありません)。

**Twilio Voice** を使用すると、アプリケーションで音声通話の発着信処理を行うことができます。**Twilio SMS** を使用すると、アプリケーションで SMS メッセージの送受信を行うことができます。**Twilio Client** を使用すると、アプリケーションに (モバイル接続を含む) 既存のインターネット接続を使用した音声通信を組み込むことができます。

## <a id="Pricing"></a>Twilio の料金および特別プラン
Twilio の料金については、[Twilio の料金に関するページ][twilio_pricing]でご確認ください。Azure ユーザーには、[特別プラン][special_offer] として、1,000 件のテキストまたは 1,000 分の着信通話相当の無料クレジットが用意されています。この特別プランにサインアップする、または詳細を確認するには、[http://ahoy.twilio.com/azure][special_offer] を参照してください。

## <a id="Concepts"></a>概念
Twilio API は、アプリケーションに音声および SMS 機能を提供する REST ベースの API です。クライアント ライブラリはさまざまな言語で用意されています。言語の一覧については、[Twilio API ライブラリに関するページ][twilio_libraries]を参照してください。

Twilio API の主要な側面として、Twilio 動詞と Twilio Markup Language (TwiML) が挙げられます。

### <a id="Verbs"></a>Twilio 動詞
API では、Twilio 動詞を使用します。たとえば、**&lt;Say&gt;** 動詞は、メッセージを音声で返すことを Twilio に指示します。

Twilio 動詞の一覧を次に示します。

* **&lt;Dial&gt;**: 呼び出し元を別の電話に接続します。
* **&lt;Gather&gt;**: 電話キーパッドに入力された数字を収集します。
* **& lt;Hangup & gt;**: 通話を終了します。
* **&lt;Play&gt;**: 音声ファイルを再生します。
* **&lt;Pause&gt;**: 何も行わずに指定された秒数待機します。
* **&lt;Record&gt;**: 呼び出し元の声を録音し、声が録音されたファイルの URL を返します。
* **&lt;Redirect&gt;**: 通話または SMS の制御を別の URL の TwiML に転送します。
* **&lt;Reject&gt;**: Twilio 番号への着信通話を拒否します。課金はされません。
* **&lt;Say&gt;**: テキストを音声に変換して返します。
* **&lt;Sms&gt;**: SMS メッセージを送信します。

### <a id="TwiML"></a>TwiML
TwiML は、Twilio 動詞に基づいた XML ベースの命令のセットで、通話または SMS をどのように処理するかを Twilio に通知します。

たとえば、次の TwiML は、テキスト **Hello World** を音声に変換します。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

アプリケーションで Twilio API を呼び出す場合は、API パラメーターの 1 つである URL によって TwiML 応答が返されます。開発用には、Twilio から提供される URL を使用して、アプリケーションで使用する TwiML 応答を提供することができます。また、独自に URL をホストして、TwiML 応答を生成することもできます。別のオプションとして、**TwiMLResponse** オブジェクトを使用することもできます。

Twilio の動詞と属性、および TwiML の詳細については、[TwiML に関するページ][twiml]を参照してください。Twilio API の詳細については、[Twilio API に関するページ][twilio_api]を参照してください。

## <a id="CreateAccount"></a>Twilio アカウントを作成する
Twilio アカウントを取得する準備ができたら、[Twilio のサインアップ ページ][try_twilio]でサインアップします。無料アカウントで始め、後でアカウントをアップグレードすることができます。

Twilio アカウントにサインアップすると、アカウント ID と認証トークンが発行されます。Twilio API を呼び出すには、この両方が必要になります。自分のアカウントが不正にアクセスされないように、認証トークンを安全に保管してください。アカウント ID と認証トークンは、[Twilio アカウント ページ][twilio_account]の **[ACCOUNT SID]** フィールドと **[AUTH TOKEN]** フィールドでそれぞれ確認できます。

## <a id="create_app"></a>Java アプリケーションの作成
1. Twilio JAR を入手し、Java のビルド パスと WAR デプロイ アセンブリに追加します。[https://github.com/twilio/twilio-java][twilio_java] で、GitHub のソースをダウンロードして独自の JAR を作成するか、ビルド済み JAR (依存関係あり/なし) をダウンロードすることができます。
2. JDK の **cacerts** キーストアに Equifax Secure Certificate Authority 証明書と MD5 フィンガープリント 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 が格納されていることを確認します (シリアル番号は 35:DE:F4:CF、SHA1 フィンガープリントは D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A)。これは、[https://api.twilio.com][twilio_api_service] サービスの証明機関 (CA) 証明書であり、Twilio API の使用時に呼び出されます。JDK の **cacerts** キーストアに正しい CA 証明書が格納されているかどうかを確認する方法については、「[証明書を Java CA 証明書ストアに追加する方法][add_ca_cert]」を参照してください。

Java 用 Twilio クライアント ライブラリを使用する手順の詳細については、「[Azure 上の Java アプリケーションで Twilio を使用して通話する方法][howto_phonecall_java]」で確認できます。

## <a id="configure_app"></a>Twilio ライブラリを使用するアプリケーションの構成
コードのソース ファイルの先頭に、アプリケーションで使用する Twilio のパッケージまたはクラスの **import** ステートメントを追加できます。

Java ソース ファイルの場合:

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Java Server Page (JSP) ソース ファイルの場合:

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
使用する Twilio のパッケージまたはクラスによって **import** ステートメントは異なる場合があります。

## <a id="howto_make_call"></a>方法: 発信通話する
次のコードでは、**CallFactory** クラスを使用して発信通話を行う方法を示しています。このコードは、Twilio から提供されるサイトも使用して、Twilio Markup Language (TwiML) 応答を返します。コードを実行する前に、**From** および **To** の電話番号の値を置き換えて、Twilio アカウントの **From** の電話番号を確認します。

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

**CallFactory.create** メソッドに渡されるパラメーターの詳細については、[http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] を参照してください。

既に説明したように、このコードは Twilio から提供されるサイトを使用して、TwiML 応答を返します。代わりに独自のサイトを使用して TwiML 応答を返すことができます。詳細については、「[方法: 独自の Web サイトから TwiML 応答を返す](#howto_provide_twiml_responses)」を参照してください。

## <a id="howto_send_sms"></a>方法: SMS メッセージを送信する
次のコードは、**SmsFactory** クラスを使用して SMS メッセージを送信する方法を示しています。試用アカウントで SMS メッセージを送信できるように、**From** の番号として **4155992671** が Twilio から提供されます。コードを実行する前に、Twilio アカウントの **To** の番号を確認する必要があります。

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
**SmsFactory.create** メソッドに渡されるパラメーターの詳細については、[http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms] を参照してください。

## <a id="howto_provide_twiml_responses"></a>方法: 独自の Web サイトから TwiML 応答を返す
アプリケーションで Twilio API の呼び出しをインスタンス化する場合 (たとえば、**CallFactory.create** メソッドを使用した場合)、Twilio は TwiML 応答を返すことが想定されている URL にユーザーの要求を送信します。前の例では、Twilio から提供される URL [http://twimlets.com/message][twimlet_message_url] を使用しています(TwiML は Web サービスで使用するように設計されており、ブラウザーで表示できます。たとえば、[http://twimlets.com/message][twimlet_message_url] をクリックすると、空の **<Response>** 要素が表示されます。もう 1 つの例として、[http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] をクリックすると、**<Say>** 要素を格納している **<Response>** 要素が表示されます。

Twilio から提供される URL を使用する代わりに、HTTP 応答を返す独自の URL サイトを作成できます。HTTP 応答を返すサイトは任意の言語で作成できます。このトピックでは、JSP ページで URL をホストすることを前提としています。

次の JSP ページでは、通話時の TwiML 応答で "**Hello World**" というテキストが読み上げられます。

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

次の JSP ページでは、複数の言葉と間合いのポーズを含む TwiML 応答が返され、Twilio API バージョンと Azure ロール名に関する情報が読み上げられます。


    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

**ApiVersion** パラメーターは、SMS 要求ではなく Twilio 音声要求に使用できます。Twilio 音声および SMS 要求に使用できる要求パラメーターを確認するには、<https://www.twilio.com/docs/api/twiml/twilio_request> と <https://www.twilio.com/docs/api/twiml/sms/twilio_request> をそれぞれ参照してください。**RoleName** 環境変数は Azure デプロイの一部として使用できます(カスタム環境変数を追加して **System.getenv** から取得できるようにする場合は、[その他のロール構成設定に関するページ][misc_role_config_settings]の環境変数に関するセクションを参照してください)。

TwiML 応答を提供するように JSP ページを設定したら、**CallFactory.create** メソッドに渡される URL として JSP ページの URL を使用します。たとえば、Azure ホステッド サービスにデプロイされた MyTwiML という名前の Web アプリケーションがあり、JSP ページの名前が mytwiml.jsp である場合、次のコード例に示すように URL を **CallFactory.create** に渡すことができます。

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

TwiML で応答するための別の方法は **TwiMLResponse** クラスを使用することです。このクラスは **com.twilio.sdk.verbs** パッケージに含まれています。

Azure 上の Java で Twilio を使用する方法の詳細については、「[Azure 上の Java アプリケーションで Twilio を使用して通話する方法][howto_phonecall_java]」を参照してください。

## <a id="AdditionalServices"></a>方法: その他の Twilio サービスを使用する
ここに示す例以外にも、Twilio が提供する Web ベースの API を使用して、Azure アプリケーションからその他の Twilio 機能を利用することができます。詳細については、[Twilio API に関するドキュメント][twilio_api_documentation]を参照してください。

## <a id="NextSteps"></a>次のステップ
これで、Twilio サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

* [Twilio に関するセキュリティ ガイドラインのページ][twilio_security_guidelines]
* [Twilio に関する方法とコード例のページ][twilio_howtos]
* [Twilio のクイック スタート チュートリアルのページ][twilio_quickstarts] 
* [GitHub 上の Twilio に関するページ][twilio_on_github]
* [Twilio に関するサポートへの連絡のページ][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]: https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart

<!---HONumber=Oct15_HO3-->