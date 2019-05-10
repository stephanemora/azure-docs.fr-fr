---
title: Analyser des vidéos avec Media Services à l’aide de .NET - Azure | Microsoft Docs
description: Suivez les étapes de ce didacticiel pour analyser des vidéos à l’aide d’Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 04/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: cf1ea32e9b023db2e1a066c2baa6adf46ab51066
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152745"
---
# <a name="tutorial-analyze-videos-with-media-services-v3-using-net"></a>Didacticiel : Analyser des vidéos avec Media Services v3 à l’aide de .NET

Ce didacticiel vous montre comment analyser des vidéos avec Azure Media Services. Il existe plusieurs scénarios dans lesquels vous pouvez souhaiter obtenir des informations détaillées sur vos vidéos enregistrées ou vos contenus audio. Par exemple, pour obtenir une satisfaction plus élevée des clients, les organisations peuvent exécuter un traitement de reconnaissance vocale pour convertir les enregistrements du support client en un catalogue pouvant faire l’objet de recherches, avec des index et des tableaux de bord. Ensuite, elles peuvent obtenir des informations sur leur entreprise, comme la liste des réclamations courantes avec leurs sources, et d’autres informations utiles.

Ce didacticiel vous explique les procédures suivantes :    

> [!div class="checklist"]
> * Télécharger l’exemple d’application décrit dans la rubrique
> * Examiner le code qui analyse la vidéo spécifiée
> * Exécution de l'application
> * Analyser la sortie
> * Supprimer des ressources

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas Visual Studio, vous pouvez obtenir [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Créer un compte Media Services](create-account-cli-how-to.md).<br/>Veillez à mémoriser les valeurs que vous avez utilisées pour le nom du groupe de ressources et le nom du compte Media Services.
- Suivez les étapes décrites dans [Accéder à l’API Azure Media Services avec Azure CLI](access-api-cli-how-to.md) et enregistrez les informations d’identification. Vous en aurez besoin pour accéder à l’API.

## <a name="download-and-configure-the-sample"></a>Télécharger et configurer l’exemple

Clonez un référentiel GitHub qui contient l’exemple .NET sur votre machine à l’aide de la commande suivante :  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

L’exemple se trouve dans le dossier [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

Ouvrez [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) dans votre projet téléchargé. Remplacez les valeurs par les informations d’identification que vous avez obtenues en [accédant aux API](access-api-cli-how-to.md).

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>Examiner le code qui analyse la vidéo spécifiée

Cette section examine les fonctions définies dans le fichier [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) du projet *AnalyzeVideos*.

L’exemple effectue les actions suivantes :

1. Crée une **transformation** et un **travail** qui analyse votre vidéo.
2. Crée une **actif multimédia** d’entrée et charge la vidéo dans celui-ci. La ressource d’entrée est utilisée en tant qu’entrée du travail.
3. Créer une ressource de sortie qui stocke la sortie du travail. 
4. Soumettre le travail.
5. Vérifier l’état du travail.
6. Télécharger les fichiers qui résultent de l’exécution du travail. 

> [!NOTE]
> Lorsque vous utilisez des présélections pour l’analyseur vidéo ou audio, utilisez le Portail Azure pour paramétrer votre compte de sorte à ce qu’il dispose de 10 unités réservées Multimédia S3. Pour plus d’informations, consultez [Vue d’ensemble de la mise à l’échelle du traitement multimédia](media-reserved-units-cli-how-to.md).

### <a name="start-using-media-services-apis-with-net-sdk"></a>Commencer à utiliser les API Media Services avec le Kit de développement logiciel (SDK) .NET

Pour commencer à utiliser les API Media Services avec .NET, vous devez créer un objet **AzureMediaServicesClient**. Pour créer l’objet, vous devez fournir les informations d’identification nécessaires pour que le client puisse se connecter à Azure à l’aide d’Azure AD. Dans le code que vous avez cloné au début de l’article, la fonction **GetCredentialsAsync** crée l’objet ServiceClientCredentials basé sur les informations d’identification fournies dans le fichier de configuration local. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Créer une ressource d’entrée et charger un fichier local dans celle-ci 

La fonction **CreateInputAsset** crée une nouvelle [ressource](https://docs.microsoft.com/rest/api/media/assets) d’entrée et charge le fichier vidéo local spécifié dans celle-ci. Cette ressource est utilisée en tant qu’entrée de votre travail d’encodage. Dans Media Services v3, l’entrée d’un travail peut être une ressource ou bien le contenu que vous mettez à la disposition de votre compte Media Services via des URL HTTPS. Si vous souhaitez savoir comment encoder à partir d’une URL HTTPS, consultez [cet](job-input-from-http-how-to.md) article.  

Dans Media Services v3, vous utilisez des API Stockage Azure pour charger des fichiers. L’extrait de code .NET suivant vous explique comment faire.

La fonction suivante effectue les actions ci-après :

* Elle crée une ressource 
* Elle récupère une [URL SAP](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) accessible en écriture vers le [conteneur de stockage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) de la ressource
* Elle charge le fichier dans le conteneur de stockage à l’aide de l’URL SAP

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Créer une ressource de sortie pour stocker le résultat du travail 

La [ressource](https://docs.microsoft.com/rest/api/media/assets) de sortie stocke le résultat de votre travail. Le projet définit la fonction **DownloadResults** qui télécharge les résultats à partir de cette ressource de sortie dans le dossier « output », afin de voir ce que vous avez obtenu.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Créer une transformation et un travail qui analyse les vidéos

Lors de l’encodage ou du traitement de contenus dans Media Services, il est courant de configurer les paramètres de codage en tant que formule. Vous envoyez ensuite un **travail** pour appliquer cette formule à une vidéo. En envoyant de nouveaux travaux pour chaque nouvelle vidéo, vous appliquez cette formule à toutes les vidéos de votre bibliothèque. Une formule dans Media Services est référencée comme une **transformation**. Pour plus d’informations, consultez [Transformations et travaux](transform-concept.md). L’exemple décrit dans ce didacticiel définit une recette qui analyse la vidéo spécifiée. 

#### <a name="transform"></a>Transformer

Lorsque vous créez une instance de [transformation](https://docs.microsoft.com/rest/api/media/transforms), vous devez spécifier ce qu’elle doit produire comme sortie. Le paramètre requis est un objet **TransformOutput**, comme indiqué dans le code ci-dessus. Chaque objet **TransformOutput** contient un **préréglage**. Le **préréglage** décrit les instructions détaillées concernant les opérations de traitement vidéo et/ou audio qui doivent être utilisées pour générer l’objet **TransformOutput** souhaité. Dans cet exemple, le préréglage **VideoAnalyzerPreset** est utilisé et la langue (« fr-fr ») est passée à son constructeur. Ce préréglage vous permet d’extraire plusieurs insights audio et vidéo à partir d’une vidéo. Vous pouvez utiliser le préréglage **AudioAnalyzerPreset** si vous avez besoin d’extraire plusieurs insights audio à partir d’une vidéo. 

Lorsque vous créez une **transformation**, vous devez tout d’abord vérifier s’il en existe déjà une à l’aide de la méthode **Get**, comme indiqué dans le code qui suit.  Dans Media Services v3, les méthodes **Get** appliquées sur les entités renvoient **null** si l’entité n’existe pas (une vérification du nom respectant la casse).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Travail

Comme indiqué ci-dessus, l’objet [Transformation](https://docs.microsoft.com/rest/api/media/transforms) est la formule et un [travail](https://docs.microsoft.com/rest/api/media/jobs) est la requête réelle envoyée à Media Services pour appliquer cette **transformation** à un contenu vidéo ou audio d’entrée donné. Le **travail** spécifie des informations telles que l’emplacement de la vidéo d’entrée et celui de la sortie. Vous pouvez spécifier l’emplacement de votre vidéo en utilisant : URL HTTPS, URL SAS ou ressources situées dans votre compte de service multimédia. 

Dans cet exemple, l’entrée de la tâche est une vidéo locale.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Attendre la fin du travail

Le travail prend du temps à se terminer et vous voulez être prévenu lorsque c’est le cas. Il existe différentes options pour être prévenu de l’achèvement du [travail](https://docs.microsoft.com/rest/api/media/jobs). L’option la plus simple (indiquée ici) est d’utiliser l’interrogation. 

L’interrogation n’est pas une meilleure pratique recommandée pour les applications de production en raison de la latence potentielle. L’interrogation peut être limitée si elle est utilisée de façon excessive sur un compte. À la place, les développeurs doivent utiliser Event Grid.

Event Grid est conçu pour une haute disponibilité, des performances cohérentes et une mise à l’échelle dynamique. Avec Event Grid, vos applications peuvent écouter les événements de presque tous les services Azure ou de toute source personnalisée, et y réagir. La gestion simple et réactive des événements basée sur HTTP vous aide à générer des solutions efficaces grâce au filtrage et au routage intelligents des événements. Consultez [Acheminer des événements Azure Media Services vers un point de terminaison personnalisé à l’aide de CLI](job-state-events-cli-how-to.md).

Le **travail** passe généralement par les états suivants : **Planifié**, **En attente**,  **Traitement en cours**, **Terminé** (l’état final). Si le travail a rencontré une erreur, vous obtenez l’état **Erreur**. Si le travail est en cours d’annulation, vous obtenez **Annulation en cours** et **Annulé** une fois l’opération terminée.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]


### <a name="job-error-codes"></a>Codes d’erreur des tâches

Consultez [Codes d’erreur](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Télécharger le résultat du travail

La fonction suivante télécharge les résultats à partir de la [ressource](https://docs.microsoft.com/rest/api/media/assets) de sortie dans le dossier « sortie », afin que vous puissiez examiner les résultats du travail. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Supprimer les ressources de votre compte Media Services

En règle générale, vous devez supprimer tous les éléments à l’exception des objets que vous envisagez de réutiliser (habituellement, vous allez réutiliser les transformations et conserver les éléments StreamingLocators). Si vous souhaitez que votre compte soit propre après avoir effectué vos expériences, vous devez supprimer les ressources que vous n’envisagez pas de réutiliser. Par exemple, le code suivant supprime les travaux.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Appuyez sur Ctrl+F5 pour exécuter l’application *AnalyzeVideos*.

Lorsque nous exécutons le programme, le travail génère des miniatures pour chaque visage trouvé dans la vidéo. Il génère également le fichier insights.json.

## <a name="examine-the-output"></a>Analyser la sortie

Le fichier de sortie de l’analyse de vidéos est nommé insights.json. Ce fichier contient des insights sur votre vidéo. Vous trouverez une description des éléments trouvés au sein du fichier json dans l’article [Intelligence multimédia](intelligence-concept.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources de votre groupe de ressources, notamment les comptes de stockage et Media Services que vous avez créés pour ce didacticiel, supprimez le groupe de ressources créé précédemment. 

Exécutez la commande CLI suivante :

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Multithreading

Les Kits de développement logiciel (SDK) Azure Media Services v3 ne sont pas thread-safe. Lorsque vous travaillez avec une application multithread, vous devez générer un nouvel objet AzureMediaServicesClient par thread.

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Charger, encoder et diffuser en continu des fichiers](stream-files-tutorial-with-api.md)
