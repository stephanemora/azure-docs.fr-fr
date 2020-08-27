---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: dcd2cda3bad2a13a83c5f3f6700e5a57471e2065
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653876"
---
<!--Create a media services asset REST-->

La commande suivante de Azure .NET crée un nouvel actif Media Services. Remplacez les valeurs `subscriptionID`, `resourceGroup` et `amsAccountName` par les valeurs que vous utilisez actuellement. Donnez un nom à votre ressource en définissant `assetName` ici.

[!code-csharp[Main](../../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]
