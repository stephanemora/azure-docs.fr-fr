---
title: Superviser vos applications sans modification du code - Instrumentation automatique pour Azure Monitor Application Insights | Microsoft Docs
description: Présentation de l’instrumentation automatique Azure Monitor Application Insights - Gestion des performances des applications sans code
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 0dda015d820d81fdd13eced384f97362e2ee3339
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587554"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Qu’est-ce que l’instrumentation automatique ou l’attachement sans code Azure Monitor Application Insights ?

L’auto-instrumentation, ou l’attachement sans code, vous permet d’activer la supervision des applications avec Application Insights sans modifier votre code.  

Application Insights est intégré à différents fournisseurs de ressources et fonctionne sur différents environnements. En substance, il vous suffit d’activer et, dans certains cas, de configurer l’agent, qui collecte ensuite automatiquement les données de télémétrie. En un rien de temps, vous voyez les métriques, les données et les dépendances dans votre ressource Application Insights. Ainsi, vous pouvez localiser la source des problèmes potentiels avant qu’ils se produisent et analyser la cause racine avec un affichage des transactions de bout en bout.

## <a name="supported-environments-languages-and-resource-providers"></a>Environnements, langages et fournisseurs de ressources pris en charge

À mesure que nous ajoutons des intégrations supplémentaires, la matrice des capacités de l’instrumentation automatique gagne en complexité. Le tableau ci-dessous montre l’état actuel de la question de la prise en charge de divers fournisseurs de ressources, langages et environnements.

|Environnement/fournisseur de ressources          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service sur Windows           | Disponibilité générale, APD*       | Disponibilité générale, consentement      | En cours     | En cours     | Non pris en charge   |
|Azure App Service sur Linux             | N/A             | Non pris en charge   | En cours     | Version préliminaire publique  | Non pris en charge   |
|Azure App Service sur AKS               | N/A             | Intégré       | Intégré       | Intégré       | Non pris en charge   |
|Azure Functions - De base                | Disponibilité générale, APD*       | Disponibilité générale, APD*       | Disponibilité générale, APD*       | Disponibilité générale, APD*       | Disponibilité générale, APD*       |
|Azure Functions Windows – Dépendances | Non pris en charge   | Non pris en charge   | Version préliminaire publique  | Non pris en charge   | Non pris en charge   |
|Azure Kubernetes Service               | N/A             | Intégré       | Via l’agent   | Intégré       | Non pris en charge   |
|Machines virtuelles Azure - Windows                      | Version préliminaire publique  | Non pris en charge   | Non pris en charge   | Non pris en charge   | Non pris en charge   |
|Machines virtuelles locales - Windows                | Disponibilité générale, consentement      | Non pris en charge   | Via l’agent   | Non pris en charge   | Non pris en charge   |
|Agent autonome - Tous les env.            | Non pris en charge   | Non pris en charge   | GA              | Non pris en charge   | Non pris en charge   |

*APD signifie Activé par défaut : Application Insights est activé automatiquement une fois que vous avez déployé votre application dans les environnements pris en charge. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

#### <a name="net"></a>.NET
La surveillance des applications sur Azure App Service sur Windows est disponible pour les [applications .NET](./azure-web-apps.md?tabs=net) et est activée par défaut.

#### <a name="netcore"></a>.NETCore
La surveillance des [applications .NETCore](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=netcore) peut être activée en un seul clic.

#### <a name="java"></a>Java
L’intégration du portail pour la surveillance des applications Java sur App Service sur Windows n’est pas disponible pour le moment. Toutefois, vous pouvez ajouter l’[agent autonome Java 3.0](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) d’Application Insights à votre application sans modifier le code avant de déployer les applications sur App Service. L’agent Application Insights Java 3.0 est mis à la disposition générale.

#### <a name="nodejs"></a>Node.js
La surveillance des applications Node.js sur Windows ne peut pas être activée actuellement à partir du portail. Pour superviser les applications Node.js, utilisez le [Kit de développement logiciel (SDK)](https://docs.microsoft.com/azure/azure-monitor/app/nodejs).

### <a name="linux"></a>Linux

#### <a name="netcore"></a>.NETCore
Pour surveiller les applications .NETCore s’exécutant sur Linux, utilisent le [Kit de développement logiciel (SDK)](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

#### <a name="java"></a>Java 
L’activation de la surveillance des applications Java pour App Service sur Linux à partir du portail n’est pas disponible, mais vous pouvez ajouter [l’agent Java 3.0 d’Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) à votre application avant de déployer les applications sur App Service. L’agent Application Insights Java 3.0 est mis à la disposition générale.

#### <a name="nodejs"></a>Node.js
La [supervision des applications Node.js dans App Service sur Linux](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=nodejs) est en préversion publique et peut être activée dans Portail Azure. Elle est disponible dans toutes les régions. 

#### <a name="python"></a>Python
Utilisez le Kit de développement logiciel (SDK) pour [surveiller votre application Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python). 

## <a name="azure-functions"></a>Azure Functions

La supervision de base pour Azure Functions est activée par défaut afin de collecter les données de journal, de performances et d’erreur ainsi que les requêtes HTTP. Pour les applications Java, vous pouvez activer une supervision plus riche avec le suivi distribué et obtenir les détails sur les transactions de bout en bout. Cette fonctionnalité pour Java est en préversion publique et vous pouvez [l’activer dans le Portail Azure](./monitor-functions.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

L’instrumentation sans code dans Azure Kubernetes Service est actuellement disponible pour les applications Java via l’[agent autonome](./java-in-process-agent.md). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Machines virtuelles Windows Azure et groupe de machines virtuelles identiques

L’auto-instrumentation pour les machines virtuelles Azure et le groupe de machines virtuelles identiques est disponible pour [.NET](./azure-vm-vmss-apps.md) et [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent).  

## <a name="on-premises-servers"></a>Serveurs locaux
Vous pouvez facilement activer la supervision de vos [serveurs Windows locaux pour les applications .NET](./status-monitor-v2-overview.md) et pour les [applications Java](./java-in-process-agent.md).

## <a name="other-environments"></a>Autres environnements
L’agent autonome Java polyvalent fonctionne dans n’importe quel environnement. Il n’est pas nécessaire d’instrumenter votre code. [Suivez le guide](./java-in-process-agent.md) pour activer Application Insights et en savoir plus sur les fonctionnalités étonnantes de l’agent Java. L’agent est en préversion publique et disponible dans toutes les régions. 

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Application Insights](./app-insights-overview.md)
* [Plan de l’application](./app-map.md)
* [Analyse des performances de bout en bout](../app/tutorial-performance.md)

