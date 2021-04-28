---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 07b0897de3fce6a108836f1e72ce956a3a8092f1
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107893326"
---
<!--Create a media services account -->

La commande suivante de Azure CLI crée un nouveau compte Media Services. Vous pouvez remplacer les valeurs suivantes : `amsaccount` `storageaccountforams` (doit correspondre à la valeur donnée pour votre compte de stockage), et `amsResourceGroup` (doit correspondre à la valeur donnée pour le groupe de ressources).

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```
