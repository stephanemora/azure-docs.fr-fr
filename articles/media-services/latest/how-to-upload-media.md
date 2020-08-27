---
title: Charger des éléments multimédia
titleSuffix: Azure Media Services
description: Découvrez comment charger des médias pour le streaming ou l’encodage.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: inhenkel
ms.openlocfilehash: a046a3caba9a0909d873356bda8d1fa6cf1f9860
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719255"
---
# <a name="upload-media-for-streaming-or-encoding"></a>Charger des médias pour le streaming ou l’encodage

Dans Media Services, vous chargez vos fichiers numériques (médias) dans le conteneur d’objets blob associé à une ressource. L’entité [Asset](/rest/api/media/operations/asset) peut contenir des fichiers vidéo et audio, des images, des collections de miniatures, des pistes textuelles et des légendes (et les métadonnées concernant ces fichiers). Une fois les fichiers chargés dans le conteneur de la ressource, votre contenu est stocké en toute sécurité dans le cloud et peut faire l'objet d'un traitement et d'une diffusion en continu.

Avant de commencer, vous devez cependant recueillir ou réfléchir à quelques valeurs.

1. Le chemin local du fichier à charger
1. L’ID de la ressource (conteneur)
1. Le nom que vous souhaitez attribuer au fichier chargé, sans oublier l’extension
1. Le nom du compte de stockage que vous utilisez
1. La clé d'accès de votre compte de stockage

## <a name="portal"></a>[Portail](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="rest"></a>[REST](#tab/rest/)

Une fois que vous avez [créé une ressource à l’aide de Postman ou d’une autre méthode REST et obtenu l’URL SAS de la ressource](how-to-create-asset.md?tabs=rest), utilisez les API Stockage Azure ou des kits SDK (par exemple, l’[API REST Stockage](../../storage/common/storage-rest-api-auth.md) ou le [SDK .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

---
<!-- add these to the tabs when available -->
Pour les autres méthodes, consultez la [documentation Stockage Azure](https://docs.microsoft.com/azure/storage/blobs/) en vue d’utiliser des objets blob avec [.NET](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet), [Java](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-java), [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python) et [JavaScript (Node.js)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-nodejs).

## <a name="next-steps"></a>Étapes suivantes

> [Présentation de Media Services](media-services-overview.md)
