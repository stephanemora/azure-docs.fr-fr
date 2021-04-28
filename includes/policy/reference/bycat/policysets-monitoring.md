---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f8095fc7c9be6ea23a573de8a5e871965d6c2a66
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866031"
---
|Nom |Description |Stratégies |Version |
|---|---|---|---|
|[\[Préversion\] : Déployer - Configurer les prérequis pour activer les agents Azure Monitor et de sécurité Azure sur les machines virtuelles](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Configurez des machines de manière à installer automatiquement les agents Azure Monitor et Azure Security. Security Center collecte les événements provenant des agents, et les utilise pour communiquer des alertes de sécurité et des tâches personnalisées de sécurisation renforcée (recommandations). Créez un groupe de ressources et un espace de travail Log Analytics dans la même région que la machine pour stocker les enregistrements d’audit. Cette stratégie s’applique uniquement aux machines virtuelles de certaines régions. |5 |1.0.0-preview |
|[Activer Azure Monitor pour les groupes de machines virtuelles identiques](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Activez Azure Monitor pour les groupes de machines virtuelles identiques dans l’étendue spécifiée (groupe d’administration, abonnement ou groupe de ressources). Utilise l’espace de travail Log Analytics comme paramètre. Remarque : Si la valeur upgradePolicy du groupe identique est définie sur Manuel, vous devez appliquer l’extension à toutes les machines virtuelles du groupe en appelant une mise à niveau. Dans l’interface de ligne de commande, cela se traduirait par az vmss update-instances. |6 |1.0.1 |
|[Activer Azure Monitor pour machines virtuelles](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Activez Azure Monitor pour les machines virtuelles dans l’étendue spécifiée (groupe d’administration, abonnement ou groupe de ressources). Utilise l’espace de travail Log Analytics comme paramètre. |10 |2.0.0 |
