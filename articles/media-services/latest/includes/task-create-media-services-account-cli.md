---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 5c0341087cdd348e973da5faaa1f90081780c9c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88602368"
---
<!--Create a media services account -->

La commande suivante de Azure CLI crée un nouveau compte Media Services. Vous pouvez remplacer les valeurs suivantes : `amsaccount` `storageaccountforams` (doit correspondre à la valeur donnée pour votre compte de stockage), et `amsResourceGroup` (doit correspondre à la valeur donnée pour le groupe de ressources).

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```