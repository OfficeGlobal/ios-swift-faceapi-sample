---
page_type: sample
products:
- office-365
- ms-graph
languages:
- swift
extensions:
  contentType: samples 
  technologies:
  - Microsoft Graph
  services:
  - Office 365
  - Users
  platforms:
  - iOS
  createdDate: 9/8/2016 11:27:52 AM
---
# Microsoft Cognitive Services と iOS 版 Graph SDK Sample

このサンプルでは、iOS アプリで [iOS 版 Microsoft Graph SDK](https://github.com/microsoftgraph/msgraph-sdk-ios) と [Microsoft Cognitive Services Face API](https://www.microsoft.com/cognitive-services/en-us/face-api) を使用する方法を説明します。
ユーザーは、デバイスに保存されている写真をローカルに選択するか、Microsoft Exchange または Outlook に保存されているユーザー プロフィールから写真を選択できます。このサンプルでは、Face API を使用して写真の個人を検出し、特定します。

サンプル コードでは、その方法を示します。

- Microsoft Graph を使用して、サインインしたユーザーの Office 365 ディレクトリからユーザー プロフィール画像を取得します。
- [個人] グループを作成し、[個人] グループに個人を追加し、[個人] グループをトレーニングし、顔を検出し、顔を特定します。

![PhotoSelection](/readme-images/photoSelection.png) ![PhotoIdentification](/readme-images/photoIdentification.png)

## 前提条件

- [Xcode](https://developer.apple.com/xcode/downloads/) バージョン 10.2.1
- 依存関係マネージャーとしての [CocoaPods](https://guides.cocoapods.org/using/using-cocoapods.html) のインストール。
- Microsoft の職場または学校のアカウント。Office 365 アプリのビルドを開始するために必要なリソースを含む [Office 365 Developer サブスクリプション](https://profile.microsoft.com/RegSysProfileCenter/wizardnp.aspx?wizid=14b845d0-938c-45af-b061-f798fbb4d170&lcid=1033)にサインアップできます。

    > 注:このサンプルは、ユーザーが、Microsoft Exchange で設定されている、またはユーザーの Outlook プロファイルに適用されているプロフィール画像付き組織アカウントを持っていることに依存しています。いずれかの場所にプロフィール画像が存在しない場合は、サンプルが画像を取得しようとするとエラーが表示されます。

- Cognitive Services 用のサブスクリプション キー。[Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services) により提供されるサービスを確認してください。Face API 用のキーを必ず追加してください。後述の「**Xcode でこのサンプルを実行する**」のセクションの手順を実行する場合は、このキー値を使用する必要があります。

## アプリを登録して構成する

1. ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)に移動し、**個人用アカウント** (別名:Microsoft アカウント) または**職場または学校のアカウント**を使用してログインします。

1. 左側のナビゲーションで [**Azure Active Directory**] を選択し、次に [**管理**] で [**アプリの登録**] を選択します。

1. **[新規登録]** を選択します。[**アプリケーションの登録**] ページで、次のように値を設定します。

    - [**名前**] を [`Swift Face API Sample`] に設定します。
    - [**サポート対象のアカウントの種類**] を [**任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント**] に設定します。
    - **[リダイレクト URI]** で、ドロップ ダウンを **[パブリック クライアント (モバイルとデスクトップ)]** に変更し、値を `msauth.com.microsoft.ios-swift-faceapi-sample://auth` に設定します。

1. [**登録**] を選択します。**[Swift Face API Sample]** ページで、**[アプリケーション (クライアント) ID]** の値をコピーして保存し、次の手順に移ります。

## Xcode でこのサンプルを実行する

1. このリポジトリのクローンを作成するか、ダウンロードします。
1. CocoaPods を使用して SDK の依存関係をインポートします。このサンプル アプリには、プロジェクトに pod を取り込む podfile が既に含まれています。**ターミナル**からプロジェクト ルートに移動して次を実行するだけです:

    ```Shell
    pod install
    ```

1. **ios-swift-faceAPIs-with-Graph.xcworkspace** を開きます。
1. **Application/ApplicationConstants.swift** ファイルを開きます。`YOUR CLIENT ID` をアプリ登録のアプリケーション ID に置き換えます。
1. `ENTER_SUBSCRIPTION_KEY` を Face API キーに置き換えます。
1. `YOUR_FACE_API_ENDPOINT` を Face API Cognitive Services のエンドポイントに置き換えます。詳細については、「[Azure ドキュメント](https://docs.microsoft.com/azure/cognitive-services/face/quickstarts/curl#face-endpoint-url)」を参照してください。
1. サンプルを実行します。職場のアカウントに接続/認証するように求められ、Office 365 の資格情報を入力する必要があります。認証が完了したら、写真のセレクター コントローラーに移動して、特定する個人を識別する写真を選択します。

## 目的のコード

### グラフ

このサンプルには、2つの Microsoft Graph の呼び出しが含まれています。いずれも /Graph の下の **Graph.swift** ファイル にあります。

1. ユーザーのディレクトリを取得する

    ```swift
    func getUsers(with completion: (result: GraphResult<[MSGraphUser], Error>) -> Void) {
        graphClient.users().request().getWithCompletion {
            (userCollection: MSCollection?, next: MSGraphUsersCollectionRequest?, error: NSError?) in
        ...
            }
        }
    }
    ```

2. ユーザー プロファイル (写真の値) を取得する

    ```swift
    func getPhotoValue(forUser upn: String, with completion: (result: GraphResult<UIImage, Error>) -> Void) {
        graphClient.users(upn).photoValue().downloadWithCompletion {
            (url: NSURL?, response: NSURLResponse?, error: NSError?) in
        ...
        }
    }
    ```

### Cognitive Services - Face API

このサンプルでは、Microsoft Cognitive Services Face API を使用して顔を検出し、特定する基本的な方法を示します。詳細については、[Microsoft Face API](https://www.microsoft.com/cognitive-services/en-us/face-api/documentation/overview) にアクセスしてください

一からの顔を特定するためのコードおよび関連する関数は、/CognitiveServices の下の **FaceAPI.swift** ファイルおよび /Controllers の下の **FaceApiTableViewController.swift** ファイルにあります。

コードの手順は次のとおりです。

1. [個人] グループを作成します。[個人] グループの名前は、**FaceApiTableViewController.swift** ファイルに表示されます。
2. 新しい [個人] グループで個人を作成します。

   (顔をアップロードし、トレーニングする前に、グループ内で個人を作成する必要があります。)
3. 個人の顔をアップロードします。
4. [個人] グループをトレーニングしまう。
5. トレーニングの完了を確認・待機します。

   これは、わずか数秒で完了します。完了するまでポーリングしてから、次の手順に進みます。
6. 顔を検出します。
7. [個人] グループで顔を識別します。

> 注:このサンプルでは、トレーニングに1枚の写真を使用します。実際のシナリオでは、正確さを高めるには、複数の写真を使うことをお勧めします。また、このサンプルでは、グループ内の個人だけでなく、個人グループも作成します。削除する場合は、「API の[削除](https://dev.projectoxford.ai/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395245)」を参照してください。

## 質問とコメント

Microsoft Graph SDK プロフィール画像のサンプルに関するフィードバックをお寄せください。質問や提案は、このリポジトリの「[問題](https://github.com/microsoftgraph/ios-swift-faceapi-sample/issues)」セクションで送信できます。

Microsoft Graph 開発全般の質問については、「[Stack Overflow](http://stackoverflow.com/questions/tagged/Office365+API)」に投稿してください。質問やコメントには、必ず [Office365] と [MicrosoftGraph] のタグを付けてください。

## 投稿

プル要求を送信する前に、[投稿者のライセンス契約](https://cla.microsoft.com/)に署名する必要があります。投稿者のライセンス契約 (CLA) を完了するには、ドキュメントへのリンクを含むメールを受信した際に、フォームから要求を送信し、CLA に電子的に署名する必要があります。

このプロジェクトでは、[Microsoft オープン ソース倫理規定](https://opensource.microsoft.com/codeofconduct/) が採用されています。詳細については、「[倫理規定の FAQ](https://opensource.microsoft.com/codeofconduct/faq/)」を参照してください。また、その他の質問やコメントがあれば、[opencode@microsoft.com](mailto:opencode@microsoft.com) までお問い合わせください。

## 著作権

Copyright (c) 2016 Microsoft.All rights reserved.
