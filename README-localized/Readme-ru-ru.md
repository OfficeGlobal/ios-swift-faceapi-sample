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
# Пример приложения для iOS, работающего с Microsoft Cognitive Services с помощью пакета SDK Graph

В этом примере показано, как в приложении iOS использовать [пакет SDK Microsoft Graph для iOS](https://github.com/microsoftgraph/msgraph-sdk-ios) и [API Face Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services/en-us/face-api). Пользователь может выбрать фотографию из локального источника на устройстве или из профиля пользователя, сохраненного в Microsoft Exchange или Outlook.
В примере используется API Face для поиска и идентификации пользователя на фотографии.

В примере кода показано, как выполняется следующее:

- Извлечение аватара вошедшего в систему пользователя из каталога Office 365 с помощью Microsoft Graph.
- Создание группы "люди", добавление пользователя в группу, обучение группы, поиск лиц и их идентификация.

![PhotoSelection](/readme-images/photoSelection.png) ![PhotoIdentification](/readme-images/photoIdentification.png)

## Предварительные требования

- [Xcode](https://developer.apple.com/xcode/downloads/) версии 10.2.1.
- Установка [CocoaPods](https://guides.cocoapods.org/using/using-cocoapods.html) в качестве диспетчера зависимостей.
- Рабочая или учебная учетная запись Майкрософт. Вы можете подписаться на [план Office 365 для разработчиков](https://profile.microsoft.com/RegSysProfileCenter/wizardnp.aspx?wizid=14b845d0-938c-45af-b061-f798fbb4d170&lcid=1033), который включает ресурсы, необходимые для создания приложений Office 365.

    > Примечание. В этом примере фигурирует пользователь с учетной записью организации и аватаром, установленным в Microsoft Exchange или используемым в профиле пользователя Outlook. Если аватара нет ни в одном из этих расположений, при попытке получить аватар пример выведет сообщение об ошибке.

- Ключ подписки на Cognitive Services. Ознакомьтесь с сервисами [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services). Не забудьте добавить ключ для API Face. Значение этого ключа потребуется при выполнении шагов, перечисленных в разделе **Запуск примера в Xcode** ниже.

## Регистрация и настройка приложения

1. Откройте браузер, перейдите в [Центр администрирования Azure Active Directory](https://aad.portal.azure.com) и войдите с помощью **личной учетной записи** (т. е.  учетной записи Майкрософт) или **рабочей или учебной учетной записи**.

1. Выберите **Azure Active Directory** на панели навигации слева, затем выберите **Регистрация приложения** в разделе **Управление**.

1. Выберите **Новая регистрация**. На странице **Регистрация приложения** задайте необходимые значения следующим образом:

    - Задайте **Название** для `примера API Face Swift`.
    - В качестве значения параметра **Поддерживаемые типы учетных записей** задайте **Учетные записи в любом каталоге организации и личные учетные записи Майкрософт**.
    - В разделе **URI перенаправления ** выберите в раскрывающемся списке **Общедоступный клиент (для мобильных и классических версий приложений)** и задайте значение `msauth.com.microsoft.ios-swift-faceapi-sample://auth`.

1. Нажмите кнопку **Регистрация**. На странице **Пример приложения для API Face на Swift** скопируйте значение **Идентификатора приложения (клиента)** и сохраните его — оно потребуется на следующем шаге.

## Запуск этого примера в Xcode

1. Клонируйте или скачайте этот репозиторий.
1. С помощью CocoaPods импортируйте зависимости SDK. Этот пример приложения уже содержит podfile, который добавит компоненты pod в проект. Просто перейдите к корню проекта из раздела **Терминал** и выполните следующую команду:

    ```Shell
    pod install
    ```

1. Откройте **ios-swift-faceAPIs-with-Graph.xcworkspace**.
1. Откройте файл **Application/ApplicationConstants.swift**. Замените текст `YOUR CLIENT ID` идентификатором приложения из регистрации вашего приложения.
1. Замените текст `ENTER_SUBSCRIPTION_KEY` своим ключом API Face.
1. Замените текст `YOUR_FACE_API_ENDPOINT` конечной точкой вашего когнитивного сервиса API Face. Более подробную информацию см. в [документации Azure](https://docs.microsoft.com/azure/cognitive-services/face/quickstarts/curl#face-endpoint-url).
1. Запустите пример. Вам будет предложено подключиться к своей рабочей учетной записи и пройти проверку подлинности, при этом потребуется ввести свои учетные данные Office 365. После проверки подлинности откроется контроллер выбора фотографий для выбора пользователя и фотографии, на которой вы хотите его найти.

## Полезный код

### Graph

В этом примере используются два вызова Microsoft Graph, оба содержатся в файле **Graph.swift** в разделе /Graph.

1. Получение каталога пользователя

    ```swift
    func getUsers(with completion: (result: GraphResult<[MSGraphUser], Error>) -> Void) {
        graphClient.users().request().getWithCompletion {
            (userCollection: MSCollection?, next: MSGraphUsersCollectionRequest?, error: NSError?) in
        ...
            }
        }
    }
    ```

2. Получение информации из профиля пользователя (фотографии)

    ```swift
    func getPhotoValue(forUser upn: String, with completion: (result: GraphResult<UIImage, Error>) -> Void) {
        graphClient.users(upn).photoValue().downloadWithCompletion {
            (url: NSURL?, response: NSURLResponse?, error: NSError?) in
        ...
        }
    }
    ```

### Cognitive Services - Face API

В этом примере показаны основы использования API Face Microsoft Cognitive Services для поиска и идентификации лиц. Дополнительную информацию см. в статье [Microsoft Face API](https://www.microsoft.com/cognitive-services/en-us/face-api/documentation/overview)

Код для идентификации лиц и соответствующих функций находится в файле **FaceAPI.swift** в разделе /CognitiveServices и в файле **FaceApiTableViewController.swift** в разделе /Controllers.

С помощью этого кода выполняются следующие действия:

1. Создание группы "люди". Название этой группы содержится в файле **FaceApiTableViewController.swift**.
2. Создание пользователя в новой группе.

   (Пользователь должен быть создан внутри группы до отправки изображений лиц и обучения.)
3. Отправка изображения лица.
4. Обучение группы "люди".
5. Дождитесь завершения обучения.

   Это займет всего несколько секунд. Проводите опросы до завершения обучения, затем переходите к следующему шагу.
6. Выполните поиск лиц.
7. Идентифицируйте лица в группе "люди".

> Примечание. В этом примере для обучения используется одна фотография. В реальных условиях рекомендуется использовать несколько фотографий для точности результатов. Кроме того, при выполнении примера создается группа "люди" и люди внутри этой группы. Если необходимо ее удалить, см. информацию об [удалении](https://dev.projectoxford.ai/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395245) в api.

## Вопросы и комментарии

Мы будем рады получить ваши отзывы о примере работы с фотографией пользователя в SDK Microsoft Graph. Вы можете отправлять нам вопросы и предложения в разделе [Проблемы](https://github.com/microsoftgraph/ios-swift-faceapi-sample/issues) этого репозитория.

Общие вопросы о разработке решений для Microsoft Graph следует задавать на сайте [Stack Overflow](http://stackoverflow.com/questions/tagged/Office365+API). Обязательно помечайте свои вопросы и комментарии тегами \[Office365] и \[MicrosoftGraph].

## Участие

Прежде чем отправить запрос на включение внесенных изменений, необходимо подписать [лицензионное соглашение с участником](https://cla.microsoft.com/). Чтобы заполнить лицензионное соглашение с участником (CLA), вам потребуется отправить запрос с помощью формы, а затем после получения электронного сообщения со ссылкой на этот документ подписать CLA с помощью электронной подписи.

Этот проект соответствует [Правилам поведения разработчиков открытого кода Майкрософт](https://opensource.microsoft.com/codeofconduct/). Дополнительные сведения см. в разделе [Часто задаваемые вопросы о правилах поведения](https://opensource.microsoft.com/codeofconduct/faq/). Если у вас возникли вопросы или замечания, напишите нам по адресу [opencode@microsoft.com](mailto:opencode@microsoft.com).

## Авторское право

(c) Корпорация Майкрософт (Microsoft Corporation), 2016. Все права защищены.
