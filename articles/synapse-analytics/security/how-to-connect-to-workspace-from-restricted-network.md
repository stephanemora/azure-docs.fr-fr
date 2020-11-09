---
title: Se connecter à la ressource de l’espace de travail Synapse Studio à partir d’un réseau restreint
description: Cet article vous apprendra à vous connecter aux ressources de votre espace de travail Azure Synapse Studio à partir d'un réseau restreint
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 5d28b8f2ff3045c9fdf5e8a866419a22bfbc6504
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328598"
---
# <a name="connect-to-synapse-studio-workspace-resources-from-a-restricted-network"></a>Se connecter aux ressources de l’espace de travail Synapse Studio à partir d’un réseau restreint

Cet article s’adresse à l’administrateur informatique de l’entreprise qui gère le réseau restreint de l’entreprise. L’administrateur informatique va activer la connexion réseau entre Azure Synapse Studio et la station de travail au sein de ce réseau restreint.

Dans cet article, vous allez apprendre à vous connecter à votre espace de travail Azure Synapse à partir d’un environnement réseau restreint. 

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Espace de travail Azure Synapse**  : Si vous ne disposez pas de Synapse Studio, créez un espace de travail Synapse à partir d’Azure Synapse Analytics. Le nom de l’espace de travail sera requis à l’étape 4 suivante.
* **Réseau restreint**  : Le réseau restreint est géré par l’administrateur informatique de la société. L’administrateur informatique a l’autorisation de configurer la stratégie réseau. Le nom du réseau virtuel et son sous-réseau seront nécessaires à l’étape 3 ci-dessous.



## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>Étape 1 : Ajouter des règles de sécurité réseau sortantes au réseau restreint

Vous devez ajouter quatre règles de sécurité de trafic sortant du réseau avec quatre balises de service. En savoir plus dans la [Vue d’ensemble des étiquettes de service](/azure/virtual-network/service-tags-overview.md) 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor (Facultatif. Ajoutez ce type de règle uniquement lorsque vous souhaitez partager les données avec Microsoft.)

Détails sur les règles de trafic sortant **Azure Resource Manager** comme indiqué ci-dessous. Lorsque vous créez les trois autres règles, remplacez la valeur de «  **Destination service tag**  » par le nom de la balise de service «  **AzureFrontDoor.Frontend**  », «  **AzureActiveDirectory**  », «  **AzureMonitor**  » dans la liste de sélection déroulante.

![AzureResourceManager](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)


## <a name="step-2-create-azure-synapse-analytics-private-link-hubs"></a>Étape 2 : Créer une analyse Azure Synapse Analytics (hubs de liaison privée)

Vous devez créer une analyse Azure Synapse Analytics (hubs de liaison privée) à partir du Portail Azure. Recherchez «  **Azure Synapse Analytics (hubs de liaison privée)**  » via le Portail Azure, puis remplissez le champ nécessaire et créez l’analyse. 

> [!Note]
> La région doit être la même que celle où se trouve votre espace de travail Synapse.

![Création de hubs de liaison privée Synapse Analytics](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-private-link-endpoint-for-synapse-studio-gateway"></a>Étape 3 : Créer un point de terminaison de lien privé pour la passerelle Synapse Studio

Pour accéder à la passerelle Synapse Studio, vous devez créer un point de terminaison de liaison privée à partir du Portail Azure. Recherchez «  **Private Link**  » dans le Portail Azure. Sélectionnez «  **Create private endpoint**  » (Créer un point de terminaison privé) dans le «  **Centre Private Link**  », puis remplissez le champ nécessaire et créez-le. 

> [!Note]
> La région doit être la même que celle où se trouve votre espace de travail Synapse.

![Création d’un point de terminaison privé pour Synapse Studio 1](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

Dans l’onglet suivant de «  **Resource**  » (« Ressource »), choisissez le hub de liaison privée, créé à l’étape 2 ci-dessus.

![Création d’un point de terminaison privé pour Synapse Studio 2](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

Dans l’onglet suivant de «  **Configuration**  », 
* Choisissez le nom de réseau virtuel restreint défini pour «  **Virtual network**  » (« Réseau virtuel »).
* Choisissez le sous-réseau du réseau virtuel restreint pour l’option «  **Subnet**  » (« Sous-réseau »). 
* Sélectionnez «  **Oui**  » pour «  **Intégrer à une zone DNS privée**  ».

![Création d’un point de terminaison privé pour Synapse Studio 3](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

Une fois le point d'extrémité du lien privé créé, vous pouvez accéder à la page de connexion de l'outil web Synapse Studio. Cependant, vous ne pouvez pas accéder aux ressources de votre espace de travail Synapse tant que vous n’avez pas terminé l’étape suivante.

## <a name="step-4-create-private-link-endpoints-for-synapse-studio-workspace-resource"></a>Étape 4 : Créer des points de terminaison de lien privé pour la ressource de l’espace de travail Synapse Studio

Pour accéder aux ressources de votre espace de travail Synapse Studio, vous devez créer au moins un point d’extrémité de liaison privée de type «  **Dev**  » ou «  **Target sub-resource**  » et deux autres points d’extrémité de liaison privée facultatifs de type «  **Sql**  » ou «  **SqlOnDemand**  » selon les ressources de l’espace de travail Synapse Studio auxquelles vous souhaitez accéder. Cette création de point de terminaison de lien privé pour l’espace de travail Synapse Studio est similaire à la création de point de terminaison ci-dessus.  

Prêtez attention aux zones ci-dessous sous l’onglet «  **Ressource** » :
* sélectionnez «  **Microsoft. Synapse/Workspaces**  » sur «  **Type de ressource**  ».
* Sélectionnez «  **YourWorkSpaceName**  » sur «  **Ressource**  » que vous avez créé précédemment.
* Sélectionnez le type de point de terminaison dans «  **Target sub-resource**  » :
  * **Sql**  : est destiné à l’exécution de la requête SQL dans le pool SQL.
  * **SqlOnDemand** :  est destiné à l’exécution de requêtes SQL intégrées.
  * **Dev**  : est destiné à l’accès aux autres ressources dans les espaces de travail de Synapse Studio. Vous devez créer au moins le point de terminaison de la liaison privée de ce type.

![Création d’un point de terminaison privé pour l’espace de travail Synapse Studio](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)

Tout est défini à présent. Vous pouvez accéder à votre ressource de l’espace de travail Synapse Studio.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [Réseau virtuel d’espace de travail managé](./synapse-workspace-managed-vnet.md)

En savoir plus sur les [Points de terminaison privés managés](./synapse-workspace-managed-private-endpoints.md)
