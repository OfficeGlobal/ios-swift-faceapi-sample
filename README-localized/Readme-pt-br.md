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
# Serviços Cognitivos da Microsoft com amostra Graph SDK para iOS

Este exemplo mostra como usar tanto o [Microsoft Graph SDK para iOS](https://github.com/microsoftgraph/msgraph-sdk-ios) e a [API facial dos Serviços Cognitivos da Microsoft](https://www.microsoft.com/cognitive-services/en-us/face-api) em um aplicativo iOS.
O usuário pode selecionar uma foto localmente a partir do dispositivo ou de um perfil de usuário armazenado no Microsoft Exchange ou no Outlook. O exemplo usa a API facial para detectar e identificar a pessoa na foto.

O código de exemplo mostra como fazer o seguinte:

- Recuperar uma imagem de perfil de usuário do diretório do Office 365 do usuário conectado usando o Microsoft Graph.
- Crie um grupo de pessoas, adicione uma pessoa ao grupo de pessoas, treine um grupo de pessoas, detecte rostos e identifique rostos.

![Seleção de Foto](/readme-images/photoSelection.png) ![Identificação de Foto](/readme-images/photoIdentification.png)

## Pré-requisitos

- [Xcode](https://developer.apple.com/xcode/downloads/) versão 10.2.1
- A instalação de [CocoaPods](https://guides.cocoapods.org/using/using-cocoapods.html) como um gerenciador de dependência.
- Uma conta corporativa ou de estudante da Microsoft. Inscreva-se para uma [Assinatura de Desenvolvedor do Office 365](https://profile.microsoft.com/RegSysProfileCenter/wizardnp.aspx?wizid=14b845d0-938c-45af-b061-f798fbb4d170&lcid=1033), que inclui os recursos necessários para começar a criação de aplicativos do Office 365.

    > Observação: Esse exemplo depende que o usuário possua uma conta corporativa com uma imagem de perfil definida no Microsoft Exchange ou aplicada no perfil do Outlook do usuário. Se uma imagem de perfil não estiver presente em lugar nenhum, você verá um erro quando o exemplo tentar recuperar a imagem.

- Uma chave de assinatura para Serviços Cognitivos. Confira os serviços fornecidos pela [Serviços Cognitivos da Microsoft](https://www.microsoft.com/cognitive-services). Certifique-se de adicionar uma chave para a API de rosto. Será preciso usar o valor da chave quando seguir as etapas no **executando esse exemplo na seção do Xcode** a seguir.

## Registrar e configurar o aplicativo

1. Abra um navegador e navegue até o [centro de administração do Azure Active Directory](https://aad.portal.azure.com) e faça logon, usando uma **conta pessoal** (também conhecida como: Conta Microsoft) **Conta Corporativa ou de Estudante**.

1. Selecione **Azure Active Directory** na navegação à esquerda e, em seguida, selecione **Registros de aplicativos** em **Gerenciar**.

1. Selecione **Novo registro**. Na página **Registrar um aplicativo**, defina os valores da seguinte forma.

    - Defina o **nome** para `exemplo de API de rosto Swift`.
    - Defina **tipos de contas com suporte** para **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
    - Em **URI de Redirecionamento**, altere a lista suspensa para **Cliente público (celular e desktop)**, e defina o valor como `msauth.com.microsoft.ios-swift-faceapi-sample://auth`.

1. Escolha **Registrar**. Na página **Exemplo de API de rosto Swift** , copie o valor da **ID do aplicativo (cliente)** e salve-o, você precisará dele na próxima etapa.

## Executando este exemplo em Xcode

1. Clone ou baixe esse repositório.
1. Use o CocoaPods para importar as dependências do SDK. Este aplicativo de exemplo já contém um podfile que colocará os pods no projeto. Basta navegar até a raiz do projeto a partir do **Terminal** e executar:

    ```Shell
    pod install
    ```

1. Abra o **ios-swift-faceAPIs-with-Graph.xcworkspace**.
1. Abra o arquivo **Application/ApplicationConstants.swift**. Substitua `SEU ID DE CLIENTE` pela ID do aplicativo registrado.
1. Substitua `ENTER_SUBSCRIPTION_KEY` pela chave da sua API de rosto.
1. Substitua `YOUR_FACE_API_ENDPOINT` pelo ponto de extremidade do seu serviço cognitivo da API de rosto. Consulte a [documentação Azure](https://docs.microsoft.com/azure/cognitive-services/face/quickstarts/curl#face-endpoint-url) para obter mais detalhes.
1. Execute o exemplo. Será solicitado que você se conecte a/autentique uma conta corporativa e será necessário fornecer suas credenciais do Office 365. Depois de autenticado, você será levado ao comando seletor de fotos para selecionar uma pessoa a ser identificada e uma foto a ser processada.

## Código de Interesse

### Graph

Este exemplo contém duas chamadas do Microsoft Graph, ambas estão no arquivo **Graph.swift** em /Graph.

1. Obter o diretório do usuário

    ```swift
    func getUsers(with completion: (result: GraphResult<[MSGraphUser], Error>) -> Void) {
        graphClient.users().request().getWithCompletion {
            (userCollection: MSCollection?, next: MSGraphUsersCollectionRequest?, error: NSError?) in
        ...
            }
        }
    }
    ```

2. Obter perfil do usuário (valor da foto)

    ```swift
    func getPhotoValue(forUser upn: String, with completion: (result: GraphResult<UIImage, Error>) -> Void) {
        graphClient.users(upn).photoValue().downloadWithCompletion {
            (url: NSURL?, response: NSURLResponse?, error: NSError?) in
        ...
        }
    }
    ```

### Serviços Cognitivos - API de rosto

Este exemplo mostra as noções básicas de como usar a API de rosto dos Serviços Cognitivos da Microsoft, para detectar e identificar rostos. Para mais informações, acesse [API de rosto da Microsoft](https://www.microsoft.com/cognitive-services/en-us/face-api/documentation/overview)

O código para identificar rostos do zero e as funções relacionadas estão no arquivo **FaceAPI.swift**  em /CognitiveServices e o arquivo **FaceApiTableViewController.swift** em /Controllers.

Estas são as etapas seguidas pelo código:

1. Criar grupo de pessoas. Você pode encontrar o nome do grupo de pessoas no arquivo **FaceApiTableViewController. Swift**.
2. Criar pessoa no grupo de pessoas novas.

   (O usuário deve ser criado dentro do grupo antes de carregar as faces ou o treinamento).
3. Carregar o rosto de uma pessoa.
4. Treinar o grupo de pessoas.
5. Verificar e aguardar a conclusão do treinamento.

   Isso deve levar apenas alguns segundos. Pesquise até que ela seja concluída e prossiga para a próxima etapa.
6. Detectar faces.
7. Identifique os rostos no grupo de pessoas.

> Observação: Este exemplo usa uma única foto para treinamento. Em situações reais, seria recomendável usar mais de uma foto para obter uma melhor precisão. Além disso, este exemplo criará um grupo de pessoas, bem como as pessoas dentro desse grupo. Se desejar excluí-lo, confira [exclusão](https://dev.projectoxford.ai/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395245) de API.

## Perguntas e comentários

Adoraríamos receber os seus comentários sobre o exemplo de Fotos de Perfil do Microsoft Graph SDK. Você pode enviar perguntas e sugestões na seção [Problemas](https://github.com/microsoftgraph/ios-swift-faceapi-sample/issues) deste repositório.

As perguntas sobre o desenvolvimento do Microsoft Graph em geral devem ser postadas no [Stack Overflow](http://stackoverflow.com/questions/tagged/Office365+API). Não deixe de marcar as perguntas ou comentários com \[Office365] e \[MicrosoftGraph].

## Colaboração

Assine o [Contributor License Agreement (Contrato de Licença de Colaborador)](https://cla.microsoft.com/) antes de enviar a solicitação pull. Para concluir o CLA (Contributor License Agreement), você deve enviar uma solicitação através do formulário e assinar eletronicamente o CLA quando receber o email com o link para o documento.

Este projeto adotou o [Código de Conduta de Código Aberto da Microsoft](https://opensource.microsoft.com/codeofconduct/).  Para saber mais, confira as [Perguntas frequentes sobre o Código de Conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou entre em contato pelo [opencode@microsoft.com](mailto:opencode@microsoft.com) se tiver outras dúvidas ou comentários.

## Direitos autorais

Copyright © 2016 Microsoft. Todos os direitos reservados.
