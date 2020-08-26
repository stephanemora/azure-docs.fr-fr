---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 9e5b344f15a92e7ac40182f8fc7ae3ca667f63a4
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608653"
---
<!--Create a media services asset REST-->

La commande REST Azure suivante crée un nouveau compte Media Services. Remplacez les valeurs `subscriptionID`, `resourceGroup` et `amsAccountName` par les valeurs que vous utilisez actuellement. Donnez un nom à votre ressource en définissant `assetName` ici.

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```