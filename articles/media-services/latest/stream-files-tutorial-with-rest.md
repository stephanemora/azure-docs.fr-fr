---
title: Charger, encoder et diffuser en continu à l’aide d’Azure Media Services | Microsoft Docs
description: Suivez les étapes décrites dans ce didacticiel pour charger un fichier et encoder la vidéo, puis diffuser en continu votre contenu avec Azure Media Services à l’aide de REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: e49b450ef2c731e9ddbafa0c8366d9eae29dc5ef
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377425"
---
# <a name="tutorial-upload-encode-and-stream-videos-with-rest"></a>Tutoriel : charger, encoder et diffuser en continu des vidéos à l’aide de REST

Ce tutoriel vous explique comment charger, encoder et diffuser en continu des fichiers vidéo avec Azure Media Services.

Media Services vous permet d’encoder vos fichiers multimédias dans des formats pouvant être lus sur un large choix de navigateurs et d’appareils. Par exemple, vous souhaiterez peut-être diffuser votre contenu dans les formats HLS ou MPEG DASH d’Apple. Avant la diffusion en continu, vous devez encoder votre fichier multimédia numérique haute qualité. Pour obtenir des instructions d’encodage, consultez [Encoding concept](encoding-concept.md) (Concept d’encodage).

![Lire la vidéo](./media/stream-files-tutorial-with-api/final-video.png)

Ce didacticiel vous explique les procédures suivantes :    

> [!div class="checklist"]
> * Créer un compte Media Services
> * Accéder à l’API Media Services
> * Télécharger les fichiers Postman
> * Configurer Postman
> * Envoyer des requêtes à l’aide de Postman
> * Tester l’URL de diffusion en continu
> * Supprimer des ressources

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

- Installez le client REST [Postman](https://www.getpostman.com/) pour exécuter les API REST indiquées dans certains des didacticiels REST AMS. 

    Nous utilisons **Postman** mais n’importe quel outil REST serait approprié. Les autres solutions sont : **Visual Studio Code** avec le plug-in REST ou **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Télécharger les fichiers Postman

Cloner un référentiel GitHub contenant les fichiers de collection et d’environnement Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="configure-postman"></a>Configurer Postman

Cette section configure le Postman.

### <a name="configure-the-environment"></a>Configurer l’environnement 

1. Ouvrez **Postman**.
2. Sur la droite de l’écran, sélectionnez l’option **Gérer environnement**.

    ![Gérer environnement](./media/develop-with-postman/postman-import-env.png)
4. Dans la boîte de dialogue **Gérer environnement**, cliquez sur **Importer**.
2. Accédez au `Azure Media Service v3 Environment.postman_environment.json` fichier qui a été téléchargé lorsque vous avez effectué le clonage `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. L’environnement **Azure Media Services v3** est ajouté.

    > [!Note]
    > Mettre à jour les variables d’accès avec les valeurs que vous avez obtenues dans la section **Accéder à l’API Media Services** ci-dessus.

7. Double-cliquez sur le fichier sélectionné et entrez les valeurs que vous avez obtenues en suivant les étapes pour [accéder aux API](#access-the-media-services-api).
8. Fermez la boîte de dialogue.
9. Sélectionnez l’environnement **Azure Media Services v3 Environnement** dans la liste déroulante.

    ![Choisir l’environnement](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Configurer la collection

1. Cliquez sur **Importer** pour importer le fichier de la collection.
1. Accédez au `Media Services v3.postman_collection.json` fichier qui a été téléchargé lorsque vous avez effectué le clonage `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Choisissez le fichier **Media Services v3.postman_collection.json**.

    ![Importer un fichier](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Envoyer des requêtes à l’aide de Postman

Dans cette section, nous enverrons des requêtes qui sont pertinentes pour le codage et la création d’URL afin de vous permettre de diffuser votre fichier. Plus précisément, les requêtes suivantes sont envoyées :

1. Obtenir un jeton Azure AD pour l’authentification du principal du service
2. Créer une ressource de sortie
3. Créer une transformation
4. Création d’un travail 
5. Créer un localisateur de diffusion en continu
6. Répertorier les chemins d’accès du localisateur de diffusion en continu

> [!Note]
>  Ce tutoriel suppose que vous créez toutes les ressources avec des noms uniques.  

### <a name="get-azure-ad-token"></a>Obtenir un jeton Azure AD 

1. Dans la fenêtre de gauche de Postman, sélectionnez « étape 1 : obtenir un jeton d’authentification AAD ».
2. Ensuite, sélectionnez « obtenir un jeton Azure AD pour l’authentification du principal du service ».
3. Appuyez sur **Envoyer**.

    L’opération **POST** suivante est envoyée.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. La réponse inclut le jeton et définit la variable d’environnement « AccessToken » sur la valeur du jeton. Pour afficher le code qui définit « AccessToken », cliquez sur l’onglet **Tests**. 

    ![Obtenir le jeton AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

### <a name="create-an-output-asset"></a>Créer une ressource de sortie

La [ressource](https://docs.microsoft.com/rest/api/media/assets) de sortie stocke le résultat de votre travail d’encodage. 

1. Dans la fenêtre de gauche de Postman, sélectionnez « Ressource ».
2. Ensuite, sélectionnez « Créer ou mettre à jour une ressource ».
3. Appuyez sur **Envoyer**.

    * L’opération **PUT** suivante est envoyée :

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
        ```
    * L’opération a le corps suivant :

        ```json
        {
        "properties": {
            "description": "My Asset",
            "alternateId" : "some GUID"
         }
        }
        ```

### <a name="create-a-transform"></a>Créer une transformation

Lors de l’encodage ou du traitement de contenus dans Media Services, il est courant de configurer les paramètres de codage en tant que formule. Vous envoyez ensuite un **travail** pour appliquer cette formule à une vidéo. En envoyant de nouveaux travaux pour chaque nouvelle vidéo, vous appliquez cette formule à toutes les vidéos de votre bibliothèque. Une formule dans Media Services est référencée comme une **transformation**. Pour plus d’informations, consultez [Transforms and jobs](transform-concept.md) (Transformations et travaux). L’exemple décrit dans ce didacticiel définit une formule qui encode la vidéo, afin de la diffuser en continu sur divers appareils iOS et Android. 

Lorsque vous créez une instance de [transformation](https://docs.microsoft.com/rest/api/media/transforms), vous devez spécifier ce qu’elle doit produire comme sortie. Le paramètre requis est un objet **TransformOutput**. Chaque objet **TransformOutput** contient un **préréglage**. Le **préréglage** décrit les instructions détaillées concernant les opérations de traitement vidéo et/ou audio qui doivent être utilisées pour générer l’objet **TransformOutput** souhaité. L’exemple décrit dans cet article utilise un préréglage appelé **AdaptiveStreaming**. Le préréglage encode la vidéo d’entrée dans une échelle des vitesses de transmission générée automatiquement (paires vitesse de transmission-résolution) basée sur la vitesse de transmission et la résolution de la sortie, et crée des fichiers MP4 ISO avec des fichiers audio AAC et des fichiers vidéo H.264 qui correspondent à chaque paire vitesse de transmission-résolution. Pour plus d’informations sur ce préréglage, consultez [Encode with an auto-generated bitrate ladder](autogen-bitrate-ladder.md) (Encoder avec une échelle des vitesses de transmission générée automatiquement).

Vous pouvez utiliser un préréglage EncoderNamedPreset intégré ou des préréglages personnalisés. 

> [!Note]
> Lorsque vous créez une [transformation](https://docs.microsoft.com/rest/api/media/transforms), vous devez tout d’abord vérifier s’il en existe déjà une à l’aide de la méthode **Get**. Ce tutoriel suppose que vous créez la transformation avec un nom unique.

1. Dans la fenêtre de gauche de Postman, sélectionnez « Encodage et analyse ».
2. Sélectionnez ensuite « Créer transformation ».
3. Appuyez sur **Envoyer**.

    * L’opération **PUT** suivante est envoyée.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
        ```
    * L’opération a le corps suivant :

        ```json
        {
            "properties": {
                "description": "Basic Transform using an Adaptive Streaming encoding preset from the libray of built-in Standard Encoder presets",
                "outputs": [
                    {
                    "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                    "preset": {
                        "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                        "presetName": "AdaptiveStreaming"
                    }
                    }
                ]
            }
        }
        ```

### <a name="create-a-job"></a>Création d’un travail

Un [travail](https://docs.microsoft.com/rest/api/media/jobs) est la requête réelle envoyée à Media Services pour appliquer cette **transformation** à un contenu vidéo ou audio d’entrée donné. Le **travail** spécifie des informations telles que l’emplacement de la vidéo d’entrée et celui de la sortie.

Dans cet exemple, l’entrée du travail est basée sur une URL HTTPS (« https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/ »).

1. Dans la fenêtre de gauche de Postman, sélectionnez « Encodage et analyse ».
2. Ensuite, sélectionnez « Créer ou mettre à jour un travail ».
3. Appuyez sur **Envoyer**.

    * L’opération **PUT** suivante est envoyée.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
        ```
    * L’opération a le corps suivant :

        ```json
        {
        "properties": {
            "input": {
            "@odata.type": "#Microsoft.Media.JobInputHttp",
            "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
            "files": [
                    "Ignite-short.mp4"
                ]
            },
            "outputs": [
            {
                "@odata.type": "#Microsoft.Media.JobOutputAsset",
                "assetName": "testAsset1"
            }
            ]
        }
        }
        ```

Le travail prend du temps à se terminer et vous voulez être prévenu lorsque c’est le cas. Pour afficher la progression du travail, nous vous recommandons d’utiliser Event Grid. Ce dernier est conçu pour une haute disponibilité, des performances cohérentes et une mise à l’échelle dynamique. Avec Event Grid, vos applications peuvent écouter les événements de presque tous les services Azure ou de toute source personnalisée, et y réagir. La gestion simple et réactive des événements basée sur HTTP vous aide à générer des solutions efficaces grâce au filtrage et au routage intelligents des événements.  Consultez [Acheminer des événements Azure Media Services vers un point de terminaison personnalisé à l’aide de CLI](job-state-events-cli-how-to.md).

Le **travail** passe généralement par les états suivants : **Planifié**, **En attente**,  **Traitement en cours**, **Terminé** (l’état final). Si le travail a rencontré une erreur, vous obtenez l’état **Erreur**. Si le travail est en cours d’annulation, vous obtenez **Annulation en cours** et **Annulé** une fois l’opération terminée.

### <a name="create-a-streaming-locator"></a>Créer un localisateur de diffusion en continu

Une fois le travail d’encodage terminé, l’étape suivante consiste à mettre à la disposition des clients la vidéo dans la ressource de sortie pour qu’ils puissent la lire. Vous pouvez le faire en deux étapes : d’abord, créez un élément [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), puis générez les URL de diffusion en continu que les clients peuvent utiliser. 

Le processus de création d’un élément **StreamingLocator** est appelée publication. Par défaut, l’élément **StreamingLocator** est valide immédiatement après avoir effectué les appels d’API et dure jusqu’à ce qu’il soit supprimé, sauf si vous configurez les durées de début et de fin optionnelles. 

Lors de la création d’un élément [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), vous devez spécifier le **StreamingPolicyName** souhaité. Dans cet exemple, vous allez diffuser en continu du contenu en clair (ou non chiffré), la stratégie de diffusion en continu en clair prédéfinie (**PredefinedStreamingPolicy.ClearStreamingOnly**) est donc utilisée.

> [!IMPORTANT]
> Lorsque vous utilisez une stratégie [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) personnalisée, vous devez concevoir un ensemble limité de ces stratégies pour votre compte Media Services et les réutiliser pour vos éléments StreamingLocators chaque fois que les mêmes protocoles et options de chiffrement sont nécessaires. 

Votre compte Media Services a un quota en matière de nombre d’entrées de stratégie StreamingPolicy. Vous ne devez pas créer une stratégie StreamingPolicy pour chaque élément StreamingLocator.

1. Dans la fenêtre de gauche de Postman, sélectionnez « Stratégies de diffusion en continu ».
2. Ensuite, sélectionnez « Créer un localisateur de diffusion en continu ».
3. Appuyez sur **Envoyer**.

    * L’opération **PUT** suivante est envoyée.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingPolicies/:streamingPolicyName?api-version={{api-version}}
        ```
    * L’opération a le corps suivant :

        ```json
        {
            "properties":{
            "assetName": "{{assetName}}",
            "streamingPolicyName": "{{streamingPolicyName}}"
            }
        }
        ```

### <a name="list-paths-and-build-streaming-urls"></a>Répertorier les chemins d’accès et générer l’URL de diffusion en continu

#### <a name="list-paths"></a>Répertorier les chemins d’accès

Maintenant qu’un élément [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) a été créé, vous pouvez obtenir les URL de diffusion en continu

1. Dans la fenêtre de gauche de Postman, sélectionnez « Stratégies de diffusion en continu ».
2. Ensuite, sélectionnez « Répertorier les chemin d’accès ».
3. Appuyez sur **Envoyer**.

    * L’opération **POST** suivante est envoyée.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
        ```
        
    * L'opération n'a pas de corps :
        
4. Prenez note de l’un des chemins d’accès que vous souhaitez utiliser pour la diffusion en continu, vous allez l’utiliser dans la section suivante. Dans ce cas, les chemins d’accès suivants ont été retournés :
    
    ```
    "streamingPaths": [
        {
            "streamingProtocol": "Hls",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)"
            ]
        },
        {
            "streamingProtocol": "Dash",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=mpd-time-csf)"
            ]
        },
        {
            "streamingProtocol": "SmoothStreaming",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest"
            ]
        }
    ]
    ```

#### <a name="build-the-streaming-urls"></a>Création des URL de diffusion

Dans cette section, nous allons créer une URL de diffusion HLS. Les URL se composent des valeurs suivantes :

1. Le protocole sur lequel les données sont envoyées. Dans ce cas « https ».

    > [!NOTE]
    > Si un lecteur est hébergé sur un site https, veillez à mettre à jour l’URL vers « https ».

2. Le nom d’hôte de StreamingEndpoint. Dans ce cas, le nom est « amsaccount-usw22.streaming.media.azure.net ».

    Pour obtenir le nom d’hôte, vous pouvez utiliser l’opération GET suivante :
    
    ```
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingEndpoints/default?api-version={{api-version}}
    ```
    
3. Un chemin d’accès que vous avez obtenu dans la section précédente (Répertorier les chemins d’accès).  

Par conséquent, l’URL HLS suivante a été générée

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>Tester l’URL de diffusion en continu


> [!NOTE]
> Assurez-vous que le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu est en cours d’exécution.

Pour tester la diffusion en continu, cet article utilise le lecteur multimédia Azure. 

1. Ouvrez un navigateur web et accédez à [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Dans la zone **URL :**, collez l’URL que vous avez créée. 
3. Appuyez sur **Mise à jour du Lecteur Windows Media**.

Le lecteur multimédia Azure peut être utilisé pour effectuer des tests, mais ne doit pas être utilisé dans un environnement de production. 

## <a name="clean-up-resources-in-your-media-services-account"></a>Supprimer les ressources de votre compte Media Services

En règle générale, vous devez supprimer tous les éléments à l’exception des objets que vous envisagez de réutiliser (habituellement, vous allez réutiliser les transformations et conserver les éléments StreamingLocators, etc.). Si vous souhaitez que votre compte soit propre après avoir effectué vos expériences, vous devez supprimer les ressources que vous n’envisagez pas de réutiliser.  

Pour supprimer une ressource, sélectionnez l’opération « Supprimer... » sous la ressource que vous souhaitez supprimer.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment les comptes de stockage et Media Services que vous avez créés pour ce didacticiel, supprimez le groupe de ressources créé précédemment. Vous pouvez utiliser l’outil **CloudShell**.

Dans **CloudShell**, exécutez la commande suivante :

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment charger, encoder et diffuser en continu votre vidéo, consultez l’article suivant : 

> [!div class="nextstepaction"]
> [Analyser des vidéos](analyze-videos-tutorial-with-api.md)
