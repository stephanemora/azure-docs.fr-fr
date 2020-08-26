---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: 820353ffbb5e23e27c3039ad27a8f6507b9d2167
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608638"
---
<!--Create a media services asset REST-->

La commande suivante de Azure .NET crée un nouvel actif Media Services. Remplacez les valeurs `subscriptionID`, `resourceGroup` et `amsAccountName` par les valeurs que vous utilisez actuellement. Donnez un nom à votre ressource en définissant `assetName` ici.

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```
