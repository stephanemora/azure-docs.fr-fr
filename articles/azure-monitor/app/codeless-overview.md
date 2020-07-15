---
title: Superviser vos applications sans modification du code - Instrumentation automatique pour Azure Monitor Application Insights | Microsoft Docs
description: Présentation de l’instrumentation automatique Azure Monitor Application Insights - Gestion des performances des applications sans code
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 87342dcd316b0364522baa01e632b704665c998e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85363734"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Qu’est-ce que l’instrumentation automatique ou l’attachement sans code Azure Monitor Application Insights ?

L’auto-instrumentation, ou l’attachement sans code, vous permet d’activer la supervision des applications avec Application Insights sans modifier votre code.  

Application Insights est intégré à différents fournisseurs de ressources et fonctionne sur différents environnements. En substance, il vous suffit d’activer et, dans certains cas, de configurer l’agent, qui collecte ensuite les données de télémétrie directement. En un rien de temps, vous voyez les métriques, les données et les dépendances dans votre ressource Application Insights. Ainsi, vous pouvez localiser la source des problèmes potentiels avant qu’ils se produisent et analyser la cause racine avec un affichage des transactions de bout en bout.

## <a name="supported-environments-languages-and-resource-providers"></a>Environnements, langages et fournisseurs de ressources pris en charge

Avec l’ajout d’un nombre de plus en plus important d’intégrations, la matrice des capacités de l’instrumentation automatique gagne en complexité. Le tableau ci-dessous montre l’état actuel de la question de la prise en charge de divers fournisseurs de ressources, langages et environnements.

|Environnement/fournisseur de ressources | .NET            | .NET Core       | Java            | Node.js         |
|------------------------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service sur Windows  | Disponibilité générale, APD*       | Disponibilité générale, consentement      | Version préliminaire privée | Version préliminaire privée |
|Azure App Service sur Linux    | N/A             | Non pris en charge   | Version préliminaire publique  | Version préliminaire publique  |
|Azure App Service sur AKS      | N/A             | Intégré       | Intégré       | Intégré       |
|Azure Functions - De base       | Disponibilité générale, APD*       | Disponibilité générale, APD*       | Disponibilité générale, APD*       | Disponibilité générale, APD*       |
|Azure Functions - Dépendances| Non pris en charge   | Non pris en charge   | Version préliminaire publique  | Non pris en charge   |
|Azure Kubernetes Service      | N/A             | Intégré       | Via l’agent   | Intégré       |
|Machines virtuelles Azure - Windows             | Version préliminaire publique  | Non pris en charge   | Non pris en charge   | Non pris en charge   |
|Machines virtuelles locales - Windows       | Disponibilité générale, consentement      | Non pris en charge   | Via l’agent   | Non pris en charge   |
|Agent autonome - Tous les env.   | Non pris en charge   | Non pris en charge   | Version préliminaire publique  | Non pris en charge   |

*APD signifie Activé par défaut : Application Insights est activé automatiquement une fois que vous avez déployé votre application dans les environnements pris en charge. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

[La supervision des applications sur Azure App Service](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=net) est disponible pour l’application .NET et est activée par défaut. .NET Core peut être activé en un seul clic, et Java et Node.js sont en préversion privée.

### <a name="linux"></a>Linux 

La supervision des applications Java et Node.js dans App Service est en préversion publique et peut être activée dans le Portail Azure. Elle est disponible dans toutes les régions.

## <a name="azure-functions"></a>Azure Functions

La supervision de base pour Azure Functions est activée par défaut afin de collecter les données de journal, de performances et d’erreur ainsi que les requêtes HTTP. Pour les applications Java, vous pouvez activer une supervision plus riche avec le suivi distribué et obtenir les détails sur les transactions de bout en bout. Cette fonctionnalité pour Java est en préversion publique et vous pouvez [l’activer dans le Portail Azure](https://docs.microsoft.com/azure/azure-monitor/app/monitor-functions).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

L’instrumentation sans code dans Azure Kubernetes Service est actuellement disponible pour les applications Java via l’[agent autonome](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Machines virtuelles Windows Azure et groupe de machines virtuelles identiques

[L’auto-instrumentation pour les machines virtuelles Azure et le groupe de machines virtuelles identiques](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps) est disponible pour les applications .NET 

## <a name="on-premises-servers"></a>Serveurs locaux
Vous pouvez facilement activer la supervision de vos [serveurs Windows locaux pour les applications .NET](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) et pour les [applications Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent).

## <a name="other-environments"></a>Autres environnements
L’agent autonome Java polyvalent fonctionne dans n’importe quel environnement. Il n’est pas nécessaire d’instrumenter votre code. [Suivez le guide](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) pour activer Application Insights et en savoir plus sur les fonctionnalités étonnantes de l’agent Java. L’agent est en préversion publique et disponible dans toutes les régions. 

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* [Plan de l’application](./../../azure-monitor/app/app-map.md)
* [Analyse des performances de bout en bout](./../../azure-monitor/learn/tutorial-performance.md)
