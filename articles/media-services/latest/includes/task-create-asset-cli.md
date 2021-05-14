---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 76c6a67b0bad6095a6340ccab4fbd49c7cad1698
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107893227"
---
<!--Create a media services asset CLI-->

La commande Azure CLI suivante crée un actif multimédia Media Services. Remplacez les valeurs `assetName`, `amsAccountName` et `resourceGroup` par les valeurs que vous utilisez actuellement.

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
