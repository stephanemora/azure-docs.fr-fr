---
title: Utiliser Azure Data Factory pour migrer des données du serveur Netezza local vers Azure | Microsoft Docs
description: Utilisez Azure Data Factory pour migrer des données du serveur Netezza local vers Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/03/2019
ms.openlocfilehash: 4690fd81247035267861b06c204c6db7a052eba5
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259457"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-netezza-server-to-azure"></a>Utiliser Azure Data Factory pour migrer des données du serveur Netezza local vers Azure 

Azure Data Factory fournit un mécanisme performant, robuste et économique pour migrer des données à grande échelle du serveur Netezza local vers le stockage Azure ou Azure SQL Data Warehouse. Cet article fournit les informations suivantes à l’attention des ingénieurs et des développeurs de données :

> [!div class="checklist"]
> * Performances 
> * Résilience de copie
> * Sécurité du réseau
> * Architecture de solution de haut niveau 
> * Meilleures pratiques en matière d’implémentation  

## <a name="performance"></a>Performances

Azure Data Factory offre une architecture serverless qui autorise le parallélisme à différents niveaux, ce qui permet aux développeurs de créer des pipelines pour utiliser pleinement votre bande passante réseau, ainsi que la bande passante de base de données pour maximiser le débit de déplacement de données pour votre environnement.

![performances](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

- Une seule activité de copie peut tirer parti des ressources de calcul scalables : lorsque vous utilisez Azure Integration Runtime, vous pouvez spécifier [jusqu’à 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) pour chaque activité de copie serverless ; lorsque vous utilisez un runtime d’intégration auto-hébergé, vous pouvez effectuer un scale-up manuel de la machine ou un scale-out vers plusieurs machines ([jusqu’à 4 nœuds](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), et une seule activité de copie distribuera sa partition entre tous les nœuds. 
- Une activité de copie unique lit et écrit dans le magasin de données à l’aide de plusieurs conversations. 
- Le flux de contrôle Azure Data Factory peut démarrer plusieurs activités de copie en parallèle, par exemple à l’aide d’une [boucle For Each](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Vous pouvez obtenir plus d’informations à partir du [Guide sur les performances de l’activité de copie](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)

## <a name="resilience"></a>Résilience

Dans une exécution d’activité de copie unique, Azure Data Factory intègre un mécanisme de nouvelle tentative qui permet de gérer un certain niveau d’échecs temporaires dans les magasins de données ou dans le réseau sous-jacent.

L’activité de copie dans Azure Data Factory offre également deux moyens de traiter des lignes incompatibles lors de la copie de données entre les magasins de données source et récepteur. Vous pouvez abandonner et laisser en échec l’activité de copie en cas de détection de données incompatibles, ou continuer à copier les données de reste en ignorant les lignes de données incompatibles. En outre, vous pouvez consigner les lignes incompatibles dans le stockage d’objets blob Azure ou Azure Data Lake Store pour déterminer la cause de l’échec, corriger les données sur la source de données et retenter l’activité de copie.

## <a name="network-security"></a>Sécurité du réseau 

Par défaut, Azure Data Factory transfère les données du serveur Netezza local vers le stockage Azure ou Azure SQL Data Warehouse à l’aide d’une connexion chiffrée via le protocole HTTPS. Cela assure le chiffrement des données en transit et empêche les écoutes clandestines et les attaques de l’intercepteur.

Sinon, si vous ne souhaitez pas que les données soient transférées via l’internet public, vous pouvez obtenir une sécurité accrue en transférant les données via un lien d’appairage privé et via Azure Express Route. Reportez-vous à l’architecture de la solution ci-dessous pour savoir comment procéder.

## <a name="solution-architecture"></a>Architecture de solution

Migrer des données sur l’Internet public :

![solution-architecture-public-network](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

- Dans cette architecture, les données sont transférées en toute sécurité à l’aide du protocole https via l’Internet public.
- Vous devez installer le runtime d’intégration auto-hébergé Azure Data Factory sur une machine Windows derrière le pare-feu d’entreprise pour obtenir cette architecture. Veillez à ce que votre runtime d’intégration auto-hébergé Azure Data Factory sur une machine Windows puisse accéder directement à votre serveur Netezza. Vous pouvez effectuer un scale-up manuel de votre machine ou un scale-out vers plusieurs machines pour utiliser pleinement votre bande passante réseau et la bande passante des magasins de données pour copier les données.
- Cette architecture permet à la fois la migration initiale des données instantanées et la migration des données delta.

Migrer des données via un lien privé : 

![solution-architecture-private-network](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

- Dans cette architecture, la migration des données s’effectue via un lien d’appairage privé et via Azure Express Route, de telle sorte que les données ne transitent jamais par l’Internet public. 
- Vous devez installer le runtime d’intégration auto-hébergé Azure Data Factory sur une machine virtuelle Windows au sein de votre réseau virtuel Azure pour réaliser cette architecture. Vous pouvez effectuer un scale-up manuel de vos machines virtuelles ou un scale-out vers plusieurs machines virtuelles pour utiliser pleinement votre bande passante réseau et la bande passante des magasins de données pour copier les données.
- Cette architecture permet à la fois la migration initiale des données instantanées et la migration des données delta.

## <a name="implementation-best-practices"></a>Meilleures pratiques en matière d’implémentation 

### <a name="authentication-and-credential-management"></a>Authentification et gestion des informations d’identification 

- Pour vous authentifier auprès de Netezza, vous pouvez utiliser l’[authentification ODBC via une chaîne de connexion](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties). 
- Plusieurs types d’authentification sont pris en charge pour la connexion à Azure Blob Storage.  L’utilisation d’[identités managées pour les ressources Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) est fortement recommandée : basée sur une identité Azure Data Factory automatiquement managée dans Azure AD, elle vous permet de configurer des pipelines sans fournir d’informations d’identification dans la définition du service lié.  Vous pouvez également vous authentifier auprès de Azure Blob Storage à l’aide du [Principal de service](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), de la [signature d’accès partagé](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication) ou de la [clé de compte de stockage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Plusieurs types d’authentification sont également pris en charge pour la connexion à Azure Data Lake Storage Gen2.  L’utilisation d’[identités gérées pour les ressources Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) est fortement recommandée, bien que le [principal de service](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) ou la [clé de compte de stockage](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) puissent également être utilisés. 
- Plusieurs types d’authentification sont également pris en charge pour la connexion à Azure SQL Data Warehouse. L’utilisation d’[identités managées pour les ressources Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity) est fortement recommandée, bien que le [principal de service](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) ou l’[authentification SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication) puissent également être utilisés.
- Lorsque vous n’utilisez pas d’identités managées pour les ressources Azure, il est vivement recommandé de [stocker les informations d’identification dans Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) pour faciliter la gestion centralisée et la rotation des clés sans modifier les services liés Azure Data Factory.  Il s’agit également de l'une des [meilleures pratiques pour CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migration initiale des données d’instantané 

Pour les petites tables, si leur volume est inférieur à 100 Go ou si elles peuvent être migrées vers Azure en 2 heures, vous pouvez faire en sorte que chaque tâche de copie charge les données par table. Vous pouvez exécuter plusieurs tâches de copie Azure Data Factory pour charger différentes tables simultanément afin d’obtenir un meilleur débit. 

Pour chaque tâche de copie, vous pouvez également atteindre un certain niveau de parallélisme en utilisant le [paramètre parallelCopies](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) avec l’option de partition de données pour exécuter des requêtes parallèles et copier les données par partitions. Vous pouvez choisir deux options de partition de données, détaillées ci-dessous.
- Vous êtes encouragé à commencer à partir de la tranche de données, car cela est plus efficace.  Veillez à ce que la valeur de parallélisme dans le paramètre parallelCopies soit inférieure au nombre total de partitions de la tranche de données dans votre table sur le serveur Netezza.  
- Si le volume de chaque partition de la tranche de données est encore important (par exemple, supérieur à 10 Go), il est recommandé de basculer vers une partition de plage dynamique, où vous aurez davantage de flexibilité pour définir le nombre de partitions et le volume pour chaque partition par colonne de partition, limite supérieure et limite inférieure.

Pour les tables volumineuses, lorsque leur volume dépasse 100 Go ou si elles ne peuvent pas être migrées vers Azure en 2 heures, il vous est recommandé de partitionner les données à l’aide d’une requête personnalisée, puis de faire en sorte que chaque tâche de copie ne copie qu’une partition à la fois. Vous pouvez exécuter plusieurs tâches de copie Azure Data Factory simultanément pour un meilleur débit. N’oubliez pas que, pour que chaque cible de tâche de copie charge une partition par requête personnalisée, vous pouvez encore activer le parallélisme via une tranche de données ou une plage dynamique pour augmenter le débit. 

Si l’une des tâches de copie échoue en raison d’un problème temporaire de réseau ou de magasin de données, vous pouvez réexécuter la tâche de copie ayant échoué pour recharger cette partition spécifique à partir de la table. Tous les autres travaux de copie qui chargent d’autres partitions ne seront pas affectés.

Lors du chargement de données dans Azure SQL Data Warehouse, Polybase est suggéré pour être activé dans une tâche de copie avec un stockage d’objets blob Azure comme stockage intermédiaire.

### <a name="delta-data-migration"></a>Migration des données Delta 

Pour identifier les lignes nouvelles ou mises à jour de votre table, utilisez une colonne d’horodatage ou une clé d’incrémentation dans le schéma, puis stockez la valeur la plus récente en tant que limite supérieure dans une table externe, qui peut être utilisée pour filtrer les données delta pour le prochain chargement de données. 

Différentes tables peuvent utiliser une colonne de limite différente pour identifier les lignes nouvelles ou mises à jour. Nous vous suggérons de créer une table de contrôle externe dans laquelle chaque ligne représentera une table sur le serveur Netezza avec son nom de colonne de limite spécifique et sa valeur de limite supérieure. 

### <a name="self-hosted-integration-runtime-configuration-on-azure-vm-or-machine"></a>Configuration du runtime d’intégration auto-hébergé sur une machine ou une machine virtuelle Azure

Étant donné que vous migrez des données à partir d’un serveur Netezza vers Azure, que le serveur Netezza soit local derrière votre pare-feu d’entreprise ou dans un environnement de réseau virtuel, vous devez installer le runtime d’intégration auto-hébergé sur une machine Windows ou une machine virtuelle, qui sera le moteur de déplacement des données.

- La configuration recommandée de démarrage pour chaque machine ou machine virtuelle est de 32 processeurs virtuels et 128 Go de mémoire. Vous pouvez continuer à surveiller l’utilisation du processeur et de la mémoire de la machine de runtime d’intégration pendant la migration des données afin de déterminer si vous devez continuer le scale-up de la machine pour améliorer les performances ou effectuer un scale-down de la machine pour réduire les coûts.
- Vous pouvez également effectuer un scale-out en associant jusqu’à 4 nœuds à un seul runtime d’intégration auto-hébergé. Une seule tâche de copie exécutée sur un runtime d’intégration auto-hébergé tire automatiquement parti de tous les nœuds de machine virtuelle pour copier les fichiers en parallèle. Pour une haute disponibilité, il est recommandé de commencer par deux nœuds de machine virtuelle pour éviter un point de défaillance unique pendant la migration des données.

### <a name="rate-limiting"></a>Limitation du débit

La bonne pratique est de procéder à un examen des performances avec un exemple de jeu de données représentatif, afin de déterminer une taille de partition appropriée pour chaque activité de copie. Nous vous suggérons de faire en sorte que chaque partition soit chargée dans Azure dans un délai de 2 heures.  

Commencez par une seule activité de copie avec une seule machine de runtime d’intégration auto-hébergé pour copier une table. Augmentez progressivement le paramètre parallelCopies en fonction du nombre de partitions de tranche de données dans votre table et vérifiez si la table entière peut être chargée dans Azure dans un délai de 2 heures avec le débit que vous voyez de la tâche de copie. 

Si ce n’est pas possible et que la capacité du nœud de runtime d’intégration auto-hébergé et du magasin de données n’est pas entièrement utilisée, augmentez progressivement le nombre d’activités de copie simultanées jusqu’à atteindre les limites de votre réseau ou la bande passante limite des magasins de données. 

Poursuivez l’analyse de l’utilisation du processeur et de la mémoire sur la machine de runtime d’intégration auto-hébergé et préparez-vous à effectuer un scale-up de la machine ou un scale-out vers plusieurs machines lorsque vous voyez que le processeur et la mémoire sont entièrement utilisées. 

Lorsque vous rencontrez des erreurs de limitation signalées par l’activité de copie d’Azure Data Factory, réduisez le paramètre d’accès simultané ou parallelCopies dans Azure Data Factory, ou envisagez d’augmenter les limites de bande passante/IOPS des magasins de données et du réseau. 


### <a name="estimating-price"></a>Estimation du coût 

Considérez le pipeline suivant, construit pour la migration de données à partir d’un serveur Netezza local vers Azure SQL Data Warehouse :

![pricing-pipeline](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Prenons l’exemple suivant : 

- Le volume total de données est de 50 To. 
- Migration des données à l’aide de la première architecture de solution (serveur Netezza local derrière le pare-feu)
- Les 50 To sont divisés en 500 partitions et chaque activité de copie déplace une partition.
- Chaque activité de copie est configurée avec un runtime d’intégration auto-hébergé sur 4 machines et atteint un débit de 20 Mbits/s. (Dans l’activité de copie, parallelCopies a pour valeur 4 et chaque thread de chargement de données à partir de la table atteint un débit de 5 Mbits/s)
- L’accès concurrentiel ForEach est défini sur 3 et le débit agrégé est de 60 Mbits/s.
- Au total, il faut 243 heures pour réaliser la migration.

Voici le prix estimé selon les hypothèses ci-dessus : 

![pricing-table](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Il s’agit d’un exemple de tarification hypothétique. Vos tarifs réels dépendent du débit réel dans votre environnement. Le prix de la machine Windows (avec le runtime d’intégration auto-hébergé installé) n’est pas inclus. 

### <a name="additional-references"></a>Références supplémentaires 
- [Utilisation d’Azure Data Factory pour migrer des données à partir d’un entrepôt de données relationnelles local vers Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/Data_migration_from_on-prem_RDW_to_ADLS_using_ADF.pdf)
- [Connecteur Netezza](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [Connecteur ODBC](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Connecteur Stockage Blob Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Connecteur Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Connecteur Azure SQL Data Warehouse](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Guide sur les performances et le réglage de l’activité de copie](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Créer et configurer un runtime d’intégration auto-hébergé](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Extensibilité et haute disponibilité du runtime d’intégration auto-hébergé](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considérations relatives à la sécurité des déplacements de données](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Stocker les informations d’identification dans Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copier de façon incrémentielle des données d’une table](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Copier de façon incrémentielle des données de plusieurs tables](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Page de tarification d’Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Étapes suivantes

- [Copier des fichiers issus de plusieurs conteneurs avec Azure Data Factory](solution-template-copy-files-multiple-containers.md)