---
title: Charger des fichiers vers un compte Media Services dans le portail Azure | Microsoft Docs
description: Ce didacticiel vous indique les étapes à suivre pour charger des fichiers vers un compte Media Services dans le portail Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: a0e4b7e759e27dafb1d847dc5d7ce464b440e98c
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960654"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Charger des fichiers vers un compte Media Services dans le portail Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portail](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 

> [!NOTE]
> Aucune nouvelle fonctionnalité ni fonction n’est ajoutée à Media Services v2. Pour obtenir les fichiers de téléchargement à jour via le portail, consultez [Utiliser le portail pour charger, encoder et diffuser du contenu](../latest/asset-create-asset-upload-portal-quickstart.md).<br/>Consultez également [Media Services v3](../latest/index.yml). Consultez aussi [Conseils de migration de v2 vers v3](../latest/migrate-v-2-v-3-migration-introduction.md).

Dans Azure Media Services, vous chargez vos fichiers numériques vers une ressource. Cette ressource peut contenir des fichiers vidéo, des fichiers audio, des images, des collections de miniatures, des pistes textuelles et des fichiers de sous-titres (ainsi que les métadonnées concernant ces fichiers). Une fois les fichiers chargés, votre contenu est stocké en toute sécurité dans le cloud, à des fins de traitement approfondi et de diffusion en continu.

Media Services définit une taille de fichier maximale pour le traitement des fichiers. Pour obtenir des informations détaillées sur les limites de taille de fichier, voir [Quotas et limitations](media-services-quotas-and-limitations.md).

Pour suivre ce tutoriel, vous avez besoin d’un compte Azure. Pour plus d’informations, consultez la page [Version d’évaluation gratuite d’Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="upload-files"></a>Charger des fichiers
1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Sélectionnez **Paramètres** > **Éléments multimédias**. Ensuite, cliquez sur le bouton **Charger**.
   
    ![Charger des fichiers](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    La fenêtre **Upload a video asset** (Charger un élément multimédia vidéo) s’affiche.
   
   > [!NOTE]
   > Media Services ne limite pas la taille de fichier des vidéos chargées.
 
3. Sur votre ordinateur, accédez à la vidéo que vous souhaitez charger. Sélectionnez la vidéo, puis cliquez sur **OK**.  
   
    Le chargement démarre. La progression s’affiche sous le nom du fichier.  

Une fois le chargement terminé, la nouvelle ressource est répertoriée dans le volet **Ressources**. 

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Étapes suivantes
* Vous pouvez désormais [encoder vos ressources chargées](media-services-portal-encode.md).

* Vous pouvez également utiliser Azure Functions pour déclencher une tâche de codage lorsqu’un fichier arrive dans le conteneur configuré. Pour plus d’informations, consultez l’exemple dans [Media Services : Intégration d’Azure Media Services dans Azure Functions et Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/).
