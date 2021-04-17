---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: dcd2cda3bad2a13a83c5f3f6700e5a57471e2065
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88653876"
---
<!--Create a media services asset REST-->

La commande suivante de Azure .NET crée un nouvel actif Media Services. Remplacez les valeurs `subscriptionID`, `resourceGroup` et `amsAccountName` par les valeurs que vous utilisez actuellement. Donnez un nom à votre ressource en définissant `assetName` ici.

[!code-csharp[Main](../../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]
