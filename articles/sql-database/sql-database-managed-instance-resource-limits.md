---
title: Limites de ressources Azure SQL Database - instance gérée | Microsoft Docs
description: Cet article fournit une vue d’ensemble des limites de ressources Azure SQL Database pour les instances gérées.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: 7af15e2e2ca6698f9d8ba1629f13804ce6457b8d
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315636"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Vue d’ensemble des limites de ressources Azure SQL Database Managed Instance

Cet article offre une vue d’ensemble des limites de ressources Azure SQL Database Managed Instance et fournit des informations sur la façon de créer une requête pour augmenter les limites d’abonnement régional par défaut.

> [!NOTE]
> Pour connaître les autres limitations Managed Instance, consultez [modèle d’achat basé sur le nombre de vCores](sql-database-managed-instance.md#vcore-based-purchasing-model) et [Niveaux de service de Managed Instance](sql-database-managed-instance.md#managed-instance-service-tiers). Pour connaître les différences entre les fonctionnalités prises en charge et les instructions T-SQL, consultez [Différences de fonctionnalités](sql-database-features.md) et [Prise en charge des instructions T-SQL](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>Limites de ressources au niveau de l’instance

Managed Instance a des caractéristiques et des limites de ressources qui dépendent de l’infrastructure et de l’architecture sous-jacentes. Les limites dépendent de la génération du matériel et du niveau de service.

### <a name="hardware-generation-characteristics"></a>Caractéristiques de la génération du matériel

Azure SQL Database Managed Instance peut être déployé sur deux générations du matériel (Gen4 et Gen5). Les générations du matériel ont des caractéristiques spécifiques qui sont décrites dans le tableau suivant :

|   | **Gen 4** | **Gen 5** |
| --- | --- | --- |
| Matériel | Processeurs Intel E5-2673 v3 (Haswell) 2,4 GHz, disque SSD attaché, vCore = 1 PP (cœur physique) | Processeurs Intel E5-2673 v4 (Broadwell) 2,3 GHz, disque SSD fast eNVM, vCore = 1 LP (hyperthread) |
| Calcul | 8, 16, 24 vCores | 8, 16, 24, 32, 40, 64, 80 vCores |
| Mémoire | 7 Go par vCore | 5,1 Go par vCore |
| Espace de stockage maximal (Critique pour l’entreprise) | 1 To | 1 To, 2 To ou 4 To, en fonction du nombre de cœurs |

### <a name="service-tier-characteristics"></a>Caractéristiques du niveau de service

Managed Instance propose deux niveaux de service : Usage général et Critique pour l’entreprise. Ces niveaux offre des fonctionnalités différentes, comme décrit dans le tableau ci-dessous :

| **Fonctionnalité** | **Usage général** | **Critique pour l’entreprise** |
| --- | --- | --- |
| Nombre de vCores\* | Gen4 : 8, 16, 24<br/>Gen5 : 8, 16, 24, 32, 40, 64, 80 | Gen4 : 8, 16, 24, 32 <br/> Gen5 : 8, 16, 24, 32, 40, 64, 80 |
| Mémoire | Gen4 : 56 Go - 156 Go<br/>Gen5 : 44 Go - 440 Go<br/>\*Proportionnel au nombre de vCores | Gen4 : 56 Go - 156 Go <br/> Gen5 : 44 Go - 440 Go<br/>\*Proportionnel au nombre de vCores |
| Taille de stockage maximale | 8 To | Gen 4 : 1 To <br/> Gen 5 : <br/>- 1 To pour 8, 16 vCores<br/>- 2 To pour 24 vCores<br/>- 4 To pour 32, 40, 64, 80 vCores |
| Espace de stockage maximal par base de données | Déterminé par la taille de stockage maximale par instance | Déterminé par la taille de stockage maximale par instance |
| Nombre maximal de bases de données par instance | 100 | 100 |
| Nombre maximal de fichiers de base de données par instance | Jusqu’à 280 | 32 767 fichiers par base de données |
| Nombre (approximatif) d’E/S par seconde | 500 - 7 500 par fichier<br/>\*[Dépend de la taille du fichier](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | 11 K - 110 K (1 375 par vCore) |
| Latence (approximative) des E/S | 5 - 10 ms | 1 - 2 ms |
| Taille maximale de tempDB | 192 - 1 920 Go (24 Go par vCore) | Déterminé par la taille de stockage maximale par instance |

- Les bases de données utilisateur et système sont comprises dans la taille de stockage d’instance qui est comparée à la limite de taille de stockage maximale. Utilisez la vue système <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> pour déterminer l’espace total utilisé par les bases de données. Les journaux d’erreurs ne sont ni conservés ni compris dans la taille. Les sauvegardes ne sont pas comprises dans la taille de stockage.

## <a name="supported-regions"></a>Régions prises en charge

Une instance Managed Instance peut être créée uniquement dans des [régions prises en charge](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Si vous voulez créer une instance Managed Instance dans la région qui n’est actuellement pas prise en charge, vous pouvez [envoyer demande de support par le biais du portail Azure](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Types d’abonnements pris en charge

Managed Instance prend actuellement en charge le déploiement uniquement sur les types d’abonnements suivants :

- [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Fournisseur de services cloud (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)

> [!NOTE]
> Cette limitation est temporaire. De nouveaux types d’abonnements seront activés par la suite.

## <a name="regional-resource-limitations"></a>Limitations des ressources régionales

Les types d’abonnements pris en charge peuvent contenir un nombre limité de ressources par région. Managed Instance a deux limites par défaut par région Azure en fonction d’un type d’abonnement :

- **Limite de sous-réseaux** : nombre maximal de sous-réseaux dans lesquels des instances gérées sont déployées dans une seule et même région.
- **Limite du nombre d’instances** : nombre maximal d’instances pouvant être déployées dans une seule et même région.

Le tableau suivant présente les limites régionales par défaut pour les abonnements pris en charge :

|Type d’abonnement| Nombre maximal de sous-réseaux Managed Instance | Nombre maximal d’instances |Nombre maximal d’instances gérées Usage général*|Nombre maximal d’instances gérées Critique pour l’entreprise*|
| :---| :--- | :--- |:--- |:--- |
|Paiement à l’utilisation|1*|4*|4*|1*|
|CSP |1*|4*|4*|1*|
|Pay-As-You-Go Dev/Test|1*|4*|4*|1*|
|Enterprise Dev/Test|1*|4*|4*|1*|
|EA|3**|12**|12**|3**|

\* Vous pouvez déployer 1 instance Critique pour l’entreprise ou 4 instances Usage général dans un sous-réseau, afin que le nombre total d’« unités d’instance » dans le sous-réseau ne dépasse jamais 4.

** Le nombre maximal d’instances dans un même niveau de service s’applique s’il n’y a aucune instance dans un autre niveau de service. Dans le cas où vous envisagez de combiner des instances Usage général et Critique pour l’entreprise au sein du même sous-réseau, utilisez la section suivante comme référence pour les combinaisons autorisées. En règle générale, le nombre total de sous-réseaux ne peut pas dépasser 3, et le nombre total d’unités d’instance ne peut pas dépasser 12.

Ces limites peuvent être augmentées en créant une [demande de support spéciale dans le portail Azure](#obtaining-a-larger-quota-for-sql-managed-instance) si vous avez besoin de plus d’instances gérées dans la région actuelle. Vous pouvez aussi créer des instances gérées dans une autre région Azure sans envoyer de demandes de support.

> [!IMPORTANT]
> Quand vous planifiez vos déploiements, considérez qu’une instance Critique pour l’entreprise (en raison de la redondance renforcée) consomme généralement 4 fois plus de capacité qu’une instance Usage général. Ainsi, pour vos calculs, 1 instance Usage général = 1 unité d’instance et 1 instance Critique pour l’entreprise = 4 unités d’instance. Pour simplifier votre analyse de la consommation par rapport aux limites par défaut, récapitulez les unités d’instance de tous les sous-réseaux de la région où les instances gérées sont déployées et comparez les résultats avec les limites d’unités d’instance pour votre type d’abonnement.

## <a name="strategies-for-deploying-mixed-general-purpose-and-business-critical-instances"></a>Stratégies de déploiement d’une combinaison d’instances Usage général et d’instances Critique pour l’entreprise

Les abonnements [Contrat entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) peuvent avoir des combinaisons d’instances Usage général et Critique pour l’entreprise. Toutefois, il existe certaines contraintes quant au positionnement des instances dans les sous-réseaux.

> [!Note]
> Les types d’abonnement [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) et [Fournisseur de services cloud (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources) peuvent avoir soit une instance Critique pour l’entreprise, soit jusqu’à 4 instances Usage général.

Les exemples suivants couvrent des cas de déploiement avec les sous-réseaux non vides et une combinaison de niveaux de service Usage général et Critique pour l’entreprise.

|Nombre de sous-réseaux|Sous-réseau 1|Sous-réseau 2|Sous-réseau 3|
|:---|:---|:---|:---|
|1|1 Critique pour l’entreprise et jusqu’à 8 Usage général<br>2 Critique pour l’entreprise et jusqu’à 4 Usage général|N/A| N/A|
|2|0 Critique pour l’entreprise, jusqu’à 4 Usage général|1 Critique pour l’entreprise, jusqu’à 4 Usage général<br>2 Critique pour l’entreprise, 0 Usage général|N/A|
|2|1 Critique pour l’entreprise, 0 Usage général|0 Critique pour l’entreprise, jusqu’à 8 Usage général<br>1 Critique pour l’entreprise, jusqu’à 4 Usage général|N/A|
|2|2 Critique pour l’entreprise, 0 Usage général|0 Critique pour l’entreprise, jusqu’à 4 Usage général|N/A|
|3|1 Critique pour l’entreprise, 0 Usage général|1 Critique pour l’entreprise, 0 Usage général|0 Critique pour l’entreprise, jusqu’à 4 Usage général|
|3|1 Critique pour l’entreprise, 0 Usage général|0 Critique pour l’entreprise, jusqu’à 4 Usage général|0 Critique pour l’entreprise, jusqu’à 4 Usage général|

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Obtention d’un quota plus élevé pour SQL Managed Instance

Si vous avez besoin de davantage d’instances gérées dans vos régions actuelles, vous pouvez envoyer la demande de support pour étendre le quota à l’aide du portail Azure.
Pour lancer le processus d’obtention d’un quota plus élevé :

1. Ouvrez **Aide + Support**, puis cliquez sur **Nouvelle demande de support**.

   ![Aide et support](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Sous l’onglet De base de la nouvelle demande de support :
   - Pour **Type de problème**, sélectionnez **Limites du service et des abonnements (quotas)**.
   - Pour **Abonnement**, sélectionnez votre abonnement.
   - Pour **Type de quota**, sélectionnez **SQL Database Managed Instance**.
   - Pour **Plan de support**, sélectionnez votre plan de support.

     ![Type de problème : Quota](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Cliquez sur **Suivant**.
4. Sous l’onglet Problème de la nouvelle demande de support :
   - Pour **Gravité**, sélectionnez le niveau de gravité du problème.
   - Pour **Détails**, fournissez des informations supplémentaires concernant votre problème, notamment les messages d’erreur.
   - Pour **Chargement de fichier**, joignez un fichier contenant plus d’informations (jusqu’à 4 Mo).

     ![Détails du problème](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Une demande valide doit inclure les éléments suivants :
     > - Région dans laquelle la limite d’abonnement doit être augmentée
     > - Nombre nécessaire d’instances, par niveau de service dans les sous-réseaux existants après l’augmentation du quota (si l’un des sous-réseaux existants doit être développé)
     > - Nombre nécessaire de nouveaux sous-réseaux et nombre total d’instances par niveau de service dans les nouveaux sous-réseaux (si vous devez déployer des instances gérées dans les nouveaux sous-réseaux).

5. Cliquez sur **Suivant**.
6. Sous l’onglet Informations de contact pour la nouvelle demande de support, entrez la méthode de communication préférée (e-mail ou téléphone) et les détails du contact.
7. Cliquez sur **Créer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur Managed Instance, consultez [Présentation de Managed Instance](sql-database-managed-instance.md).
- Pour plus d’informations sur la tarification, voir [Tarification de SQL Database Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Pour savoir comment créer votre première Managed Instance, consultez [Guide de démarrage rapide](sql-database-managed-instance-get-started.md).
