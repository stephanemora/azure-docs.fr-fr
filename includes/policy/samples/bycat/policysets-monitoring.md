---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/26/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 191b2fc8d5ade6181bf9ec50c8b9eb654967a320
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82190746"
---
|Name |Description |Stratégies |Version |
|---|---|---|---|
|[Activer Azure Monitor pour les groupes de machines virtuelles identiques](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Activez Azure Monitor pour les groupes de machines virtuelles identiques dans l’étendue spécifiée (groupe d’administration, abonnement ou groupe de ressources). Utilise l’espace de travail Log Analytics comme paramètre. Remarque : Si la valeur upgradePolicy du groupe identique est définie sur Manuel, vous devez appliquer l’extension à toutes les machines virtuelles du groupe en appelant une mise à niveau. Dans l’interface de ligne de commande, cela se traduirait par az vmss update-instances. |6 |1.0.1 |
|[Activer Azure Monitor pour machines virtuelles](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Activez Azure Monitor pour machines virtuelles dans l’étendue spécifiée (groupe d’administration, abonnement ou groupe de ressources). Utilise l’espace de travail Log Analytics comme paramètre. |6 |1.0.1 |
