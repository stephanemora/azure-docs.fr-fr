---
title: Limites des ressources Azure SQL Database - instance managée | Microsoft Docs
description: Cet article fournit une vue d’ensemble des limites de ressources Azure SQL Database pour les instances managées.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 06/26/2019
ms.openlocfilehash: e5dc449dc51faccdd8c0e69337cc5f8ac19fa296
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874402"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Vue d’ensemble des limites de ressources Azure SQL Database Managed Instance

Cet article donne un aperçu des limites de ressources pour l’instance gérée par Azure SQL Database et fournit des informations sur la façon de demander une augmentation de ces limites.

> [!NOTE]
> Pour connaître les différences entre les fonctionnalités prises en charge et les instructions T-SQL, consultez [Différences de fonctionnalités](sql-database-features.md) et [Prise en charge des instructions T-SQL](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>Limites de ressources au niveau de l’instance

L’instance gérée a des caractéristiques et des limites de ressources qui dépendent de l’infrastructure et de l’architecture sous-jacentes. Les limites dépendent de la génération du matériel et du niveau de service.

### <a name="hardware-generation-characteristics"></a>Caractéristiques de la génération du matériel

L’instance gérée d’Azure SQL Database peut être déployée sur deux générations du matériel : Gen4 et Gen5. Les générations du matériel ont différentes caractéristiques, qui sont décrites dans le tableau suivant :

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Matériel | Processeurs Intel E5-2673 v3 (Haswell) 2,4 GHz, disque SSD attaché, vCore = 1 PP (cœur physique) | Processeurs Intel E5-2673 v4 (Broadwell) 2,3 GHz, disque SSD fast NVMe, vCore = 1 LP (hyperthread) |
| Nombre de vCores | 8, 16, 24 vCores | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| Mémoire maximale (ratio mémoire/cœur) | 7 Go par vCore<br/>Ajoutez plus de vCores pour obtenir davantage de mémoire. | 5,1 Go par vCore<br/>Ajoutez plus de vCores pour obtenir davantage de mémoire. |
| Mémoire OLTP maximum en mémoire | Limite de l’instance : 3 Go par vCore<br/>Limites de base de données :<br/> - 8 cœurs : 8 Go par base de données<br/> - 16 cœurs : 20 Go par base de données<br/> - 24 cœurs : 36 Go par base de données | Limite de l’instance : 2,5 Go par vCore<br/>Limites de base de données :<br/> - 8 cœurs : 13 Go par base de données<br/> - 16 cœurs : 32 Go par base de données |
| Stockage réservé d’instance max. |  Usage général : 8 To<br/>Critique pour l’entreprise : 1 To | Usage général : 8 To<br/> Critique pour l’entreprise 1 To, 2 To ou 4 To, en fonction du nombre de cœurs |

> [!IMPORTANT]
> Les nouvelles bases de données Gen4 ne sont plus prises en charge dans les régions Australie Est et Brésil Sud.

### <a name="service-tier-characteristics"></a>Caractéristiques du niveau de service

L’instance gérée a deux niveaux de service : Usage général/Vital pour l’entreprise. Ces niveaux offre des fonctionnalités différentes, comme décrit dans le tableau ci-dessous :

| **Fonctionnalité** | **Usage général** | **Critique pour l’entreprise** |
| --- | --- | --- |
| Nombre de vCores\* | Gen4 : 8, 16, 24<br/>Gen5 : 4, 8, 16, 24, 32, 40, 64, 80 | Gen4 : 8, 16, 24, 32 <br/> Gen5 : 4, 8, 16, 24, 32, 40, 64, 80 |
| Mémoire maximale | Gen4 : 56-168 Go (7 Go/vCore)<br/>Gen5 : 40,8-408 Go (5,1 Go/vCore)<br/>Ajoutez plus de vCores pour obtenir davantage de mémoire. | Gen4 : 56-168 Go (7 Go/vCore)<br/>Gen5 : 40,8-408 Go (5,1 Go/vCore)<br/>Ajoutez plus de vCores pour obtenir davantage de mémoire. |
| Taille de stockage réservé maximale d’instance | - 2 To pour les 4 vCores (Gen5 uniquement)<br/>- 8 To pour les autres tailles | Gen4 : 1 To <br/> Gen5 : <br/>- 1 To pour 4, 8, 16 vCores<br/>- 2 To pour 24 vCores<br/>- 4 To pour 32, 40, 64, 80 vCores |
| Taille de base de données maximale | Déterminé par la taille de stockage maximale par instance | Déterminé par la taille de stockage maximale par instance |
| Nombre maximal de bases de données par instance | 100 | 100 |
| Nombre maximal de fichiers de base de données par instance | Jusqu’à 280 | 32 767 fichiers par base de données |
| Taille maximale des fichiers | 8 To | 4 To |
| IOPS de données/journal (approximatives) | 500 - 7 500 par fichier<br/>\*[Augmentez la taille de fichier pour obtenir davantage d’IOPS](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11 000 - 110 000 (1375/vCore)<br/>Ajoutez plus de vCores pour obtenir de meilleures performances d’E/S. |
| Limite de débit d’écriture du journal | 3 Mo/s par vCore<br/>22 Mo/s max par instance | 4 Mo/s par vCore<br/>48 Mo/s par instance|
| Débit de données (approximatif) | 100 - 250 Mo/s par fichier<br/>\*[Augmentez la taille de fichier pour obtenir de meilleures performances d’E/S](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | N/A |
| Latence d’E/S de stockage (approximative) | 5 - 10 ms | 1 - 2 ms |
| Taille maximale de tempDB | 192 - 1 920 Go (24 Go par vCore)<br/>Ajoutez plus de vCores pour obtenir davantage d’espace TempDB. | Limité par la taille de stockage maximale d’instance. La taille du fichier journal TempDB est actuellement limitée à 24 Go/vCore. |
| OLTP en mémoire | Non pris en charge | Disponible |
| Nombre maximal de sessions | 30000 | 30000 |

> [!NOTE]
> - Les tailles des données et des fichiers journaux dans les bases de données utilisateur et système sont comprises dans la taille de stockage d’instance qui est comparée à la limite de taille de stockage maximale. Utilisez la vue système <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> pour déterminer l’espace total utilisé par les bases de données. Les journaux d’activité d’erreurs ne sont ni conservés ni compris dans la taille. Les sauvegardes ne sont pas comprises dans la taille de stockage.
> - Le débit et les IOPS dépendent également de la taille de page qui n’est pas explicitement limitée par instance gérée.

## <a name="supported-regions"></a>Régions prises en charge

Les instances gérées peuvent être créée uniquement dans des [régions prises en charge](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Si vous voulez créer une instance gérée dans une région qui n’est actuellement pas prise en charge, vous pouvez [envoyer une demande de support par le biais du Portail Microsoft Azure](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Types d’abonnements pris en charge

L’instance gérée prend actuellement en charge le déploiement uniquement sur les types d’abonnements suivants :

- [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Fournisseur de services cloud (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Abonnements avec crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limitations des ressources régionales

Les types d’abonnements pris en charge peuvent contenir un nombre limité de ressources par région. L’instance gérée a deux limites par défaut par région Azure en fonction d’un type d’abonnement :

- **Limite de sous-réseaux** : nombre maximal de sous-réseaux dans lesquels des instances gérées sont déployées dans une seule et même région.
- **Limite de vCore**: Le nombre maximal de vCores qui peut être déployé dans toutes les instances dans une seule région.

> [!Note]
> Ces limites sont des paramètres par défaut : il ne s’agit pas de limitations techniques. Ces limites peuvent être augmentées en créant une [demande de support spéciale dans le Portail Microsoft Azure](#obtaining-a-larger-quota-for-sql-managed-instance) si vous avez besoin de plus d’instances gérées dans la région actuelle. Vous pouvez aussi créer des instances gérées dans une autre région Azure sans envoyer de demandes de support.

Le tableau suivant présente les limites régionales par défaut pour les abonnements pris en charge :

|Type d’abonnement| Nombre maximal de sous-réseaux d’instances gérées | Nombre maximal d’unités de vCore* |
| :---| :--- | :--- |
|Paiement à l’utilisation|3|320|
|CSP |8 (15 dans certaines régions**)|960 (1 440 dans certaines régions**)|
|Pay-As-You-Go Dev/Test|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 dans certaines régions**)|960 (1 440 dans certaines régions**)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional et plateformes MSDN|2|32|

\* Lorsque vous planifiez vos déploiements, considérez qu’un vCore Vital pour l’entreprise (en raison de la redondance renforcée) consomme généralement 4 fois plus de capacité qu’un vCore Usage général. Ainsi, pour vos calculs, 1 GP vCore = 1 unité vCore et 1 BC vCore = 4 unités vCore. Pour simplifier votre analyse de la consommation par rapport aux limites par défaut, récapitulez les unités vCore de tous les sous-réseaux de la région où les instances gérées sont déployées et comparez les résultats avec les limites d’unités d’instance pour votre type d’abonnement. La limite **Nombre maximal d’unités de vCore** s’applique à chaque abonnement dans une région. Il n’y a pas de limite par sous-réseau individuel sauf que la somme de tous les vCores déployés sur plusieurs sous-réseaux doit être inférieure ou égale à **nombre maximum d’unités vCore**.

\*\* Des limites de sous-réseau et de vCore plus importantes s’appliquent dans les régions suivantes : Australie Est, USA Est, USA Est 2, Europe Nord, USA Centre Sud, Asie Sud-Est, Royaume-Uni Sud, Europe Ouest, USA Ouest 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Obtention d’un quota plus élevé pour votre instance managée SQL

Si vous avez besoin de davantage d’instances gérées dans vos régions actuelles, vous pouvez envoyer une demande de support pour étendre le quota à l’aide du Portail Microsoft Azure.
Pour lancer le processus d’obtention d’un quota plus élevé :

1. Ouvrez **Aide + Support**, puis cliquez sur **Nouvelle demande de support**.

   ![Aide et support](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Sous l’onglet De base de la nouvelle demande de support :
   - Pour **Type de problème**, sélectionnez **Limites du service et des abonnements (quotas)** .
   - Pour **Abonnement**, sélectionnez votre abonnement.
   - Pour **Type de quota**, sélectionnez **SQL Database Managed Instance**.
   - Pour **Plan de support**, sélectionnez votre plan de support.

     ![Type de problème : Quota](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Cliquez sur **Suivant**.
4. Sous l’onglet **Problème** de la nouvelle demande de support :
   - Pour **Gravité**, sélectionnez le niveau de gravité du problème.
   - Pour **Détails**, fournissez des informations supplémentaires concernant votre problème, notamment les messages d’erreur.
   - Pour **Chargement de fichier**, joignez un fichier contenant plus d’informations (jusqu’à 4 Mo).

     ![Détails du problème](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Une demande valide doit inclure les éléments suivants :
     > - Région dans laquelle la limite d’abonnement doit être augmentée.
     > - Nombre nécessaire de vCores, par niveau de service dans les sous-réseaux existants après l’augmentation du quota (si l’un des sous-réseaux existants doit être développé).
     > - Nombre nécessaire de nouveaux sous-réseaux et nombre total de vCores par niveau de service dans les nouveaux sous-réseaux (si vous devez déployer des instances gérées dans les nouveaux sous-réseaux).

5. Cliquez sur **Suivant**.
6. Sous l’onglet Informations de contact pour la nouvelle demande de support, entrez la méthode de communication préférée (e-mail ou téléphone) et les détails du contact.
7. Cliquez sur **Créer**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’instance managée, consultez [Présentation de Managed Instance](sql-database-managed-instance.md).
- Pour des informations sur les prix, consultez [Tarifs des instances managées SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Pour savoir comment créer votre première instance managée, consultez le [Guide de démarrage rapide](sql-database-managed-instance-get-started.md).
