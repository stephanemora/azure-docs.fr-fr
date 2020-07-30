---
title: Créer une entrée de travail Azure Media Services à partir d’un fichier local | Microsoft Docs
description: Cet article explique comment créer une entrée de travail Azure Media Services à partir d’un fichier local.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: a635a75a64db59fc56c6890f79af967a58735853
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001126"
---
# <a name="create-a-job-input-from-a-local-file"></a>Créer une entrée de travail à partir d’un fichier local

Dans Media Services v3, lorsque vous soumettez des travaux pour traiter vos vidéos, vous devez indiquer à Media Services où trouver la vidéo d’entrée. La vidéo d’entrée peut être stockée sous la forme d’une ressource Media Service, dans laquelle vous créez une ressource d’entrée basée sur un fichier (stocké en local ou dans le stockage Blob Azure). Cette rubrique montre comment créer une entrée de travail à partir d’un fichier local. Pour obtenir un exemple complet, consultez cet [exemple GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Prérequis 

* [Créer un compte Media Services](./create-account-howto.md).
* Passer en revue [Gérer les ressources](manage-asset-concept.md).

## <a name="net-sample"></a>Exemple de code .NET

Le code suivant montre comment créer une ressource d’entrée et l’utiliser comme entrée pour le travail. La fonction CreateInputAsset effectue les actions suivantes :

* Elle crée la ressource
* Elle récupère une [URL SAP](../../storage/common/storage-sas-overview.md) accessible en écriture vers le [conteneur de stockage](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) de la ressource
* Elle charge le fichier dans le conteneur de stockage à l’aide de l’URL SAP

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

L’extrait de code suivant crée un élément multimédia de sortie s’il n’existe pas déjà :

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

L'extrait de code suivant envoie un travail d'encodage :

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Codes d’erreur des tâches

Consultez [Codes d’erreur](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Étapes suivantes

[Créer une entrée de travail à partir d’une URL HTTPS](job-input-from-http-how-to.md).
