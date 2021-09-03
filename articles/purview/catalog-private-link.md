---
title: Utiliser des points de terminaison privés pour un accès sécurisé à Purview
description: Cet article décrit une vue d’ensemble générale de la façon dont vous pouvez utiliser un point de terminaison privé pour votre compte Purview
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: 37600a101c44f556eed3678910254f9832f77366
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122535392"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>Utiliser des points de terminaison privés pour votre compte Azure Purview

Cet article décrit comment configurer des points de terminaison privés pour Azure Purview.

## <a name="conceptual-overview"></a>Vue d'ensemble conceptuelle
Vous pouvez utiliser des [points de terminaison privés Azure](../private-link/private-endpoint-overview.md) pour vos comptes Azure Purview afin de permettre aux utilisateurs d’un réseau virtuel (VNet) d’accéder en toute sécurité au catalogue via une liaison privée. Un point de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour votre compte Purview. Le trafic réseau entre les clients sur le réseau virtuel et le compte Purview traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft. 

Vous pouvez déployer un point de terminaison privé de _compte_ Azure Purview pour autoriser uniquement les appels client à Azure Purview qui proviennent du réseau privé.

Pour vous connecter à Azure Purview Studio à l’aide d’une connectivité de réseau privé, vous pouvez déployer un point de terminaison privé de _portail_.

Vous pouvez déployer des points de terminaison privés _d’ingestion_ si vous avez besoin d’analyser des sources de données IaaS et PaaS Azure dans des réseaux virtuels Azure et des sources de données locales via une connexion privée. Cette méthode garantit l’isolement réseau pour vos métadonnées circulant des sources de données vers Azure Purview Data Map.

:::image type="content" source="media/catalog-private-link/purview-private-link-overview.png" alt-text="Capture d’écran montrant Azure Purview avec des points de terminaison privés."::: 

## <a name="prerequisites"></a>Configuration requise

Avant de déployer des points de terminaison privés pour le compte Azure Purview, vérifiez que vous respectez les prérequis suivants :

1. Compte Azure avec un abonnement actif. [Créez un compte gratuitement.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
<br>
2. Réseau virtuel Azure existant. Déployez un nouveau [réseau virtuel Azure](../virtual-network/quick-create-portal.md) si vous n’en avez pas.
<br>

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Scénarios de déploiement d’un point de terminaison privé Azure Purview

Utilisez la liste de vérification recommandée suivante pour effectuer le déploiement d’un compte Azure Purview avec des points de terminaison privés :


|Scénario  |Objectifs  |
|---------|---------|
|**Scénario 1** - [Se connecter de manière privée et sécurisée à votre compte Purview](./catalog-private-link-account-portal.md)   | Vous devez activer l’accès à votre compte Azure Purview, notamment l’accès à _Azure Purview Studio_ et à l’API Atlas via des points de terminaison privés. (Déployez des points de terminaison privés de _compte_ et de _portail_).   |
|**Scénario 2** - [Analyser vos sources de données de manière privée et sécurisée](./catalog-private-link-ingestion.md)  | Vous devez analyser les sources de données localement et sur Azure derrière un réseau virtuel à l’aide du runtime d’intégration auto-hébergé. (Déployez des points de terminaison privés _d’ingestion_.)    |
|**Scénario 3** - [Se connecter à Azure Purview et analyser des sources de données de manière privée et sécurisée](./catalog-private-link-end-to-end.md) |Vous devez restreindre l’accès à votre compte Azure Purview uniquement à l’aide d’un point de terminaison privé, notamment à Azure Purview Studio, aux API Atlas et analyser les sources de données localement et sur Azure derrière un réseau virtuel à l’aide du runtime d’intégration auto-hébergé, garantissant ainsi l’isolement réseau de bout en bout. (Déployez des points de terminaison privés de _compte_, de _portail_ et _d’ingestion_.)   |

## <a name="support-matrix-for-scanning-data-sources-through-_ingestion_-private-endpoint"></a>Matrice de prise en charge pour l’analyse des sources de données via un point de terminaison privé _d’ingestion_

Pour les scénarios où le point de terminaison privé _d’ingestion_ est utilisé dans votre compte Azure Purview, et que l’accès public sur vos sources de données est désactivé, Azure Purview peut analyser les sources de données suivantes qui se trouvent derrière un point de terminaison privé :

|Source de données derrière un point de terminaison privé  |Type de runtime d’intégration  |Type d'informations d'identification  |
|---------|---------|---------|
|Stockage Blob Azure | Runtime d’intégration auto-hébergé | Principal de service|
|Stockage Blob Azure | Runtime d’intégration auto-hébergé | Clé du compte|
|Azure Data Lake Storage Gen 2 | Runtime d’intégration auto-hébergé| Principal de service|
|Azure Data Lake Storage Gen 2 | Runtime d’intégration auto-hébergé| Clé du compte|
|Azure SQL Database | Runtime d’intégration auto-hébergé| Authentification SQL|
|Azure SQL Database | Runtime d’intégration auto-hébergé| Principal de service|
|Azure SQL Managed Instance | Runtime d’intégration auto-hébergé| Authentification SQL|
|Azure Cosmos DB| Runtime d’intégration auto-hébergé| Clé du compte|
|SQL Server | Runtime d’intégration auto-hébergé| Authentification SQL|

## <a name="frequently-asked-questions"></a>Forum Aux Questions  

Pour consulter les questions fréquentes relatives aux déploiements de points de terminaison privés dans Azure Purview, consultez le [Forum aux questions sur les points de terminaison privés Azure Purview](./catalog-private-link-faqs.md).
 
## <a name="troubleshooting-guide"></a>Guide de résolution des problèmes 
Pour résoudre les problèmes de configuration de point de terminaison privé pour les comptes Purview, consultez [Résolution des problèmes de configuration de point de terminaison privé pour les comptes Purview](./catalog-private-link-troubleshoot.md).

## <a name="known-limitations"></a>Limitations connues
Pour afficher la liste des limitations actuelles liées aux points de terminaison privés Azure Purview, consultez [Limitations connues des points de terminaison privés Azure Purview](./catalog-private-link-troubleshoot.md#known-limitations).

## <a name="next-steps"></a>Étapes suivantes

- [Déployer des points de terminaison privés d’ingestion](./catalog-private-link-ingestion.md)