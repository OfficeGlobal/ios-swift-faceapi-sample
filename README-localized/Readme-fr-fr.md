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
# Microsoft Cognitive Services avec exemple de kit de développement logiciel (SDK) Graph pour iOS

Cet exemple vous présente comment utiliser le [Kit de développement logiciel SDK Microsoft Graph pour iOS](https://github.com/microsoftgraph/msgraph-sdk-ios) et [Face API Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services/en-us/face-api) dans une application iOS.
L’utilisateur peut sélectionner une photo localement à partir de l’appareil ou du profil utilisateur stocké dans Microsoft Exchange ou Outlook. L’exemple utilise Face API pour détecter et identifier la personne sur la photo.

Le code d'exemple montre comment effectuer ce qui suit :

- Récupérez une image de profil utilisateur à partir du répertoire Office 365 de l’utilisateur connecté en utilisation Microsoft Graph.
- Création d’un groupe de personnes, ajout d’une personne au groupe de personnes, formation d’un groupe de personnes, détection et identification de visages.

![PhotoSelection](/readme-images/photoSelection.png) ![PhotoIdentification](/readme-images/photoIdentification.png)

## Conditions préalables

- [Xcode](https://developer.apple.com/xcode/downloads/) version 10.2.1
- Installation de [CocoaPods](https://guides.cocoapods.org/using/using-cocoapods.html) comme gestionnaire de dépendances.
- Un compte professionnel ou scolaire Microsoft. Vous pouvez vous inscrire à [Office 365 Developer](https://profile.microsoft.com/RegSysProfileCenter/wizardnp.aspx?wizid=14b845d0-938c-45af-b061-f798fbb4d170&lcid=1033) pour accéder aux ressources dont vous avez besoin pour commencer à créer des applications Office 365.

    > Remarque : Cet exemple s’appuie sur l’utilisateur ayant un compte professionnel avec une image de profil dans Microsoft Exchange ou appliquée dans le profil Outlook de l’utilisateur. Si la photo de profil n’apparaît pas dans l’un de ces emplacements, une erreur apparaît lorsque l’exemple tente de récupérer l’image.

- Une clé d'abonnement à Cognitive Services. Découvrez les services fournis par [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services). Veillez à ajouter une clé pour Face API. Vous devez utiliser cette valeur de clé lorsque vous suivez les étapes décrites dans la section **Exécutez cet exemple dans Xcode** ci-dessous.

## Enregistrement et configuration de l’application

1. Ouvrez un navigateur, accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) et connectez-vous à l’aide d’un **compte personnel** (ou compte Microsoft) ou d’un **compte professionnel ou scolaire**.

1. Sélectionnez **Azure Active Directory** dans le volet de navigation gauche, puis sélectionnez **Inscriptions d’applications** sous **Gérer**.

1. Sélectionnez **Nouvelle inscription**. Sur la page **Inscrire une application**, définissez les valeurs comme suit.

    - Configurez le **Nom** pour l'`exemple d’API Swift Face`.
    - Définissez les **Types de comptes pris en charge** sur les **Comptes figurant dans un annuaire organisationnel et les comptes Microsoft personnels**.
    - Sous **URI de redirection**, remplacez la liste déroulante par **client public (mobile et bureau)** et définissez la valeur sur `msauth.com.microsoft.ios-swift-faceapi-sample://auth`.

1. Choisissez **Inscrire**. Dans la page **Exemple d’API Swift Face**, copiez la valeur de l'**ID d’application (client)** et enregistrez-la, car vous en aurez besoin à l’étape suivante.

## Exécution de cet exemple dans Xcode

1. Clonez ou téléchargez ce référentiel.
1. Utilisez CocoaPods pour importer les dépendances SDK. Cet exemple d’application contient déjà un podfile qui recevra les pods dans le projet. Naviguez simplement vers la racine de projet à partir de **Terminal** et exécutez :

    ```Shell
    pod install
    ```

1. Ouvrez **ios-swift-faceAPIs-with-Graph.xcworkspace**.
1. Ouvrez le fichier **Application/ApplicationConstants. Swift**. Remplacez `YOUR CLIENT ID` par l’ID de votre inscription d'application.
1. Remplacez `ENTER_SUBSCRIPTION_KEY` par votre clé API Face.
1. Remplacez `YOUR_FACE_API_ENDPOINT` par le point de terminaison de votre service cognitif Face API. Pour plus de détails, consultez la [documentation Azure](https://docs.microsoft.com/azure/cognitive-services/face/quickstarts/curl#face-endpoint-url).
1. Exécutez l’exemple. Vous êtes invité à vous connecter/vous authentifier à un compte professionnel. Vous devez fournir vos informations d’identification Office 365. Une fois l’authentification effectuée, vous êtes redirigé vers le contrôleur de sélecteur de photo pour sélectionner une personne à identifier et une photo à partir de laquelle vous voulez vous identifier.

## Code d’intérêt

### Graph

Cet exemple contient deux appels Microsoft Graph, qui se trouvent dans le fichier **Graph.swift** sous /Graph.

1. Obtenir l’annuaire de l’utilisateur

    ```swift
    func getUsers(with completion: (result: GraphResult<[MSGraphUser], Error>) -> Void) {
        graphClient.users().request().getWithCompletion {
            (userCollection: MSCollection?, next: MSGraphUsersCollectionRequest?, error: NSError?) in
        ...
            }
        }
    }
    ```

2. Obtenir la photo de profil de l'utilisateur (valeur photo)

    ```swift
    func getPhotoValue(forUser upn: String, with completion: (result: GraphResult<UIImage, Error>) -> Void) {
        graphClient.users(upn).photoValue().downloadWithCompletion {
            (url: NSURL?, response: NSURLResponse?, error: NSError?) in
        ...
        }
    }
    ```

### Cognitive Services – Face API

Cet exemple illustre les concepts de base de l’utilisation de Face API Microsoft Cognitive Services pour détecter et identifier des visages. Pour plus d’informations, veuillez visite la page [Microsoft Face API](https://www.microsoft.com/cognitive-services/en-us/face-api/documentation/overview)

Le code permettant d’identifier des visages à partir de rayures et de fonctions associées se trouve dans le fichier **FaceAPI.swift** sous /CognitiveServices et le fichier **FaceApiTableViewController.swift** sous /Controllers.

Voici les étapes suivies par le code :

1. Créer un groupe de personnes. Le nom du groupe de personnes se trouve dans le fichier **FaceApiTableViewController.swift**.
2. Créer une personne dans le nouveau groupe de personnes.

   (La personne doit être créée au sein du groupe avant de charger la ou les visages et les formations.)
3. Charger le visage de la personne.
4. Former le groupe de personnes.
5. Vérifiez & patientez jusqu’à la fin de la formation.

   Cette opération ne demande que quelques secondes. Sondez jusqu’à la fin, puis passez à l’étape suivante.
6. Détecter les visages.
7. Identifiez les visages dans le groupe de personnes.

> Remarque : Cet exemple utilise une seule photo pour la formation. Dans le cadre de scénarios concrets, nous vous conseillons d’utiliser plusieurs photos pour obtenir une meilleure précision. De plus, cet exemple crée un groupe de personnes, ainsi que des personnes au sein de ce groupe. Si vous voulez le supprimer, consultez [supprimer](https://dev.projectoxford.ai/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395245) l'API.

## Questions et commentaires

Nous serions ravis de connaître votre opinion sur l’exemple de photo de profil Microsoft Graph SDK. Vous pouvez nous faire part de vos questions et suggestions dans la rubrique [Problèmes](https://github.com/microsoftgraph/ios-swift-faceapi-sample/issues) de ce référentiel.

Les questions générales sur le développement de Microsoft Graph doivent être publiées sur [Stack Overflow](http://stackoverflow.com/questions/tagged/Office365+API). Assurez-vous de poser vos questions ou de rédiger vos commentaires avec les tags \[MicrosoftGraph] et \[Office 365].

## Contribution

Vous devrez signer un [Contrat de licence de contributeur](https://cla.microsoft.com/) avant d’envoyer votre requête de tirage. Pour compléter le contrat de licence de contributeur (CLA), vous devez envoyer une requête en remplissant le formulaire, puis signer électroniquement le CLA quand vous recevrez le courrier électronique contenant le lien vers le document.

Ce projet a adopté le [Code de conduite Open Source de Microsoft](https://opensource.microsoft.com/codeofconduct/). Pour en savoir plus, reportez-vous à la [FAQ relative au code de conduite](https://opensource.microsoft.com/codeofconduct/faq/) ou contactez [opencode@microsoft.com](mailto:opencode@microsoft.com) pour toute question ou tout commentaire.

## Copyright

Copyright (c) 2016 Microsoft. Tous droits réservés.
