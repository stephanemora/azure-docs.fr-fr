---
title: Superviser vos applications sans modification du code - Instrumentation automatique pour Azure Monitor Application Insights | Microsoft Docs
description: Présentation de l’instrumentation automatique Azure Monitor Application Insights - Gestion des performances des applications sans code
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/17/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 1c9d3e10ebf02016a0188617567cb2e4e2eeb036
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110092707"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Qu’est-ce que l’instrumentation automatique ou l’attachement sans code Azure Monitor Application Insights ?

L’auto-instrumentation, ou l’attachement sans code, vous permet d’activer la supervision des applications avec Application Insights sans modifier votre code.  

Application Insights est intégré à différents fournisseurs de ressources et fonctionne sur différents environnements. En substance, il vous suffit d’activer et, dans certains cas, de configurer l’agent, qui collecte ensuite automatiquement les données de télémétrie. En un rien de temps, vous voyez les métriques, les données et les dépendances dans votre ressource Application Insights. Ainsi, vous pouvez localiser la source des problèmes potentiels avant qu’ils se produisent et analyser la cause racine avec un affichage des transactions de bout en bout.

## <a name="supported-environments-languages-and-resource-providers"></a>Environnements, langages et fournisseurs de ressources pris en charge

À mesure que nous ajoutons des intégrations supplémentaires, la matrice des capacités de l’instrumentation automatique gagne en complexité. Le tableau ci-dessous montre l’état actuel de la question de la prise en charge de divers fournisseurs de ressources, langages et environnements.

|Environnement/fournisseur de ressources          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service sur Windows           | Disponibilité générale, APD*       | Disponibilité générale, consentement      | Version préliminaire publique  | Version préliminaire publique  | Non pris en charge   |
|Azure App Service sur Linux             | N/A             | Non pris en charge   | Version préliminaire publique  | Version préliminaire publique  | Non pris en charge   |
|Azure Functions - De base                | Disponibilité générale, APD*       | Disponibilité générale, APD*       | Disponibilité générale, APD*       | Disponibilité générale, APD*       | Disponibilité générale, APD*       |
|Azure Functions Windows – Dépendances | Non pris en charge   | Non pris en charge   | Version préliminaire publique  | Non pris en charge   | Non prise en charge   |
|Azure Spring Cloud                     | Non prise en charge   | Non pris en charge   | Version préliminaire publique  | Non pris en charge   | Non pris en charge   |
|Azure Kubernetes Service               | N/A             | Intégré       | Via l’agent   | Intégré       | Non pris en charge   |
|Machines virtuelles Azure - Windows                      | Version préliminaire publique  | Non pris en charge   | Via l’agent | Non pris en charge   | Non pris en charge   |
|Machines virtuelles locales - Windows                | Disponibilité générale, consentement      | Non pris en charge   | Via l’agent   | Non pris en charge   | Non pris en charge   |
|Agent autonome - Tous les env.            | Non pris en charge   | Non pris en charge   | GA              | Non pris en charge   | Non pris en charge   |

*APD signifie Activé par défaut : Application Insights est activé automatiquement une fois que vous avez déployé votre application dans les environnements pris en charge. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

La supervision des applications sur Azure App Service sous Windows est disponible pour les applications **[.NET](./azure-web-apps.md?tabs=net)** (activé par défaut), **[.NET Core](./azure-web-apps.md?tabs=netcore)** , **[Java](./azure-web-apps.md?tabs=java)** (en préversion publique) et **[Node.js](./azure-web-apps.md?tabs=nodejs)** . Pour superviser une application Python, ajoutez le [SDK](./opencensus-python.md) à votre code.

> [!NOTE]
> La supervision des applications est actuellement disponible pour les applications basées sur du code Windows sur App Service. La supervision des applications sur les conteneurs Windows sur App Service n’est pas encore prise en charge via l’intégration à Application Insights.

### <a name="linux"></a>Linux
Vous pouvez activer la supervision des applications **[Java](./azure-web-apps.md?tabs=java)** et **[Node.js](./azure-web-apps.md?tabs=nodejs)** s’exécutant sur Linux dans App Service par le biais du portail : l’expérience pour les deux langages est en préversion publique et disponible dans toutes les régions. 

Pour les autres langages, [.NET Core](./asp-net-core.md) et [Python](./opencensus-python.md), utilisez le SDK.

## <a name="azure-functions"></a>Azure Functions

La supervision de base pour Azure Functions est activée par défaut afin de collecter les données de journal, de performances et d’erreur ainsi que les requêtes HTTP. Pour les applications Java, vous pouvez activer une supervision plus riche avec le suivi distribué et obtenir les détails sur les transactions de bout en bout. Cette fonctionnalité pour Java est en préversion publique pour Windows et vous pouvez [l’activer dans le portail Azure](./monitor-functions.md).

## <a name="azure-spring-cloud"></a>Azure Spring Cloud

### <a name="java"></a>Java 
La supervision des applications pour les applications Java s’exécutant dans Azure Spring Cloud est intégrée au portail. Vous pouvez activer Application Insights directement à partir du portail Azure, à la fois pour les ressources Azure Spring Cloud existantes et nouvellement créées.  

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

L’instrumentation sans code dans Azure Kubernetes Service est actuellement disponible pour les applications Java via l’[agent autonome](./java-in-process-agent.md). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Machines virtuelles Windows Azure et groupe de machines virtuelles identiques

L’auto-instrumentation pour les machines virtuelles Azure et le groupe de machines virtuelles identiques est disponible pour [.NET](./azure-vm-vmss-apps.md) et [Java](./java-in-process-agent.md).  

## <a name="on-premises-servers"></a>Serveurs locaux
Vous pouvez facilement activer la supervision de vos [serveurs Windows locaux pour les applications .NET](./status-monitor-v2-overview.md) et pour les [applications Java](./java-in-process-agent.md).

## <a name="other-environments"></a>Autres environnements
L’agent autonome Java polyvalent fonctionne dans n’importe quel environnement. Il n’est pas nécessaire d’instrumenter votre code. [Suivez le guide](./java-in-process-agent.md) pour activer Application Insights et en savoir plus sur les fonctionnalités étonnantes de l’agent Java. L’agent est en préversion publique et disponible dans toutes les régions. 

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Application Insights](./app-insights-overview.md)
* [Plan de l’application](./app-map.md)
* [Analyse des performances de bout en bout](../app/tutorial-performance.md)
