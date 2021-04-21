---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: ffd053b98a54d3e23eec62427f5c3d82df58954d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799792"
---
<!-- List and set subscriptions -->

1. Obtenez une liste de vos abonnements à l’aide de la commande [az account list](/cli/azure/account#az_account_list) :

    ```
    az account list --output table
    ```

2. Utilisez `az account set` avec l’ID d’abonnement ou le nom avec lequel vous souhaitez effectuer le changement.

    ```
    az account set --subscription "My Demos"
    ```
