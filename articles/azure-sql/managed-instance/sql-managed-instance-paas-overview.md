---
title: Qu'est-ce qu'Azure SQL Managed Instance ?
description: Azure SQL Managed Instance est presque 100 % compatible avec le dernier moteur de base de données SQL Server (Édition Entreprise)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 06/25/2020
ms.openlocfilehash: 43fad6249d5c6f528353a819e03dd7401440e05d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391007"
---
# <a name="what-is-azure-sql-managed-instance"></a>Qu'est-ce qu'Azure SQL Managed Instance ?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Partie intégrante de la famille de produits Azure SQL, Azure SQL Managed Instance est le service de base de données cloud intelligent et évolutif qui combine la plus grande compatibilité SQL Server du moteur de base de données et tous les avantages d’une plateforme en tant que service entièrement gérée et persistante. SQL Managed Instance est presque 100 % compatible avec le moteur de base de données SQL Server (Édition Entreprise) le plus récent et fournit une implémentation de [réseau virtuel (VNet)](../../virtual-network/virtual-networks-overview.md) native traitant les problèmes de sécurité courants ainsi qu’un [modèle d’entreprise](https://azure.microsoft.com/pricing/details/sql-database/) favorable aux clients SQL Server existants. SQL Managed Instance permet aux clients SQL Server existants d’effectuer une migration « lift-and-shift » de leurs applications locales vers le cloud en apportant des modifications minimales aux applications et bases de données. En même temps, SQL Managed Instance conserve toutes les fonctionnalités PaaS (correctifs et mises à jour de versions automatiques, [sauvegardes automatisées](../database/automated-backups-overview.md), [haute disponibilité](../database/high-availability-sla.md)), ce qui réduit considérablement le temps de gestion et le coût total de possession.

> [!IMPORTANT]
> Pour obtenir la liste des régions où SQL Managed Instance est actuellement disponible, consultez [régions prises en charge](resource-limits.md#supported-regions).

Le diagramme suivant présente les principales fonctionnalités de SQL Managed Instance :

![Fonctionnalités clés](./media/sql-managed-instance-paas-overview/key-features.png)

Azure SQL Managed Instance est conçu pour les clients cherchant à effectuer une migration d’un grand nombre d’applications locales ou provenant d’environnements IaaS, générés automatiquement ou fournis par un éditeur de logiciels indépendant, afin de gérer complètement un environnement cloud PaaS avec le moins d’efforts de migration possible. Avec le service [Azure Data Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) entièrement automatisé, les clients peuvent effectuer une migration « lift-and-shift » de leurs instances SQL Server existantes vers une instance SQL Managed Instance compatible avec SQL Server et qui offre une isolation totale des instances des clients avec une prise en charge native des réseaux virtuels.  Avec Software Assurance, vous pouvez échanger vos licences existantes pour bénéficier de tarifs réduits sur SQL Managed Instance à l’aide d’[Azure Hybrid Benefit pour SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). SQL Managed Instance est la meilleure destination de migration dans le cloud pour les instances SQL Server qui nécessitent une haute sécurité et une surface de programmabilité riche.

## <a name="key-features-and-capabilities"></a>Fonctionnalités principales

SQL Managed Instance combine les meilleures fonctionnalités d’Azure SQL Database et du moteur de base de données SQL Server.

> [!IMPORTANT]
> SQL Managed Instance s’exécute avec toutes les fonctionnalités de la version la plus récente de SQL Server, notamment les opérations en ligne, les corrections de plan automatiques et d’autres améliorations des performances d’entreprise. Une comparaison des fonctionnalités disponibles est expliquée dans [Comparaison des fonctionnalités : Azure SQL Managed Instance et SQL Server](../database/features-comparison.md).

| **Avantages PaaS** | **Continuité de l’activité** |
| --- | --- |
|Aucun achat ni gestion de matériel <br>Aucun temps de gestion à dédier à l’infrastructure sous-jacente <br>Provisionnement et mise à l’échelle du service rapides <br>Application automatisée de correctifs et de mises à niveau de version <br>Intégration à d’autres services de données PaaS |Contrat SLA à 99,99 % de durée de fonctionnement  <br>Une [haute disponibilité](../database/high-availability-sla.md) intégrée <br>Données protégées par des [sauvegardes automatisées](../database/automated-backups-overview.md) <br>Période de rétention de sauvegarde configurable par le client <br>[Sauvegardes](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) lancées par l’utilisateur <br>Fonctionnalité de [limite de restauration dans le temps d’une base de données](../database/recovery-using-backups.md#point-in-time-restore) |
|**Sécurité et conformité** | **Gestion**|
|Environnement isolé ([intégration de réseau virtuel](connectivity-architecture-overview.md), service de locataire unique, calcul et stockage dédiés) <br>[Chiffrement transparent des données (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Authentification Azure Active Directory (Azure AD)](../database/authentication-aad-overview.md), prise en charge de l’authentification unique <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Principaux de serveur (connexions) Azure AD</a>  <br>Conformité aux mêmes normes qu’une base de données Azure SQL <br>[Audit SQL](auditing-configure.md) <br>[Protection avancée contre les menaces](threat-detection-configure.md) |API Azure Resource Manager pour automatiser le provisionnement et la mise à l’échelle des services <br>Fonctionnalités du portail Azure pour le provisionnement et la mise à l’échelle manuels des services <br>Service de migration des données

> [!IMPORTANT]
> Azure SQL Managed Instance a été certifié par rapport à plusieurs normes de conformité. Pour plus d’informations, consultez le document [Microsoft Azure Compliance Offerings](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers), qui fournit la liste la plus récente de certifications de conformité SQL Managed Instance, sous la rubrique **SQL Database**.

Les fonctionnalités clés de SQL Managed Instance figurent dans le tableau suivant :

|Fonctionnalité | Description|
|---|---|
| Version/Build de SQL Server | Moteur de base de données SQL Server (dernière version stable) |
| Sauvegardes automatisées gérées | Oui |
| Analyse et métriques des instances et bases de données intégrées | Oui |
| Mise à jour corrective automatique des logiciels | Oui |
| Les dernières fonctionnalités du moteur de base de données | Oui |
| Nombre de fichiers de données (ROWS) par base de données | Multiple |
| Nombre de fichiers journaux (LOG) par base de données | 1 |
| Réseau virtuel - Déploiement Azure Resource Manager | Oui |
| Réseau virtuel - Modèle de déploiement classique | Non |
| Prise en charge du portail | Oui|
| Integration Services (SSIS) intégré | Non : SSIS fait partie de la [plateforme PaaS Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Analysis Services (SSAS) intégré | Non : SSAS est une [plateforme PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) distincte |
| Reporting Services (SSRS) intégré | Non : utilisez les [Rapports paginés Power BI](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) à la place ou hébergez SSRS sur une machine virtuelle Azure. Même si SQL Managed Instance ne peut pas exécuter SSRS en tant que service, il peut héberger des [bases de données de catalogue SSRS](https://docs.microsoft.com/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements) pour un serveur de rapports installé sur une machine virtuelle Azure à l’aide de l’authentification SQL Server. |
|||

## <a name="vcore-based-purchasing-model"></a>Modèle d’achat vCore

Le [modèle d’achat vCore](../database/service-tiers-vcore.md) pour SQL Managed Instance vous assure flexibilité, contrôle et transparence. Il permet de traduire de manière simple les exigences des charges de travail locales dans le cloud. Ce modèle vous permet de changer la capacité de calcul, la mémoire et le stockage en fonction des besoins de vos charges de travail. Le modèle vCore permet également de réaliser jusqu'à 55 % d'économies avec [Azure Hybrid Benefit pour SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

Dans le modèle vCore, vous pouvez choisir entre différentes générations de matériel.

- Les processeurs logiques **Gen4** sont basés sur des processeurs Intel E5-2673 v3 (Haswell) de 2,4 GHz, un disque SSD attaché, des cœurs physiques, 7 Go de RAM par cœur, et des tailles de calcul comprises entre 8 et 24 vCores.
- Les processeurs logiques **Gen5** sont basés sur des processeurs Intel E5-2673 v4 (Broadwell) de 2,3 GHz et Intel SP-8160 (Skylake), un disque SSD NVMe rapide, un cœur logique multithread, et des tailles de calcul comprises entre 4 et 80 cœurs.

Vous trouverez des informations sur les différences entre les générations de matériel dans les [limites de ressources de SQL Managed Instance](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Niveaux de service

SQL Managed Instance est disponible en deux niveaux de service :

- **Usage général** : conçu pour des applications avec des exigences de performances et de latence d’E/S standard.
- **Critique pour l’entreprise** : conçu pour les applications avec des exigences de latence d’E/S faible et un impact minimal des opérations de maintenance sous-jacentes sur la charge de travail.

Les deux niveaux de service garantissent une disponibilité de 99,99 % et vous permettent de sélectionner la taille de stockage et la capacité de calcul indépendamment. Pour plus d’informations sur l’architecture à haute disponibilité d’Azure SQL Managed Instance, consultez [Haute disponibilité et Azure SQL Managed Instance](../database/high-availability-sla.md).

### <a name="general-purpose-service-tier"></a>Niveau de service Usage général

La liste suivante décrit les principales caractéristiques du niveau de service Usage général :

- Concevoir pour la majorité des applications métier avec des exigences de performances standard
- Stockage Blob Azure à hautes performances (8 To)
- [Haute disponibilité](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability) intégrée basée sur un Stockage Blob Azure fiable et [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)

Pour plus d’informations, consultez les articles [storage layer in general purpose tier](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) et [storage performance best practices and considerations for SQL Managed Instance (general purpose)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Vous trouverez plus d’informations sur les différences entre les niveaux de service dans les [limites de ressources de SQL Managed Instance](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Niveau de service Critique pour l’entreprise

Le niveau de service Critique pour l’entreprise est conçu pour les applications dont les exigences en termes d’E/S sont élevées. Il offre la meilleure résilience aux défaillances grâce à l’utilisation de plusieurs réplicas isolés.

La liste suivante décrit les principales caractéristiques du niveau de service Critique pour l’entreprise :

- Conçu pour les applications d’entreprise avec les exigences les plus hautes en matière de performances et de disponibilité
- Fourni avec un stockage SSD local extrêmement rapide (jusqu’à 1 To sur Gen4, jusqu’à 4 To sur Gen5)
- [Haute disponibilité](../database/high-availability-sla.md#premium-and-business-critical-service-tier-availability) intégrée basée sur les [groupes de disponibilité AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) et [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)
- Un [réplica de base de données en lecture seule](../database/read-scale-out.md) intégré supplémentaire qui peut être utilisé pour les rapports et d’autres charges de travail en lecture seule
- [OLTP en mémoire](../in-memory-oltp-overview.md), qui peut être utilisé pour les charges de travail avec des exigences de hautes performances  

Vous trouverez plus d’informations sur les différences entre les niveaux de service dans les [limites de ressources de SQL Managed Instance](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Opérations de gestion

Azure SQL Managed Instance fournit des opérations de gestion que vous pouvez utiliser pour déployer automatiquement de nouvelles instances managées, mettre à jour les propriétés des instances et supprimer des instances quand vous n’en avez plus besoin. Cette section fournit des informations sur les opérations de gestion et leurs durées habituelles.

Pour prendre en charge les [déploiements sur les réseaux virtuels Azure](../../virtual-network/virtual-network-for-azure-services.md) et assurer l’isolation et la sécurité des clients, SQL Managed Instance s’appuie sur des [clusters virtuels](connectivity-architecture-overview.md#high-level-connectivity-architecture), qui représentent un ensemble dédié de machines virtuelles isolées déployées sur le sous-réseau du réseau virtuel du client. Fondamentalement, chaque déploiement d’instance managée sur un sous-réseau vide génère une nouvelle structure de cluster virtuel.

Les opérations ultérieures sur les instances managées déployées peuvent également avoir des effets sur le cluster virtuel sous-jacent. Cela affecte la durée des opérations de gestion, car le déploiement de machines virtuelles supplémentaires entraîne une surcharge qui doit être prise en compte quand vous planifiez de nouveaux déploiements ou des mises à jour d’instances managées existantes.

Toutes les opérations de gestion peuvent être classées comme suit :

- Déploiement d’instance (création d’une nouvelle instance)
- Mise à jour d’instance (modification des propriétés d’instance, telles que vCores ou stockage réservé).
- Suppression d’instance

En règle générale, les opérations sur les clusters virtuels prennent le plus de temps. La durée des opérations sur les clusters virtuels varie. Vous trouverez ci-dessous les valeurs généralement observées, en fonction des données de télémétrie de service existantes :

- **Création de cluster virtuel** :  Il s’agit d’une étape synchrone dans les opérations de gestion d’instance. **90 % des opérations se terminent dans les quatre heures**.
- **Redimensionnement de cluster virtuel (expansion ou réduction)**  : L’expansion est une étape synchrone, tandis que la réduction est effectuée de façon asynchrone (sans incidence sur la durée des opérations de gestion des instances). **90 % des expansions de cluster nécessitent moins de deux heures trente**.
- **Suppression de cluster virtuel** : La suppression est une étape asynchrone, mais elle peut également être [lancée manuellement](virtual-cluster-delete.md) sur un cluster virtuel vide, auquel cas elle s’exécute de façon synchrone. **90 % des suppressions de cluster virtuel ne prennent pas plus d’une heure trente**.

En outre, la gestion des instances peut également inclure l’une de ces opérations sur les bases de données hébergées, ce qui rallonge les durées d’exécution :

- **Attachement de fichiers de base de données à partir du Stockage Azure** :  Il s’agit d’une étape synchrone, telle qu’un scale-up ou un scale-down de la capacité de calcul (vCore) ou du stockage dans le niveau de service Usage général. **90 % de ces opérations ne prennent pas plus de cinq minutes**.
- **Amorçage de groupe de disponibilité Always On** : Il s’agit d’une étape synchrone, telle qu’une mise à l’échelle de la capacité de calcul (vCore) ou du stockage dans le niveau de service Critique pour l’entreprise, ainsi que le basculement du niveau de service de Usage général à Critique pour l’entreprise (ou vice versa). La durée de cette opération est proportionnelle à la taille totale de la base de données, ainsi qu’à l’activité actuelle de la base de données (nombre de transactions actives). L’activité de la base de données lors de la mise à jour d’une instance peut faire varier considérablement la durée totale. **90 % de ces opérations s’exécutent à 220 Go/heure ou plus**.

Le tableau suivant récapitule les opérations et les durées totales habituelles :

|Category  |Opération  |Segment de longue durée  |Durée estimée  |
|---------|---------|---------|---------|
|**Déploiement** |Première instance sur un sous-réseau vide|Création de cluster virtuel|90 % des opérations se terminent dans les 4 heures.|
|Déploiement |Première instance d’une autre génération de matériel sur un sous-réseau non vide (par exemple, première instance de Génération 5 sur un sous-réseau avec des instances de Génération 4)|Création de cluster virtuel*|90 % des opérations se terminent dans les 4 heures.|
|Déploiement |Création de première instance de quatre vCores, sur un sous-réseau vide ou non vide|Création de cluster virtuel**|90 % des opérations se terminent dans les 4 heures.|
|Déploiement |Création d’instance suivante sur le sous-réseau non vide (2e instance, 3e instance, et ainsi de suite.)|Redimensionnement de cluster virtuel|90 % des opérations se terminent dans les deux heures trente.|
|**Mettre à jour** |Modification de propriété d’instance (mot de passe administrateur, connexion Azure AD, indicateur Azure Hybrid Benefit)|N/A|Jusqu’à une minute.|
|Update |Scale-up ou scale-down du stockage d’instance (niveau de service Usage général)|Attachement de fichiers de base de données|90 % des opérations ne prennent pas plus de cinq minutes.|
|Update |Scale-up et scale-down du stockage d’instance (niveau de service Critique pour l’entreprise)|- Redimensionnement de cluster virtuel<br>- Amorçage de groupe de disponibilité Always On|90 % des opérations se terminent dans les 2,5 heures + durée nécessaire pour amorcer toutes les bases de données (220 Go/heure).|
|Update |Scale-up et scale-down de la capacité de calcul des instances (vCores) (Usage général)|- Redimensionnement de cluster virtuel<br>- Attachement de fichiers de base de données|90 % des opérations se terminent dans les deux heures trente.|
|Update |Scale-up et scale-down de la capacité de calcul des instances (vCores) (Critique pour l’entreprise)|- Redimensionnement de cluster virtuel<br>- Amorçage de groupe de disponibilité Always On|90 % des opérations se terminent dans les 2,5 heures + durée nécessaire pour amorcer toutes les bases de données (220 Go/heure).|
|Update |Scale-down de l’instance à quatre vCores (Usage général)|- Redimensionnement de cluster virtuel (s’il s’agit du premier redimensionnement, il peut nécessiter la création d’un cluster virtuel**)<br>- Attachement de fichiers de base de données|90 % des opérations se terminent dans les quatre heures cinq.**|
|Update |Scale-down de l’instance à 4 vCores (Critique pour l’entreprise)|- Redimensionnement de cluster virtuel (s’il s’agit du premier redimensionnement, il peut nécessiter la création d’un cluster virtuel**)<br>- Amorçage de groupe de disponibilité Always On|90 % des opérations se terminent dans les quatre heures + durée nécessaire pour amorcer toutes les bases de données (220 Go/heure).|
|Update |Modification du niveau de service de l’instance (Usage général vers Critique pour l’entreprise et vice versa)|- Redimensionnement de cluster virtuel<br>- Amorçage de groupe de disponibilité Always On|90 % des opérations se terminent dans les 2,5 heures + durée nécessaire pour amorcer toutes les bases de données (220 Go/heure).|
|**Suppression**|Suppression d’instance|Sauvegarde de la fin du journal pour toutes les bases de données|90 % des opérations se terminent en une minute.<br>Remarque : Si la dernière instance du sous-réseau est supprimée, cette opération planifiera la suppression du cluster virtuel après 12 heures.***|
|Suppression|Suppression de cluster virtuel (en tant qu’opération lancée par l’utilisateur)|Suppression de cluster virtuel|90 % des opérations ne nécessitent pas plus d’une heure trente.|

\* Le cluster virtuel est généré en fonction de la génération du matériel.

\*\* L’option à 4 vCores a été publiée en juin 2019 et nécessite une nouvelle version de cluster virtuel. Si vous avez des instances du sous-réseau cible qui ont été créées avant le 12 juin, un nouveau cluster virtuel sera déployé automatiquement pour héberger quatre instances de vCores.

\*\*\* 12 heures est la configuration actuelle, mais cela peut changer à l’avenir. Ne prenez donc pas ce critère comme une dépendance. Si vous devez supprimer un cluster virtuel plus tôt (par exemple pour libérer le sous-réseau), consultez [Supprimer un sous-réseau après avoir supprimé une instance managée](virtual-cluster-delete.md).

### <a name="instance-availability-during-management-operations"></a>Disponibilité de l’instance pendant les opérations de gestion

SQL Managed Instance **est disponible pendant les opérations de mise à jour**, à l’exception d’un court temps d’arrêt dû au basculement qui se produit à la fin de la mise à jour. Elle dure généralement jusqu’à 10 secondes, même en cas de transactions longues interrompues, grâce à la [récupération de base de données accélérée](../accelerated-database-recovery.md).

> [!IMPORTANT]
> Nous vous déconseillons de mettre à l’échelle la capacité de calcul ou de stockage d’Azure SQL Managed Instance ou de modifier le niveau de service en même temps que les transactions de longue durée (importation de données, travaux de traitement des données, régénération d’index, etc.). Le basculement de base de données qui est effectué à la fin de l’opération annule toutes les transactions en cours.

SQL Managed Instance n’est pas accessible aux applications clientes pendant les opérations de déploiement et de suppression.

### <a name="management-operations-cross-impact"></a>Impact sur les opérations de gestion

Les opérations de gestion des instances managées peuvent affecter les autres opérations de gestion des instances placées dans le même cluster virtuel. Notamment :

- Les **opérations de restauration durables** dans un cluster virtuel mettent en attente une opération de création ou de mise à l’échelle d’une autre instance dans le même sous-réseau.<br/>**Exemple :** Si une opération de restauration durable est en cours d’exécution et qu’il existe une demande de création ou de mise à l’échelle dans le même sous-réseau, cette demande prend plus de temps à s’exécuter, car elle attend la fin de l’opération de restauration avant de continuer.
    
- L’opération suivante de **création ou de mise à l’échelle d’instance** est mise en attente par l’opération de création ou de mise à l’échelle d’instance lancée précédemment, qui a initié le redimensionnement du cluster virtuel.<br/>**Exemple :** Dans le cas de plusieurs demandes de création et/ou de mise à l’échelle dans le même sous-réseau, sous le même cluster virtuel, si l’une d’elles lance le redimensionnement du cluster virtuel, toutes les demandes soumises 5 minutes ou plus après celle qui a nécessité le redimensionnement du cluster virtuel durent plus longtemps que prévu, car ces demandes doivent attendre la fin de ce redimensionnement pour être traitées.

- Les **opérations de création/mise à l’échelle soumises dans un délai de 5 minutes** sont traitées par lot et exécutées en parallèle.<br/>**Exemple :** Un seul redimensionnement de cluster virtuel est effectué pour toutes les opérations soumises dans un délai de 5 minutes (à compter de l’exécution de la première demande d’opération). Si une autre demande est soumise plus de 5 minutes après la première, elle attend la fin du redimensionnement du cluster virtuel avant de commencer à s’exécuter.

> [!IMPORTANT]
> Les opérations de gestion mises en attente en raison d’une autre opération en cours sont relancées automatiquement une fois les conditions remplies. Aucune action de l’utilisateur n’est requise pour reprendre les opérations de gestion temporairement suspendues.

### <a name="canceling-management-operations"></a>Annulation des opérations de gestion

Le tableau suivant récapitule la possibilité d’annuler des opérations de gestion spécifiques et les durées totales habituelles :

Category  |Opération  |Annulable  |Durée d’annulation estimée  |
|---------|---------|---------|---------|
|Déploiement |Création d’instance |Non |  |
|Update |Scale-up ou scale-down du stockage d’instance (Usage général) |Non |  |
|Update |Scale-up et scale-down du stockage d’instance (Critique pour l’entreprise) |Oui |90 % des opérations ne prennent pas plus de cinq minutes. |
|Update |Scale-up et scale-down de la capacité de calcul des instances (vCores) (Usage général) |Oui |90 % des opérations ne prennent pas plus de cinq minutes. |
|Update |Scale-up et scale-down de la capacité de calcul des instances (vCores) (Critique pour l’entreprise) |Oui |90 % des opérations ne prennent pas plus de cinq minutes. |
|Update |Modification du niveau de service de l’instance (Usage général vers Critique pour l’entreprise et vice versa) |Oui |90 % des opérations ne prennent pas plus de cinq minutes. |
|DELETE |Suppression d’instance |Non |  |
|DELETE |Suppression de cluster virtuel (en tant qu’opération lancée par l’utilisateur) |Non |  |

Pour annuler l’opération de gestion, accédez au panneau Présentation et cliquez sur la zone de notification de l’opération en cours. Sur le côté droit, un écran avec l’opération en cours s’affiche et un bouton permet d’annuler l’opération. Après un premier clic, vous êtes invité à cliquer à nouveau et à confirmer que vous voulez annuler l’opération.

[![Annuler une opération](./media/sql-managed-instance-paas-overview/canceling-operation.png)](./media/sql-managed-instance-paas-overview/canceling-operation.png#lightbox)

Une fois que l’annulation de la demande a été envoyée et traitée, vous recevez une notification indiquant si l’annulation de l’envoi a réussi ou non.

En cas de réussite de l’annulation, l’opération de gestion est annulée en quelques minutes, entraînant un échec.

![Résultat d’opération d’annulation](./media/sql-managed-instance-paas-overview/canceling-operation-result.png)

Si la demande d’annulation échoue ou que le bouton n’est pas actif, cela signifie que l’opération de gestion n’est pas à l’état annulable et qu’elle se termine dans quelques minutes. L’opération de gestion poursuit son exécution jusqu’à ce qu’elle soit terminée.

> [!IMPORTANT]
> L’opération d’annulation n’est actuellement prise en charge que dans le portail.

## <a name="advanced-security-and-compliance"></a>Sécurité et conformité avancées

SQL Managed Instance intègre les fonctionnalités de sécurité avancées fournies par la plateforme Azure et le moteur de base de données SQL Server.

### <a name="security-isolation"></a>Isolation à des fins de sécurité

SQL Managed Instance offre une meilleure isolation de la sécurité vis-à-vis des autres locataires dans la plateforme Azure. L’isolation de la sécurité inclut :

- [Implémentation d’un réseau virtuel natif](connectivity-architecture-overview.md) et sa connexion à votre environnement local à l’aide d’Azure ExpressRoute ou d’une passerelle VPN.
- Dans un déploiement par défaut, le point de terminaison SQL est exposé uniquement par le biais d’une adresse IP privée, ce qui permet de sécuriser la connexion à partir de réseaux Azure privés ou hybrides.
- Locataire unique avec infrastructure sous-jacente dédiée (calcul, stockage).

Le diagramme suivant présente différentes options de connectivité pour vos applications :

![Haute disponibilité](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Pour plus d’informations sur l’intégration de réseau virtuel et l’application de stratégie de mise en réseau au niveau du sous-réseau, consultez [Architecture de réseau virtuel pour les instances managées](connectivity-architecture-overview.md) et [Connecter votre application à une instance managée](connect-application-instance.md).

> [!IMPORTANT]
> Placez plusieurs instances gérées dans le même sous-réseau, partout où vos exigences de sécurité l’autorisent, car cela vous apportera des avantages supplémentaires. La colocation d’instances dans le même sous-réseau simplifie considérablement la maintenance de l’infrastructure réseau et réduit le temps de provisionnement des instances, car une durée de provisionnement longue est associée au coût de déploiement de la première instance managée dans un sous-réseau.

### <a name="security-features"></a>Fonctionnalités de sécurité

Azure SQL Managed Instance fournit un ensemble de fonctionnalités de sécurité avancées qui peuvent être utilisées pour protéger vos données.

- [L’audit SQL Managed Instance](auditing-configure.md) suit les événements de base de données et les écrit dans un fichier journal d’audit placé dans votre compte de stockage Azure. L’audit peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données et à découvrir des discordances et anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité.
- Chiffrement des données en mouvement : SQL Managed Instance sécurise vos données par le biais d’un chiffrement des données en mouvement à l’aide du protocole TLS. En plus du protocole TLS, SQL Managed Instance offre une protection des données sensibles en vol, au repos et pendant le traitement des requêtes avec [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always offre la protection des données contre les failles de sécurité impliquant le vol de données critiques. Par exemple, avec Always Encrypted, les numéros de carte de crédit sont toujours chiffrés dans la base de données, même pendant le traitement des requêtes, ce qui permet le déchiffrement au point d’utilisation par les applications ou le personnel autorisés qui doivent traiter ces données.
- [Advanced Threat Protection](threat-detection-configure.md) complète l’[audit](auditing-configure.md) en fournissant une couche supplémentaire d’informations de sécurité intégrée au service qui détecte les tentatives d’accès ou d’exploitation de base de données inhabituelles et potentiellement dangereuses. Vous êtes alerté en cas d’activités suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux à la base de données. Les alertes de la protection avancée contre les menaces sont consultables dans [Azure Security Center](https://azure.microsoft.com/services/security-center/). Elles fournissent des détails sur les activités suspectes et recommandent l’action à entreprendre pour analyser et atténuer la menace.  
- Le [masquage dynamique des données](/sql/relational-databases/security/dynamic-data-masking) limite l’exposition des données sensibles en les masquant aux utilisateurs sans privilèges. Le masquage des données dynamique contribue à empêcher tout accès non autorisé aux données sensibles en vous permettant d’indiquer la quantité de données sensibles à révéler avec un impact minimal sur la couche Application. Il s’agit d’une fonctionnalité de sécurité basée sur des stratégies qui masque les données sensibles dans le jeu de résultats d’une requête, sur des champs de base de données désignés (les données dans la base de données ne sont pas modifiées).
- La [sécurité au niveau des lignes](/sql/relational-databases/security/row-level-security) (RLS) vous permet de contrôler l’accès aux lignes d’une table de base de données en fonction des caractéristiques de l’utilisateur qui exécute une requête (par exemple, appartenance à un groupe ou contexte d’exécution). La sécurité au niveau des colonnes simplifie la conception et le codage de la sécurité dans votre application. Elle vous permet d’implémenter des restrictions sur l’accès aux lignes de données. Par exemple, en s’assurant que les employés ne peuvent accéder qu’aux lignes de données utiles à leur service, ou en limitant l’accès aux données aux seules données pertinentes.
- [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) : également appelé chiffrement des données au repos, chiffre les fichiers de données SQL Managed Instance. TDE effectue le chiffrement et le déchiffrement d’E/S en temps réel des données et des fichiers journaux. Le chiffrement utilise une clé de chiffrement de base de données stockée dans l’enregistrement de démarrage de base de données à des fins de disponibilité lors de la récupération. Vous pouvez protéger toutes vos bases de données dans une instance managée avec Transparent Data Encryption. Il s’agit de la technologie de chiffrement au repos éprouvée de SQL Server, qui est requise par de nombreuses normes de conformité comme protection contre le vol d’un support de stockage.

La migration d’une base de données chiffrée vers SQL Managed Instance est prise en charge via Azure Database Migration Service ou une restauration native. Si vous envisagez de migrer une base de données chiffrée en utilisant une restauration native, la migration du certificat TDE existant de l’instance SQL Server vers SQL Managed Instance est une étape obligatoire. Pour plus d’informations sur les options de migration, consultez [Migration de SQL Server vers SQL Managed Instance](migrate-to-instance-from-sql-server.md).

## <a name="azure-active-directory-integration"></a>Intégration d’Azure Active Directory

SQL Managed Instance prend en charge les connexions à des moteurs de base de données SQL Server traditionnelles et les connexions intégrées à Azure AD. Les principaux (connexions) de serveur Azure AD (**préversion publique**) correspondent à une version cloud Azure des connexions aux bases de données locales que vous utilisez dans votre environnement local. Les principaux (connexions) de serveur Azure AD vous permettent de spécifier des utilisateurs et des groupes de votre locataire Azure AD sous forme de principaux limités à une instance et capables d’effectuer toutes les opérations au niveau de l’instance, y compris les requêtes entre plusieurs bases de données au sein de la même instance managée.

Une nouvelle syntaxe a été ajoutée pour créer des principaux (connexions) de serveur Azure AD (**À PARTIR D’UN FOURNISSEUR EXTERNE**). Pour plus d’informations sur la syntaxe, consultez <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> et lisez l’article [Provisionner un administrateur Azure Active Directory pour SQL Managed Instance](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Intégration d’Azure Active Directory et authentification multifacteur

SQL Managed Instance vous permet de gérer de manière centralisée les identités d’utilisateur de base de données et d’autres services Microsoft avec l’[Intégration d’Azure Active Directory](../database/authentication-aad-overview.md). Cette fonctionnalité simplifie la gestion des autorisations et améliore la sécurité. Azure Active Directory prend en charge l’[authentification multifacteur](../database/authentication-mfa-ssms-configure.md) pour augmenter la sécurité des données et des applications, ainsi qu’un processus d’authentification unique.

### <a name="authentication"></a>Authentification

L’authentification SQL Managed Instance fait référence à la façon dont les utilisateurs prouvent leur identité quand ils se connectent à la base de données. SQL Managed Instance prend en charge deux types d’authentification :  

- **Authentification SQL** :

  Cette méthode d’authentification utilise un nom d’utilisateur et un mot de passe.
- **Authentification Azure Active Directory** :

  Cette méthode d’authentification utilise des identités gérées par Azure Active Directory, et est prise en charge pour les domaines managés et intégrés. Utilisez l’authentification Active Directory (sécurité intégrée) [dans la mesure du possible](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autorisation

Le terme autorisation fait référence aux actions qu’un utilisateur peut exécuter au sein d’une base de données dans Azure SQL Managed Instance. Celles-ci sont contrôlées par les appartenances aux rôles et les autorisations au niveau objet de la base de données de votre compte d’utilisateur. SQL Managed Instance a les mêmes fonctionnalités d’autorisation que SQL Server 2017.

## <a name="database-migration"></a>Migration de base de données

SQL Managed Instance cible des scénarios d’utilisateur impliquant une migration de base de données en masse depuis des implémentations locales ou IaaS. SQL Managed Instance prend en charge plusieurs options de migration de base de données :

### <a name="backup-and-restore"></a>Sauvegarde et restauration  

L’approche de la migration s’appuie sur les sauvegardes SQL dans Stockage Blob Azure. Les sauvegardes stockées dans un objet blob de stockage Azure peuvent être directement restaurées dans une instance managée à l’aide de la [commande T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Pour obtenir un guide de démarrage rapide montrant comment restaurer le fichier de sauvegarde de base de données Wide World Importers - Standard, consultez [Restaurer un fichier de sauvegarde dans une instance managée](restore-sample-database-quickstart.md). Ce guide de démarrage rapide vous montre que vous devez charger un fichier de sauvegarde dans le stockage d’objets blob Azure et le sécuriser à l’aide d’une clé de signature d’accès partagé (SAS).
- Pour plus d’informations sur la restauration à partir d’une URL, consultez [Restauration native à partir d’une URL](migrate-to-instance-from-sql-server.md#native-restore-from-url).

> [!IMPORTANT]
> Les sauvegardes d’une instance managée peuvent uniquement être restaurées sur une autre instance managée. Elles ne peuvent pas être restaurées sur une instance SQL Server ou une base de données Azure SQL.

### <a name="database-migration-service"></a>Database Migration Service

Azure Database Migration Service est un service complètement managé conçu pour permettre la migration transparente de plusieurs sources de base de données vers des plateformes de données Azure, pour un temps d’arrêt minime. Ce service simplifie les tâches nécessaires pour déplacer les bases de données externes et les bases de données SQL Server vers Azure SQL Database, Azure SQL Managed Instance et SQL Server dans la machine virtuelle Azure. Consultez [Comment migrer votre base de données locale vers SQL Managed Instance à l’aide de Database Migration Service](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Fonctionnalités SQL prises en charge

SQL Managed Instance a pour but d’offrir une compatibilité de la surface d’exposition proche de 100 % avec la dernière version de SQL Server par le biais d’un plan de mise en production intermédiaire. Pour obtenir la liste des fonctionnalités et des différences, consultez [Comparaison des fonctionnalités SQL Managed Instance](../database/features-comparison.md). Pour obtenir la liste des différences de T-SQL entre SQL Managed Instance et SQL Server, consultez [Différences de T-SQL entre SQL Managed Instance et SQL Server](transact-sql-tsql-differences-sql-server.md).

SQL Managed Instance prend en charge la compatibilité descendante avec les bases de données SQL Server 2008. La migration directe à partir de serveurs de base de données SQL Server 2005 est prise en charge, le niveau de compatibilité des bases de données SQL Server 2005 qui ont migré est mis à jour vers SQL Server 2008.
  
Le diagramme suivant présente la compatibilité de la surface d’exposition dans SQL Managed Instance :  

![Migration](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>Principales différences entre SQL Server local et SQL Managed Instance

SQL Managed Instance tire parti du fait d’être toujours à jour dans le cloud, ce qui signifie que certaines fonctionnalités de SQL Server peuvent quant à elles être obsolètes, supprimées ou remplacées par d’autres. Dans certains cas, les outils doivent reconnaître qu'une fonctionnalité particulière agit de façon légèrement différente ou que le service est exécuté dans un environnement que vous ne contrôlez pas entièrement.

Quelques différences clés :

- La haute disponibilité est intégrée et préconfigurée avec une technologie similaire aux [groupes de disponibilité AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Le client ne dispose que de sauvegardes automatisées et de la restauration dans le temps limitée. Les clients peuvent lancer des sauvegardes `copy-only` qui n’interfèrent pas avec la chaîne de sauvegarde automatique.
- La spécification de chemins physiques complets n’étant pas prise en charge, tous les scénarios correspondants doivent être pris en charge d’une autre façon : RESTORE DB ne prend pas en charge WITH MOVE, CREATE DB n’autorise pas les chemins d’accès physiques, BULK INSERT fonctionne uniquement avec les objets BLOB Azure, etc.
- SQL Managed Instance prend en charge l’[authentification Azure AD](../database/authentication-aad-overview.md) comme une alternative cloud à l’authentification Windows.
- SQL Managed Instance gère automatiquement le groupe de fichiers et les fichiers XTP des bases de données contenant des objets OLTP en mémoire.
- SQL Managed Instance prend en charge SSIS (SQL Server Integration Services) et peut héberger le catalogue SSIS (SSISDB) qui stocke les packages SSIS, mais ceux-ci sont exécutés sur une instance Azure-SSIS IR (Integration Runtime) managée dans Azure Data Factory. Consultez [Créer un runtime d’intégration Azure-SSIS dans Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Pour comparer les fonctionnalités SSIS, consultez [Comparer SQL Database et SQL Managed Instance](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

### <a name="administration-features"></a>Fonctionnalités d’administration

SQL Managed Instance permet à l’administrateur système de passer moins de temps sur les tâches d’administration, car le service les effectue pour vous ou les simplifie considérablement. Par exemple, l’[installation et la mise à jour corrective d’un système d’exploitation ou système de gestion de base de données relationnelle](../database/high-availability-sla.md), le [redimensionnement et la configuration d’une instance dynamique](../database/single-database-scale.md), les [sauvegardes](../database/automated-backups-overview.md), la [réplication de base de données](replication-between-two-instances-configure-tutorial.md) (notamment des bases de données système), la [configuration de la haute disponibilité](../database/high-availability-sla.md) et la configuration des flux de données de [supervision des performances](../../azure-monitor/insights/azure-sql.md) et de l’intégrité.

Pour plus d’informations, consultez [une liste des fonctionnalités SQL Managed Instance prises en charge et non prises en charge](../database/features-comparison.md) et les [différences T-SQL entre SQL Managed Instance et SQL Server](transact-sql-tsql-differences-sql-server.md).

### <a name="programmatically-identify-a-managed-instance"></a>Identifier par programmation une instance managée

Le tableau suivant montre plusieurs propriétés, accessibles par le biais de Transact-SQL, que vous pouvez utiliser pour détecter que votre application fonctionne avec SQL Managed Instance et récupérer des propriétés importantes.

|Propriété|Valeur|Commentaire|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Cette valeur est identique à celle indiquée dans SQL Database. **N'indique pas** le moteur SQL version 12 (SQL Server 2014). SQL Managed Instance exécute toujours la dernière version stable du moteur SQL, qui est égale ou supérieure à la dernière version RTM disponible de SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Cette valeur est identique à celle indiquée dans SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Cette valeur identifie de façon unique une instance managée.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nom DNS d’instance complet au format suivant :`<instanceName>`.`<dnsPrefix>`.database.windows.net, où `<instanceName>` est le nom fourni par le client, tandis que `<dnsPrefix>` est une partie générée automatiquement du nom garantissant l’unicité des noms DNS globaux (par exemple, « wcus17662feb9ce98 »)|Exemple : my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment créer votre première instance managée, consultez le [Guide de démarrage rapide](instance-create-quickstart.md).
- Pour consulter la liste des fonctionnalités et les comparer, consultez [Fonctionnalités SQL communes](../database/features-comparison.md).
- Pour plus d’informations sur la configuration du réseau virtuel, consultez [Configuration de réseau virtuel SQL Managed Instance](connectivity-architecture-overview.md).
- Pour obtenir un guide de démarrage rapide qui crée une instance managée et restaure une base de données à partir d’un fichier de sauvegarde, consultez [Créer une instance managée](instance-create-quickstart.md).
- Pour accéder à un tutoriel expliquant comment utiliser Azure Database Migration Service pour la migration, consultez [Migration SQL Managed Instance à l’aide d’Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
- Pour une supervision avancée des performances de base de données SQL Managed Instance avec des informations de dépannage intégrées, consultez [Superviser Azure SQL Managed Instance avec Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Pour plus d’informations sur la tarification, voir [Tarification SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).
