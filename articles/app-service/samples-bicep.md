---
title: Exemples Bicep
description: Recherchez des exemples Bicep pour certains des scénarios App Service les plus répandus. Découvrez comment automatiser vos tâches de déploiement ou de gestion App Service.
author: seligj95
tags: azure-service-management
ms.topic: sample
ms.date: 8/26/2021
ms.author: jordanselig
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: c6423d3f77e85918f02bc64224dba86488b7323d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224274"
---
# <a name="bicep-files-for-app-service"></a>Fichiers Bicep pour App Service

Le tableau suivant inclut des clients vers des fichiers Bicep pour Azure App Service. Pour suivre des guides de démarrage rapide et obtenir d’autres informations sur Bicep, consultez la [documentation de Bicep](../azure-resource-manager/bicep/index.yml).

Pour découvrir la syntaxe et les propriétés de Bicep pour les ressources App Services, consultez [Types de ressources Microsoft.Web](/azure/templates/microsoft.web/allversions).

| Déploiement d’une application | Description |
|-|-|
| [Plan App Service et application Linux de base](https://github.com/Azure/bicep/tree/main/docs/examples/101/web-app-linux) | Déploie une application App Service configurée pour Linux. |
| [Plan App Service et application Windows de base](https://github.com/Azure/bicep/tree/main/docs/examples/101/web-app-windows) | Déploie une application App Service configurée pour Windows. |
| [Site web avec conteneur](https://github.com/Azure/bicep/tree/main/docs/examples/101/website-with-container) | Déploie un site web à partir d’une image Docker configurée pour Linux. |
| **Configuration d’une application** | **Description** |
| [Application avec journalisation conditionnelle](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-conditional-log)| Déploie une application App Service avec des conditions pour l’activation de la journalisation. |
| [Application avec module Log Analytics](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-loganalytics-mod)| Déploie une application App Service avec Log Analytics. |
| [Application avec intégration au réseau virtuel régional](https://github.com/Azure/bicep/tree/main/docs/examples/101/app-service-regional-vnet-integration)| Déploie une application App Service avec l’intégration au réseau virtuel régional activée. |
|**Application avec des ressources connectées**| **Description** |
| [Application avec CosmosDB](https://github.com/Azure/bicep/tree/main/docs/examples/101/cosmosdb-webapp)| Déploie une application App Service sur Linux avec CosmosDB. |
| [Application avec MySQL](https://github.com/Azure/bicep/tree/main/docs/examples/101/webapp-managed-mysql)| Déploie une application App Service sur Windows avec Azure Database pour MySQL. |
| [Application avec une base de données dans Azure SQL Database](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-sql-database)| Déploie une application App Service et une base de données dans Azure SQL Database au niveau de service De base. |
| [Application connectée à une application web principale](https://github.com/Azure/bicep/tree/main/docs/examples/101/webapp-privateendpoint-vnet-injection)| Déploie deux applications web (front-end et back-end) connectées en toute sécurité avec l’injection de réseaux virtuels et le point de terminaison privé. |
| [Application avec une base de données, une identité managée et une supervision](https://github.com/Azure/bicep/tree/main/docs/examples/301/web-app-managed-identity-sql-db)| Déploie une application App Service avec une base de données, une identité managée et une supervision. |
|**Environnement App Service**| **Description** |
| [Créer un environnement App Service Environment v2](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-asev2-create) | Crée un environnement App Service Environment v2 dans votre réseau virtuel. |
| | |