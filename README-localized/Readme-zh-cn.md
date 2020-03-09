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
# 适用于 iOS 的包含 Graph SDK 示例的 Microsoft 认知服务

此示例说明如何在 iOS 应用中同时使用[适用于 iOS 的 Microsoft Graph SDK](https://github.com/microsoftgraph/msgraph-sdk-ios) 和 [Microsoft 认知服务 Face API](https://www.microsoft.com/cognitive-services/en-us/face-api)。
用户可以从本地设备或从 Microsoft Exchange 或 Outlook 内存储的用户个人资料中选择照片。该示例使用 Face API 来检测和识别照片中的人物。

示例代码演示了如何执行以下操作：

- 使用 Microsoft Graph 从已登录用户的 Office 365 目录中检索用户个人资料图片。
- 创建人员组、将人员添加到人员组、演练人员组、检测面部并识别面部。

![PhotoSelection](/readme-images/photoSelection.png) ![PhotoIdentification](/readme-images/photoIdentification.png)

## 先决条件

- [Xcode](https://developer.apple.com/xcode/downloads/) 版本 10.2.1
- 安装 [CocoaPods](https://guides.cocoapods.org/using/using-cocoapods.html) 作为依存关系管理器。
- Microsoft 工作或学校帐户。你可以注册 [Office 365 开发人员订阅](https://profile.microsoft.com/RegSysProfileCenter/wizardnp.aspx?wizid=14b845d0-938c-45af-b061-f798fbb4d170&lcid=1033)，其中包含你开始构建 Office 365 应用所需的资源。

    > 注意：此示例要求用户拥有一个组织帐户，其中包含在 Microsoft Exchange 中设置的个人资料图片或在用户的 Outlook 配置文件中应用的个人资料图片。如果两个位置都不存在个人资料图片，则当示例尝试检索图片时，你将看到错误。

- 认知服务的订阅密钥。查看由 [Microsoft 认知服务](https://www.microsoft.com/cognitive-services)提供的服务。确保为 Face API 添加密钥。当你按照以下“**在 Xcode 中运行此示例**”部分中的步骤执行操作时，需要使用该密钥值。

## 注册和配置应用

1. 打开浏览器，并导航到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)，然后使用**个人帐户**（亦称为“Microsoft 帐户”）或**工作/学校帐户**登录。

1. 选择左侧导航栏中的“Azure Active Directory”****，再选择“管理”****下的“应用注册”****。

1. 选择“新注册”****。在“**注册应用程序**”页上，按如下方式设置值。

    - 将“**名称**”设置为 `Swift Face API 示例`。
    - 将“**受支持的帐户类型**”设置为“**任何组织目录中的帐户和个人 Microsoft 帐户**”。
    - 在“**重定向 URI**”中，将下拉列表更改为“**公共客户端(移动和桌面)**”，然后将值设为 `msauth.com.microsoft.ios-swift-faceapi-sample://auth`。

1. 选择“**注册**”。在“**Swift Face API 示例**”页上，复制并保存**应用程序（客户端）ID** 的值，将在下一步中用到它。

## 在 Xcode 中运行此示例

1. 克隆或下载此存储库。
1. 使用 CocoaPods 导入 SDK 依赖项。该示例应用已包含可将 pod 导入项目中的 Podfile。只需从**终端**导航到项目根目录并运行：

    ```Shell
    pod install
    ```

1. 打开 **ios-swift-faceAPIs-with-Graph.xcworkspace**。
1. 打开 **Application/ApplicationConstants.swift** 文件。将 `YOUR CLIENT ID` 替换为应用注册的应用程序 ID。
1. 将 `ENTER_SUBSCRIPTION_KEY` 替换为 Face API 密钥。
1. 将 `YOUR_FACE_API_ENDPOINT` 替换为 Face API 认知服务的终结点。有关详细信息，请参阅 [Azure 文档](https://docs.microsoft.com/azure/cognitive-services/face/quickstarts/curl#face-endpoint-url)。
1. 运行示例。系统将要求你连接到工作帐户并进行身份验证，你还需要提供 Office 365 凭据。通过身份验证后，你将进入照片选择器控制器，以选择要识别的人物以及要从中识别的照片。

## 相关代码

### Graph

此示例包含两个 Microsoft Graph 调用，这两个调用都位于 /Graph 下的 **Graph.swift** 文件中。

1. 获取用户的目录

    ```swift
    func getUsers(with completion: (result: GraphResult<[MSGraphUser], Error>) -> Void) {
        graphClient.users().request().getWithCompletion {
            (userCollection: MSCollection?, next: MSGraphUsersCollectionRequest?, error: NSError?) in
        ...
            }
        }
    }
    ```

2. 获取用户个人资料（照片值）

    ```swift
    func getPhotoValue(forUser upn: String, with completion: (result: GraphResult<UIImage, Error>) -> Void) {
        graphClient.users(upn).photoValue().downloadWithCompletion {
            (url: NSURL?, response: NSURLResponse?, error: NSError?) in
        ...
        }
    }
    ```

### 认知服务 - Face API

此示例显示使用 Microsoft 认知服务 Face API 检测和识别面部的基础知识。有关详细信息，请访问 [Microsoft Face API](https://www.microsoft.com/cognitive-services/en-us/face-api/documentation/overview)

用于从头开始识别面部的代码和相关函数位于 /CognitiveServices 下的 **FaceAPI.swift** 文件和 /Controllers 下的 **FaceApiTableViewController.swift** 文件中。

以下是代码执行的步骤：

1. 创建个人组。你可以在 **FaceApiTableViewController.swift** 文件中找到人员组的名称。
2. 在新人员组中创建人员。

   （必须先在组中创建人员，然后才能上传面部和进行演练。）
3. 上传人员面部。
4. 演练人员组。
5. 查看并等待演练完成。

   这应该只需要几秒钟时间。执行轮询，直到其完成，然后继续执行下一步操作。
6. 检测面部。
7. 识别人员组中的面部。

> 注意：此示例使用单张照片进行演练。在实际应用场景中，建议使用多张照片以提高准确性。此外，此示例将创建一个人员组并在该组中创建人员。如果你想要删除它，请参阅[删除 API](https://dev.projectoxford.ai/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395245)。

## 问题和意见

我们乐意倾听你有关 Microsoft Graph SDK 个人资料图片示例的反馈。你可通过该存储库中的[问题](https://github.com/microsoftgraph/ios-swift-faceapi-sample/issues)部分向我们发送问题和建议。

与 Microsoft Graph 开发相关的一般问题应发布到 [Stack Overflow](http://stackoverflow.com/questions/tagged/Office365+API)。确保你的问题或意见标记有 \[Office365] 和 \[MicrosoftGraph]。

## 贡献

您需要在提交拉取请求之前签署[参与者许可协议](https://cla.microsoft.com/)。要完成参与者许可协议 (CLA)，你需要通过表格提交请求，并在收到包含文件链接的电子邮件时在 CLA 上提交电子签名。

此项目已采用 [Microsoft 开放源代码行为准则](https://opensource.microsoft.com/codeofconduct/)。有关详细信息，请参阅[行为准则常见问题解答](https://opensource.microsoft.com/codeofconduct/faq/)。如有其他任何问题或意见，也可联系 [opencode@microsoft.com](mailto:opencode@microsoft.com)。

## 版权信息

版权所有 (c) 2016 Microsoft。保留所有权利。
