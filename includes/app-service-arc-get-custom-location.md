---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: d4d6783645f719f21d97f18abc9bc900a368fd8f
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112362012"
---
Obtenez les informations suivantes sur l’emplacement personnalisé auprès de votre administrateur de cluster (voir [Créer un emplacement personnalisé](../articles/app-service/manage-create-arc-environment.md#create-a-custom-location)).

```azurecli-interactive
customLocationGroup="<resource-group-containing-custom-location>"
customLocationName="<name-of-custom-location>"
```

Obtenez l’ID d’emplacement personnalisé pour la prochaine étape.

```azurecli-interactive
customLocationId=$(az customlocation show \
    --resource-group $customLocationGroup \
    --name $customLocationName \
    --query id \
    --output tsv)
```
