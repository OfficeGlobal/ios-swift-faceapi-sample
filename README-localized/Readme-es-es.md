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
# Ejemplo para iOS de Microsoft Cognitive Services con SDK de Graph

En este ejemplo se muestra cómo usar el [SDK de Microsoft Graph para iOS](https://github.com/microsoftgraph/msgraph-sdk-ios) y la [API de Face de Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services/en-us/face-api) en una aplicación iOS.
El usuario puede seleccionar una foto localmente desde el dispositivo o desde un perfil de usuario almacenado en Microsoft Exchange u Outlook. En el ejemplo se usa la API de Face para detectar e identificar a la persona de la foto.

En el código de ejemplo se muestra cómo hacer lo siguiente:

- Recuperar una imagen de perfil de usuario desde el directorio de Office 365 del usuario que ha iniciado sesión mediante Microsoft Graph.
- Crear un grupo de personas, agregar un usuario al grupo de personas, entrenar un grupo de personas, detectar caras e identificar caras.

![Selección de fotos](/readme-images/photoSelection.png) ![Identificación de fotos](/readme-images/photoIdentification.png)

## Requisitos previos

- La versión 10.2.1 de [Xcode](https://developer.apple.com/xcode/downloads/)
- Instalación de [CocoaPods](https://guides.cocoapods.org/using/using-cocoapods.html) como administrador de dependencias.
- Una cuenta educativa o profesional de Microsoft. Puede registrarse para obtener [una suscripción a Office 365 Developer](https://profile.microsoft.com/RegSysProfileCenter/wizardnp.aspx?wizid=14b845d0-938c-45af-b061-f798fbb4d170&lcid=1033), que incluye los recursos que necesita para comenzar a crear aplicaciones de Office 365.

    > Nota: Para usar este ejemplo, el usuario debe tener una cuenta de la organización con una imagen de perfil configurada en Microsoft Exchange o aplicada en el perfil de Outlook del usuario. Si no hay ninguna imagen de perfil en ninguna de las ubicaciones, verá un error cuando el ejemplo intente recuperar la imagen.

- Una clave de suscripción de Cognitive Services. Eche un vistazo a los servicios proporcionados por [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services). Asegúrese de agregar una clave para la API de Face. Tendrá que usar ese valor de clave cuando siga los pasos de la sección **Ejecutar este ejemplo en Xcode** que encontrará más abajo.

## Registrar y configurar la aplicación

1. Abra un explorador y vaya al [Centro de administración de Azure Active Directory](https://aad.portal.azure.com) e inicie sesión con una **cuenta personal** (también conocida como: una cuenta de Microsoft) o una **cuenta profesional o educativa**.

1. Seleccione **Azure Active Directory** en el panel de navegación izquierdo y, después, **Registros de aplicaciones** en **Administrar**.

1. Seleccione **Nuevo registro**. En la página **Registrar una aplicación**, establezca los valores siguientes.

    - Establezca **Nombre** como `Swift Face API Sample`.
    - Establezca **Tipos de cuenta admitidos** en **Cuentas en cualquier directorio de organización y cuentas personales de Microsoft**.
    - En **URI de redirección**, cambie la lista desplegable a **Cliente público (móvil y escritorio)** y establezca el valor en `msauth.com.microsoft.ios-swift-faceapi-sample://auth`.

1. Elija **Registrar**. En la página **Swift Face API Sample**, copie el valor del **Id. de aplicación (cliente)** y guárdelo. Lo necesitará en el paso siguiente.

## Ejecutar este ejemplo en Xcode

1. Clone o descargue este repositorio.
1. Use CocoaPods para importar las dependencias del SDK. Esta aplicación de muestra contiene un podfile que recibirá los pods en el proyecto. Simplemente vaya a la raíz del proyecto desde **Terminal** y ejecute:

    ```Shell
    pod install
    ```

1. Abra **ios-swift-faceAPIs-with-Graph.xcworkspace**.
1. Abra el archivo **Application/ApplicationConstants.swift**. Reemplace `YOUR CLIENT ID` por el Id. de aplicación de la aplicación registrada.
1. Reemplace `ENTER_SUBSCRIPTION_KEY` por la clave de la API de Face.
1. Reemplace `YOUR_FACE_API_ENDPOINT` por el punto de conexión del servicio cognitivo de la API de Face. Consulte la [documentación de Azure](https://docs.microsoft.com/azure/cognitive-services/face/quickstarts/curl#face-endpoint-url) para obtener más información.
1. Ejecute el ejemplo. Se le pedirá que se conecte/autentique en una cuenta profesional y tendrá que proporcionar las credenciales de Office 365. Una vez que se haya autenticado, se le dirigirá al controlador del selector de fotografías para que seleccione una persona para identificarla y una foto con la que identificarla.

## Código de interés

### Graph

Este ejemplo contiene dos llamadas a Microsoft Graph, que se encuentran en el archivo **Graph.swift** en /Graph.

1. Obtener el directorio del usuario

    ```swift
    func getUsers(with completion: (result: GraphResult<[MSGraphUser], Error>) -> Void) {
        graphClient.users().request().getWithCompletion {
            (userCollection: MSCollection?, next: MSGraphUsersCollectionRequest?, error: NSError?) in
        ...
            }
        }
    }
    ```

2. Obtener el perfil del usuario (valor de foto)

    ```swift
    func getPhotoValue(forUser upn: String, with completion: (result: GraphResult<UIImage, Error>) -> Void) {
        graphClient.users(upn).photoValue().downloadWithCompletion {
            (url: NSURL?, response: NSURLResponse?, error: NSError?) in
        ...
        }
    }
    ```

### Cognitive Services: API de Face

En este ejemplo se muestran los conceptos básicos del uso de la API de Face de Microsoft Cognitive Services para detectar e identificar caras. Para obtener más información, visite [API de Microsoft Face](https://www.microsoft.com/cognitive-services/en-us/face-api/documentation/overview)

El código para identificar caras desde cero y las funciones relacionadas se encuentra en el archivo **FaceAPI.swift**, en /CognitiveServices, y en el archivo **FaceApiTableViewController.swift**, en /Controllers.

Estos son los pasos que sigue el código:

1. Crear grupo de personas. Puede encontrar el nombre del grupo de personas en el archivo **FaceApiTableViewController.swift**.
2. Crear una persona en el nuevo grupo de personas.

   (Es necesario crear la persona en el grupo antes de cargar la cara o las caras y hacer el entrenamiento).
3. Cargar la cara de la persona.
4. Entrenar el grupo de personas.
5. Comprobar y esperar a que finalice el entrenamiento.

   Esto debería tardar solo unos segundos. Sondear hasta que finalice y, después, ir al siguiente paso.
6. Detectar caras.
7. Identificar caras en el grupo de personas.

> Nota: En este ejemplo se usa una única foto para el entrenamiento. En escenarios reales, es recomendable usar más de una foto para mejorar la precisión. En este ejemplo también se crea un grupo de personas, así como personas dentro de ese grupo. Si quiere eliminarla, consulte [Eliminar](https://dev.projectoxford.ai/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395245) API.

## Preguntas y comentarios

Nos encantaría recibir sus comentarios sobre el ejemplo de imágenes de perfil del SDK de Microsoft Graph. Puede enviarnos sus preguntas y sugerencias a través de la sección [Problemas](https://github.com/microsoftgraph/ios-swift-faceapi-sample/issues) de este repositorio.

Las preguntas sobre el desarrollo de Microsoft Graph en general deben publicarse en [Stack Overflow](http://stackoverflow.com/questions/tagged/Office365+API). Asegúrese de que sus preguntas o comentarios se etiquetan con \[Office365] y \[MicrosoftGraph].

## Colaboradores

Deberá firmar un [Contrato de licencia de colaborador](https://cla.microsoft.com/) antes de enviar la solicitud de incorporación de cambios. Para completar el Contrato de licencia de colaborador (CLA), deberá enviar una solicitud mediante un formulario y, después, firmar electrónicamente el CLA cuando reciba el correo electrónico que contiene el vínculo al documento.

Este proyecto ha adoptado el [código de conducta de código abierto de Microsoft](https://opensource.microsoft.com/codeofconduct/). Para obtener más información, vea [Preguntas frecuentes sobre el código de conducta](https://opensource.microsoft.com/codeofconduct/faq/) o póngase en contacto con [opencode@microsoft.com](mailto:opencode@microsoft.com) si tiene otras preguntas o comentarios.

## Copyright

Copyright (c) 2016 Microsoft. Todos los derechos reservados.
