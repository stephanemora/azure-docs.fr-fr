---
title: Développement de fonctions Azure Functions avec Media Services v3
description: Cet article montre comment développer des fonctions Azure Functions avec Media Services v3 et Visual Studio Code.
services: media-services
author: xpouyat
ms.service: media-services
ms.workload: media
ms.devlang: dotnet
ms.topic: article
ms.date: 06/09/2021
ms.author: xpouyat
ms.custom: devx-track-csharp
ms.openlocfilehash: 523a5dbfb503f47f15e44e7be1b61bf6cf05c471
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661713"
---
# <a name="develop-azure-functions-with-media-services-v3"></a>Développement de fonctions Azure Functions avec Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Cet article vous montre comment vous familiariser avec la création de fonctions Azure qui utilisent Media Services. La fonction Azure définie dans cet article encode un fichier vidéo avec Media Encoder Standard. Une fois que le travail d’encodage a été créé, la fonction retourne le nom du travail et le nom de l’actif multimédia de sortie. Pour en savoir plus sur Azure Functions, consultez la [Vue d’ensemble](../../azure-functions/functions-overview.md) et les autres rubriques de la section **Azure Functions**.

Si vous souhaitez explorer et déployer des fonctions Azure existantes qui utilisent Azure Media Services, consultez [Fonctions Azure Media Services](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration). Ce dépôt contient des exemples qui utilisent Media Services pour afficher les workflows liés à l’ingestion de contenu directement à partir du stockage Blob, à l’encodage et aux opérations de streaming en direct.

## <a name="prerequisites"></a>Conditions préalables requises

- Pour créer votre première fonction, vous devez avoir un compte Azure actif. Si tel n’est pas le cas, des [comptes gratuits sont disponibles](https://azure.microsoft.com/free/).
- Si vous souhaitez créer des fonctions Azure Functions qui effectuent des actions sur votre compte Azure Media Services (AMS) ou qui écoutent des événements envoyés par Media Services, vous devez créer un compte AMS, comme décrit [ici](account-create-how-to.md).
- Installer [Visual Studio Code](https://code.visualstudio.com/) sur l’une des [plateformes prises en charge](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

Cet article explique comment créer une fonction C# .NET 5 qui communique avec Azure Media Services. Pour créer une fonction avec un autre langage, consultez [cet article](../../azure-functions/functions-develop-vs-code.md).

### <a name="run-local-requirements"></a>Conditions requises pour une exécution locale

Ces conditions préalables sont requises uniquement pour exécuter et déboguer vos fonctions localement. Elles ne sont pas requises pour créer ou publier des projets dans Azure Functions.

- [SDK .NET Core 3.1 et .NET 5](https://dotnet.microsoft.com/download/dotnet).

- [Azure Functions Core Tools](../../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools) version 3.x ou ultérieure. Le package Core Tools est téléchargé et installé automatiquement lorsque vous démarrez le projet en local. Ce package inclut l’intégralité du runtime Azure Functions. Par conséquent, son téléchargement et son installation peuvent prendre un certain temps.

- [Extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pour Visual Studio Code.

## <a name="install-the-azure-functions-extension"></a>Installer l’extension Azure Functions

Vous pouvez utiliser l’extension Azure Functions pour créer et tester des fonctions, et les déployer dans Azure.

1. Dans Visual Studio Code, ouvrez **Extensions**. Lancez une recherche sur **azure functions** ou sélectionnez ce lien dans Visual Studio Code : [`vscode:extension/ms-azuretools.vscode-azurefunctions`](vscode:extension/ms-azuretools.vscode-azurefunctions).

1. Sélectionnez **Installer** pour installer l’extension pour Visual Studio Code :

    ![Installez l’extension pour Azure Functions](./Media/integrate-azure-functions-dotnet-how-to/vscode-install-extension.png)

1. Une fois l’installation effectuée, sélectionnez l’icône Azure dans la barre d’activités. Vous devriez voir une zone Azure Functions dans la barre latérale.

    ![Zone Azure Functions dans la barre latérale](./Media/integrate-azure-functions-dotnet-how-to/azure-functions-window-vscode.png)

## <a name="create-an-azure-functions-project"></a>Créer un projet Azure Functions

L’extension Functions vous permet de créer un projet d’application de fonction, ainsi que votre première fonction. Les étapes suivantes expliquent comment créer une fonction déclenchée via HTTP dans un nouveau projet Functions. Le Déclencheur HTTP est le modèle de déclencheur de fonction le plus simple à expliquer.

1. À partir d’**Azure : Functions**, sélectionnez l’icône **Créer une fonction** :

    ![Créer une fonction](./Media/integrate-azure-functions-dotnet-how-to/create-function.png)

1. Sélectionnez le dossier de votre projet d’application de fonction. Ensuite, **sélectionnez le langage C# pour votre projet de fonction** et **.NET 5 Isolé** pour le runtime.

1. Sélectionnez le modèle de fonction **Déclencheur HTTP**.

    ![Choisir le modèle de déclencheur HTTP](./Media/integrate-azure-functions-dotnet-how-to/create-function-choose-template.png)

1. Tapez **HttpTriggerEncode** pour le nom de la fonction puis sélectionnez Entrée. Acceptez **Company.Function** pour l’espace de noms, puis sélectionnez **Fonction** pour les droits d’accès. Avec ce niveau d’autorisation, vous devez fournir une [clé de fonction](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) lorsque vous appelez le point de terminaison de la fonction.

    ![Sélectionner l’autorisation Fonction](./Media/integrate-azure-functions-dotnet-how-to/create-function-auth.png)

    Une fonction est créée dans le langage que vous avez choisi, et dans le modèle d’une fonction déclenchée via HTTP.

    ![Modèle de fonction déclenchée via HTTP dans Visual Studio Code](./Media/integrate-azure-functions-dotnet-how-to/new-function-full.png)

## <a name="install-media-services-and-other-extensions"></a>Installer Media Services et d’autres extensions

Exécutez la commande dotnet add package dans la fenêtre de terminal pour installer les packages d’extension dont vous avez besoin dans votre projet. La commande suivante installe le package Media Services ainsi que les autres extensions nécessaires à l’exemple.

```bash
dotnet add package Azure.Storage.Blobs
dotnet add package Microsoft.Azure.Management.Media
dotnet add package Azure.Identity
```

## <a name="generated-project-files"></a>Fichiers projet générés

Le modèle de projet crée un projet dans le langage choisi, et installe les dépendances voulues. Le nouveau projet comporte ces fichiers :

* **host.json** : vous permet de configurer l’hôte Functions. Ces paramètres s’appliquent lorsque vous exécutez des fonctions localement, et lorsque vous les exécutez dans Azure. Pour plus d’informations, consultez l’article de référence sur [host.json](./../../azure-functions/functions-host-json.md).

* **local.settings.json** : maintient à jour les paramètres utilisés lorsque vous exécutez des fonctions localement. Ces paramètres sont utilisés uniquement lorsque vous exécutez des fonctions localement.

    >[!IMPORTANT]
    >Étant donné que le fichier local.settings.json peut contenir des secrets, vous devez l’exclure du contrôle de code source du projet.

* Le fichier de classe **HttpTriggerEncode.cs** qui implémente la fonction.

### <a name="httptriggerencodecs"></a>HttpTriggerEncode.cs

Il s’agit du code C# de votre fonction. Son rôle consiste à prendre un actif multimédia Media Services ou une URL source, puis à lancer une tâche d’encodage avec Media Services. Cela implique l’utilisation d’une transformation qui est créée si elle n’existe pas déjà. Une fois celle-ci créée, la présélection fournie dans le corps de l’entrée est utilisée. 

>[!IMPORTANT]
>Remplacez l’intégralité du contenu du fichier HttpTriggerEncode.cs par [`HttpTriggerEncode.cs` qui est issu de ce dépôt](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/blob/main/Tutorial/HttpTriggerEncode.cs).

Une fois que vous avez défini votre fonction, sélectionnez **Enregistrer et exécuter**.

Le code source de la méthode **Run** de la fonction est le suivant :

[!code-csharp[Main](../../../media-services-v3-dotnet-core-functions-integration/Tutorial/HttpTriggerEncode.cs#Run)]

### <a name="localsettingsjson"></a>local.settings.json

Mettez à jour le fichier avec le contenu suivant (en remplaçant les valeurs existantes).

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "dotnet-isolated",
    "AadClientId": "00000000-0000-0000-0000-000000000000",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "00000000-0000-0000-0000-000000000000",
    "AadTenantId": "00000000-0000-0000-0000-000000000000",
    "AccountName": "amsaccount",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "ResourceGroup": "amsResourceGroup",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000"
  }
}
```

## <a name="test-your-function"></a>Tester votre fonction

Lorsque vous exécutez la fonction localement dans VS Code, la fonction doit être exposée de la façon suivante : 

```url
http://localhost:7071/api/HttpTriggerEncode
```

Pour la tester, vous pouvez utiliser Postman afin d’effectuer une publication sur cette URL à l’aide d’un corps d’entrée JSON.

Exemple de corps d’entrée JSON :

```json
{
    "inputUrl":"https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4",
    "transformName" : "TransformAS",
    "builtInPreset" :"AdaptiveStreaming"
 }
```

La fonction doit retourner 200 OK avec un corps de sortie contenant le nom du travail et le nom de l’actif multimédia de sortie.

![Tester la fonction avec Postman](./Media/integrate-azure-functions-dotnet-how-to/postman.png)

## <a name="next-steps"></a>Étapes suivantes

À ce stade, vous êtes prêt à développer des fonctions qui appellent l’API Media Services.

Pour en savoir plus et obtenir un exemple complet de l’utilisation d’Azure Functions avec Azure Media Services v3, consultez l’[exemple Media Services v3 Azure Functions](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/main/Functions).
