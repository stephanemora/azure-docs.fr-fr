---
title: Fichier include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: a20a4a4b3c618a03ba7b5f788395bb541d2c143d
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371864"
---
Obtenez les informations suivantes sur l’emplacement personnalisé auprès de votre administrateur de cluster (voir [Créer un emplacement personnalisé](../articles/app-service/manage-create-arc-environment.md#create-a-custom-location)).

```azurecli-interactive
customLocationGroup="<resource-group-containing-custom-location>"
customLocationName=<name-of-custom-location>
```

Obtenez l’ID d’emplacement personnalisé pour la prochaine étape.

```azurecli-interactive
customLocationId=$(az customlocation show \
    --resource-group $customLocationGroup \
    --name $customLocationName \
    --query id \
    --output tsv)
```