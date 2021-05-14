---
title: Guide de migration SQL Server vers SQL Server sur les machines virtuelles Azure
description: Dans ce guide, vous allez découvrir comment migrer vos bases de données SQL Server individuelles vers SQL Server sur les machines virtuelles Azure.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 77aec881cdf934888356dd276cad7d0698d56ad1
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108136226"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-virtual-machines"></a>Guide de migration : SQL Server vers SQL Server sur les machines virtuelles Azure

[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Dans ce guide, vous allez apprendre à *découvrir*, *évaluer* et *migrer* vos bases de données utilisateur de SQL Server vers une instance de SQL Server sur les machines virtuelles Azure à l’aide de la fonctionnalité de sauvegarde et restauration avec copie des journaux de transaction utilisant [l’Assistant Migration de données Microsoft](/sql/dma/dma-overview) à des fins d’évaluation.

Vous pouvez migrer SQL Server s’exécutant en local ou sur :

- SQL Server sur les machines virtuelles.
- Amazon Web Services (AWS) EC2.
- Amazon Relational Database Service (AWS RDS).
- Compute Engine (Google Cloud Platform [GCP]).

Pour plus d’informations sur des stratégies de migration supplémentaires, consultez la [vue d’ensemble de la migration de machine virtuelle SQL Server](sql-server-to-sql-on-azure-vm-migration-overview.md). Pour obtenir d’autres guides de migration, consultez les [Guides de migration de base de données Azure](/data-migration).

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Diagramme illustrant un flux du processus de migration.":::

## <a name="prerequisites"></a>Prérequis

La migration vers SQL Server sur les machines virtuelles Azure requiert les ressources suivantes :

- [Assistant Migration de données](https://www.microsoft.com/download/details.aspx?id=53595).
- [Projet Azure Migrate](../../../migrate/create-manage-projects.md)
- Instance cible préparée de [SQL Server sur les machines virtuelles Azure](../../virtual-machines/windows/create-sql-vm-portal.md) dont la version est identique ou supérieure à celle de la source SQL Server.
- [Connectivité entre Azure et l’environnement local](/azure/architecture/reference-architectures/hybrid-networking)
- [Choix d’une stratégie de migration appropriée](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)

## <a name="pre-migration"></a>Prémigration

Avant de commencer votre migration, vous devez découvrir la topologie de votre environnement SQL et évaluer la faisabilité de la migration prévue.

### <a name="discover"></a>Découvrez

Azure Migrate évalue la pertinence de la migration des ordinateurs locaux, effectue un dimensionnement en fonction des performances et fournit des estimations de coût pour une exécution locale. Pour planifier la migration, utilisez Azure Migrate afin d’[identifier les sources de données existantes ainsi que les détails relatifs aux fonctionnalités](../../../migrate/concepts-assessment-calculation.md) utilisées par vos instances de SQL Server. Ce processus implique une analyse du réseau pour identifier toutes les instances de SQL Server de votre organisation ainsi que la version et les fonctionnalités utilisées.

> [!IMPORTANT]
> Quand vous choisissez une machine virtuelle Azure cible pour votre instance SQL Server, veillez à prendre en compte les [Recommandations de performances pour SQL Server sur les machines virtuelles Azure](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md).

Pour accéder à des outils de découverte supplémentaires, consultez [Services et outils disponibles pour les scénarios de migration de données](../../../dms/dms-tools-matrix.md#business-justification-phase).

### <a name="assess"></a>Évaluer

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Une fois que vous avez découvert toutes les sources de données, utilisez [l’Assistant Migration de données](/sql/dma/dma-overview) pour évaluer les instances SQL Server locales qui migrent vers une instance SQL Server sur les machines virtuelles Azure afin de comprendre les différences entre les instances source et cible.

> [!NOTE]
> Si vous _ne mettez pas_ à niveau la version de SQL Server, ignorez cette étape et passez à la section [Migrer](#migrate).

#### <a name="assess-user-databases"></a>Évaluer les bases de données utilisateur

L’Assistant Migration de données vous aide à effectuer la migration vers une plateforme de données moderne en détectant les problèmes de compatibilité qui peuvent affecter les fonctionnalités de base de données dans la nouvelle version de SQL Server. L’Assistant Migration de données recommande des améliorations pour la fiabilité et les performances de votre environnement cible. De plus, il vous permet de déplacer votre schéma, vos données et vos objets de connexion de votre serveur source vers votre serveur cible.

Pour en savoir plus, consultez la section [Évaluation](/sql/dma/dma-migrateonpremsql).

> [!IMPORTANT]
>Selon le type d’évaluation, les autorisations requises sur le serveur SQL Server source peuvent être différentes :
   > - D’après les recommandations de l’outil Advisor sur la *parité des fonctionnalités*, les informations d’identification fournies pour se connecter à la base de données SQL Server source doivent être celles d’un membre du rôle serveur *sysadmin*.
   > - D’après les recommandations de l’outil Advisor sur les *problèmes de compatibilité*, les informations d’identification fournies doivent disposer au moins des autorisations `CONNECT SQL`, `VIEW SERVER STATE` et `VIEW ANY DEFINITION`.
   > - L’Assistant Migration de données met en évidence les autorisations nécessaires d’après l’outil Advisor choisi avant d’exécuter l’évaluation.

#### <a name="assess-the-applications"></a>Évaluer les applications

En règle générale, la couche Application accède aux bases de données utilisateur pour conserver et modifier les données. L’Assistant Migration de données peut évaluer la couche d’accès aux données d’une application de deux manières :

- À l’aide des [événements étendus](/sql/relational-databases/extended-events/extended-events) capturés ou des [traces du SQL Server Profiler](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) de vos bases de données utilisateur. Vous pouvez également vous servir de l’[Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-capture-trace) afin de créer un journal des traces utilisable pour les tests A/B.
- À l’aide de l’outil [Data Access Migration Toolkit (préversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) qui permet de découvrir et d’évaluer les requêtes SQL contenues dans le code et de migrer le code source de l’application d’une plateforme de base de données vers une autre. Cet outil prend en charge les types de fichiers populaires tels que C#, Java, XML et le texte brut. Pour obtenir un guide sur l’exécution d’une évaluation avec l’outil Data Access Migration Toolkit, consultez le billet de blog [Use Data Migration Assistant](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) (Utiliser l’Assistant Migration de données).

Lors de l’évaluation des bases de données utilisateur, utilisez l’Assistant Migration de données pour [importer](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) les fichiers de trace capturés ou les fichiers du Data Access Migration Toolkit.

#### <a name="assessments-at-scale"></a>Évaluation à grande échelle

Si vous disposez de plusieurs serveurs qui nécessitent une évaluation à l’aide de l’Assistant Migration de données, vous pouvez automatiser le processus via [l’interface de ligne de commande](/sql/dma/dma-commandline). À l’aide de l’interface, vous pouvez préparer des commandes d’évaluation à l’avance pour chaque instance de SQL Server comprise dans l’étendue de la migration.

Pour les rapports récapitulatifs portant sur de grands domaines, les évaluations de l’Assistant Migration de données peuvent désormais être [centralisées dans Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb).

#### <a name="refactor-databases-with-data-migration-assistant"></a>Refactoriser des bases de données avec l’Assistant Migration de données

En fonction des résultats de l’évaluation effectuée avec l’Assistant Migration de données, vous pouvez disposer d’une série de recommandations qui vous permettent de vérifier que vos bases de données utilisateur fonctionneront correctement après la migration. L’Assistant Migration de données fournit des détails sur les objets affectés, ainsi que des ressources pour la résolution de chaque problème. Assurez-vous de résoudre tous les changements cassants et tous les changements de comportement avant de commencer la migration de production.

Pour les fonctionnalités déconseillées, vous pouvez choisir d’exécuter vos bases de données utilisateur dans leur mode de [compatibilité](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) d’origine, si vous souhaitez éviter d’effectuer ces changements afin d’accélérer la migration. Cette action empêchera la [mise à niveau de la compatibilité de votre base de données](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) tant que les éléments déconseillés n’auront pas été résolus.

Vous devez créer un script de tous les correctifs de l’Assistant Migration de données et les appliquer à la base de données SQL Server cible lors de la phase [Post-migration](#post-migration).

> [!CAUTION]
> Toutes les versions de SQL Server ne prennent pas en charge l’ensemble des modes de compatibilité. Vérifiez que votre [version cible de SQL Server](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) prend en charge la compatibilité de base de données choisie. Par exemple, SQL Server 2019 ne prend pas en charge les bases de données ayant le niveau de compatibilité 90 (qui correspond à SQL Server 2005). Ces bases de données nécessitent au moins une mise à niveau vers le niveau de compatibilité 100.
>

## <a name="migrate"></a>Migrer

Une fois que vous avez effectué les étapes de pré-migration, vous êtes prêt à migrer les bases de données utilisateur et les composants. Migrez vos bases de données à l’aide de la [méthode de migration](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate) que vous préférez.

Les sections suivantes indiquent les étapes à suivre pour effectuer une migration à l’aide de la fonctionnalité de sauvegarde et restauration ou une migration avec temps d’arrêt minimal à l’aide de la fonctionnalité de sauvegarde et restauration avec copie des journaux de transaction.

### <a name="backup-and-restore"></a>Sauvegarde et restauration

Pour effectuer une migration standard à l’aide de la fonctionnalité de sauvegarde et restauration :

1. Configurez la connectivité vers SQL Server sur les machines virtuelles Azure en fonction de vos exigences. Pour plus d’informations, consultez [Se connecter à une machine virtuelle SQL Server sur Azure (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Suspendez ou arrêtez toutes les applications qui utilisent des bases de données qui doivent migrer.
1. Vérifiez que les bases de données utilisateur sont inactives à l’aide du [mode utilisateur unique](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
1. Effectuez une sauvegarde complète de la base de données vers un emplacement sur site.
1. Copiez vos fichiers de sauvegarde locaux sur votre machine virtuelle avec le Bureau à distance, [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) ou [l’utilitaire en ligne de commande AZCopy](../../../storage/common/storage-use-azcopy-v10.md). (Espace recommandé pour les sauvegardes : supérieur à 2 To).
1. Restaurez les sauvegardes complètes de base de données vers SQL Server sur les machines virtuelles Azure.

### <a name="log-shipping-minimize-downtime"></a>Copie des journaux de transaction (minimiser le temps d’arrêt)

Pour effectuer une migration avec un temps d’arrêt minimal à l’aide de la fonctionnalité de sauvegarde et restauration avec copie des journaux de transaction :

1. Configurez la connectivité à SQL Server sur les machines virtuelles Azure en fonction de vos exigences. Pour plus d’informations, consultez [Se connecter à une machine virtuelle SQL Server sur Azure (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Vérifiez que les bases de données utilisateur locales à migrer sont en mode de récupération complète ou en mode de récupération utilisant les journaux de transaction.
1. Effectuez une sauvegarde complète de base de données vers un emplacement local, puis modifiez tous les travaux existants de sauvegarde complète de base de données afin d’utiliser le mot clé [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) pour conserver la séquence de journaux de transaction consécutifs.
1. Copiez vos fichiers de sauvegarde locaux sur votre machine virtuelle avec le Bureau à distance, [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) ou [l’utilitaire en ligne de commande AZCopy](../../../storage/common/storage-use-azcopy-v10.md). (Espace recommandé pour les sauvegardes : supérieur à 1 To).
1. Restaurez les sauvegardes complètes de base de données vers SQL Server sur les machines virtuelles Azure.
1. Configurez la [copie des journaux de transaction](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) entre la base de données locale et l’instance SQL Server sur les machines virtuelles Azure. Veillez à ne pas réinitialiser les bases de données, car cette tâche a déjà été effectuée au cours des étapes précédentes.
1. Effectuez le basculement vers le serveur cible.
   1. Suspendez ou arrêtez les applications en utilisant les bases de données à migrer.
   1. Vérifiez que les bases de données utilisateur sont inactives à l’aide du [mode utilisateur unique](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
   1. Quand vous êtes prêt, effectuez un [basculement contrôlé](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) de la copie des journaux de transaction des bases de données locales vers SQL Server sur les machines virtuelles Azure.

### <a name="migrate-objects-outside-user-databases"></a>Migrer des objets en dehors des bases de données utilisateur

Des objets SQL Server supplémentaires sont parfois requis pour garantir un fonctionnement fluide de vos bases de données utilisateur après la migration.

Le tableau ci-dessous fournit une liste des composants de référence et des méthodes de migration recommandées à appliquer avant ou après la migration de vos bases de données utilisateur.

| **Fonctionnalité** | **Composant** | **Méthodes de migration** |
| --- | --- | --- |
| **Bases de données** | Modèle | Script avec SQL Server Management Studio. |
|| TempDB | Pour des performances optimales, prévoyez le déplacement de la base de données tempDB sur un [disque temporaire (SSD) de machine virtuelle Azure](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md#storage). Veillez à choisir une machine virtuelle disposant d’un disque SSD local dont la taille est suffisante pour accueillir votre base de données tempDB. |
|| Bases de données utilisateur avec FileStream | Utilisez les méthodes de [sauvegarde et de restauration](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) pour la migration. L’Assistant Migration de données ne prend pas en charge les bases de données avec FileStream. |
| **Sécurité** | Connexions SQL Server et Windows | Utilisez l’Assistant Migration de données pour [migrer les connexions utilisateur](/sql/dma/dma-migrateserverlogins). |
|| Rôles SQL Server | Script avec SQL Server Management Studio. |
|| Fournisseurs de chiffrement | Recommandez la [conversion pour utiliser Azure Key Vault](../../virtual-machines/windows/azure-key-vault-integration-configure.md). Cette procédure utilise le [fournisseur de ressources de machines virtuelles SQL](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md). |
| **Objets de serveur** | Unités de sauvegarde | Remplacez-les par une sauvegarde de base de données à l’aide de [Sauvegarde Azure](../../../backup/backup-sql-server-database-azure-vms.md) ou écrivez les sauvegardes dans le [Stockage Azure](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 CU2 +). Cette procédure utilise le [fournisseur de ressources de machines virtuelles SQL](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md).|
|| Serveurs liés | Script avec SQL Server Management Studio. |
|| Déclencheurs de serveur | Script avec SQL Server Management Studio. |
| **Réplication** | Publications locales | Script avec SQL Server Management Studio. |
|| Abonnés locaux | Script avec SQL Server Management Studio. |
| **PolyBase** | PolyBase | Script avec SQL Server Management Studio. |
| **Gestion** | Messagerie de base de données | Script avec SQL Server Management Studio. |
| **SQL Server Agent** | travaux | Script avec SQL Server Management Studio. |
|| Alertes | Script avec SQL Server Management Studio. |
|| Opérateurs | Script avec SQL Server Management Studio. |
|| Proxies | Script avec SQL Server Management Studio. |
| **Système d'exploitation** | Fichiers, partages de fichiers | Notez tous les autres fichiers ou partages de fichiers utilisés par vos serveurs SQL Server et répliquez-les sur la cible de machine virtuelle Azure. |

## <a name="post-migration"></a>Post-migration

Une fois la phase de Migration terminée, vous devez effectuer une série de tâches post-migration pour vous assurer que tout fonctionne de la manière la plus fluide et efficace possible.

### <a name="remediate-applications"></a>Corriger les applications

Une fois les données migrées vers l’environnement cible, toutes les applications qui consommaient la source doivent commencer à consommer la cible. Dans certains cas, il peut être nécessaire d’apporter des modifications aux applications.

Appliquez les correctifs recommandés par l’Assistant Migration de données aux bases de données utilisateur. Vous devez générer un script de ces correctifs pour garantir la cohérence et permettre l’automatisation.

### <a name="perform-tests"></a>Effectuer des tests

L’approche de test pour la migration de base de données comprend les activités suivantes :

1. **Développer des tests de validation** : pour tester la migration d’une base de données, vous devez utiliser des requêtes SQL. Créez des requêtes de validation à exécuter sur les bases de données source et cible. Vos requêtes de validation doivent couvrir l’étendue que vous avez définie.
1. **Configurer un environnement de test** : L’environnement de test doit contenir une copie de la base de données source et de la base de données cible. Veillez à isoler l’environnement de test.
1. **Exécuter des tests de validation** : exécutez les tests de validation sur la source et sur la cible, puis analysez les résultats.
1. **Exécuter des tests de performance** : exécutez des tests de performances sur la source et sur la cible, puis analysez et comparez les résultats.

> [!TIP]
> Utilisez [l’Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview) pour faciliter l’évaluation des performances du serveur cible SQL Server.

### <a name="optimize"></a>Optimiser

La phase post-migration est cruciale pour résoudre les problèmes d’exactitude et vérifier l’exhaustivité des données, ainsi que pour traiter les problèmes de performances liés à la charge de travail.

Pour plus d’informations sur ces problèmes et les étapes pour les atténuer, consultez :

- [Guide de validation et d’optimisation post-migration](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Réglage des performances sur les machines virtuelles Azure SQL](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md)
- [Centre d’optimisation des coûts Azure](https://azure.microsoft.com/overview/cost-optimization/)

## <a name="next-steps"></a>Étapes suivantes

- Pour vérifier la disponibilité des services qui s’appliquent à SQL Server, consultez le [Centre de l’infrastructure mondiale Azure](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).
- Si vous souhaitez obtenir une matrice des services et outils Microsoft et tiers qui peuvent vous aider dans différents scénarios de migration de données et de base de données ainsi que pour des tâches spécialisées, consultez [Services et outils disponibles pour les scénarios de migration de données](../../../dms/dms-tools-matrix.md).
- Pour en savoir plus sur Azure SQL, consultez :
   - [Options de déploiement](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server sur les machines virtuelles Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Outil de calcul du coût total de possession (TCO) Azure](https://azure.microsoft.com/pricing/tco/calculator/)

- Pour en savoir plus sur l’infrastructure et le cycle d’adoption des migrations cloud, consultez :
   - [Cloud Adoption Framework pour Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   - [Meilleures pratiques pour l’évaluation des coûts et le dimensionnement des charges de travail migrées vers Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- Pour plus d’informations sur la gestion des licences, consultez :
   - [BYOL (apportez votre propre licence) avec Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Bénéficier d’un support étendu gratuit pour SQL Server 2008 et SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Pour évaluer la couche d’accès aux applications, consultez [Data Access Migration Toolkit (préversion)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Pour plus d’informations sur la façon d’effectuer un test A/B pour la couche d’accès aux données, consultez [Vue d’ensemble de l’Assistant Expérimentation de base de données](/sql/dea/database-experimentation-assistant-overview).