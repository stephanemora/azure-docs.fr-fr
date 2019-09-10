---
title: Utiliser Azure Data Factory pour migrer des données d’un cluster Hadoop local vers stockage Azure | Microsoft Docs
description: Utilisez Azure Data Factory pour migrer des données d’un cluster Hadoop local vers stockage Azure.
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
ms.date: 8/30/2019
ms.openlocfilehash: 1b26b22fa9cdf9f6671702ceb9640aa39a6ecf17
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211599"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-hadoop-cluster-to-azure-storage"></a>Utiliser Azure Data Factory pour migrer des données d’un cluster Hadoop local vers stockage Azure 

Azure Data Factory fournit un mécanisme performant, robuste et rentable pour migrer des données à l’échelle d’un HDFS local vers Stockage Blob Azure ou Azure Data Lake Storage Gen2. En fait, Azure Data Factory propose deux approches pour migrer des données à partir d’un HDFS local vers Azure. Vous pouvez sélectionner chacune d’elles en fonction de votre scénario. 

- Mode DistCp d’ADF. Prise en charge d’ADF en utilisant [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) pour copier des fichiers en l’état vers un objet blob Azure (y compris une [copie intermédiaire](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)) ou Azure Data Lake Store, auquel cas il est possible de tirer pleinement parti de la puissance de votre cluster au lieu d’exécuter la commande sur le runtime d’intégration auto-hébergé. Le débit de la copie est ainsi amélioré, en particulier quand votre cluster est très puissant. Selon votre configuration dans Azure Data Factory, l’activité de copie construit automatiquement une commande DistCp, l’envoie à votre cluster Hadoop et surveille l’état de la copie. Grâce à l’intégration d’ADF avec DistCp, le client peut non seulement tirer parti de votre cluster puissant existant pour obtenir le meilleur débit de copie, mais également profiter de la planification flexible et de l’expérience de surveillance unifiée d’ADF. Par défaut, le mode DistCp d’ADF est la méthode recommandée pour migrer des données d’un cluster Hadoop local vers Azure.
- Mode IR natif d’ADF. Dans certains scénarios, DistCp ne peut pas fonctionner pour vos cas d’utilisation (par exemple, dans un environnement de réseau virtuel, l’outil DistCp ne prend pas en charge à la fois l’homologation privée Express Route et le point de terminaison de réseau virtuel Stockage Azure). En outre, il se peut que vous ne vouliez pas utiliser votre cluster Hadoop existant comme moteur de migration des données, car cela fait peser de lourdes charges sur votre cluster, ce qui peut avoir une incidence sur les performances de travaux ETL existants. Dans ce cas, vous pouvez utiliser la capacité native d’ADF, où le runtime d’intégration d’ADF peut être le moteur pour copier les données d’un HDFS local vers Azure.

Cet article fournit les informations suivantes pour les deux approches à destination des ingénieurs et développeurs de données :
> [!div class="checklist"]
> * Performances 
> * Résilience de copie
> * Sécurité du réseau
> * Architecture de solution de haut niveau 
> * Meilleures pratiques en matière d’implémentation  

## <a name="performance"></a>Performances

En mode DistCp d’ADF, le débit est identique à celui obtenu avec l’utilisation de l’outil DistCp indépendamment, qui tire parti de la capacité de votre cluster Hadoop existant. Le mode DistCp (copie distribuée) est un outil utilisé pour la copie inter/intra-cluster à grande échelle. Il utilise MapReduce pour sa distribution, la gestion et la récupération d’erreurs, et la génération de rapports. Il développe une liste de fichiers et de répertoires en entrée pour mapper des tâches, qui copient chacune une partition des fichiers spécifiés dans la liste source. En utilisant ADF intégré avec DistCp, vous pouvez créer des pipelines pour utiliser pleinement la bande passante de votre réseau, ainsi que les E/S par seconde et la bande passante de stockage pour maximiser le débit de déplacement de données pour votre environnement.  

En mode natif IR d’ADF, il permet également un parallélisme à différents niveaux, qui peut utiliser pleinement la bande passante de votre réseau, ainsi que les IOPS et la bande passante de stockage pour maximiser le débit de déplacement de données en mettant manuellement à l’échelle l’ordinateur IR auto-hébergé ou en augmentant la taille des instances de plusieurs machines IR auto-hébergées.

- Une seule activité de copie peut tirer parti de plusieurs ressources de calcul évolutives. Avec le runtime d’intégration auto-hébergé, vous pouvez faire monter en puissance manuellement la machine ou opérer une montée en charge de plusieurs machines ([jusqu’à 4 nœuds](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), de sorte qu’une activité de copie partitionne son jeu de fichiers sur tous les nœuds. 
- Une activité de copie unique lit et écrit dans le magasin de données à l’aide de plusieurs conversations. 
- Le flux de contrôle ADF peut démarrer plusieurs activités de copie en parallèle, par exemple [Pour chaque boucle](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Pour plus d’informations, voir le [Guide sur les performances de l’activité de copie](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Résilience

En mode DistCp d’ADF, vous pouvez utiliser différents paramètres de ligne de commande DistCp (par exemple, -i, pour ignorer les échecs ; -update, pour écrire des données lorsque les tailles du fichier source et du fichier de destination diffèrent) afin d’atteindre différents niveaux de résilience.

En mode IR natif d’ADF, dans une exécution d’activité de copie unique, ADF intègre un mécanisme de nouvelle tentative qui permet de gérer un certain niveau d’échecs temporaires dans les magasins de données ou dans le réseau sous-jacent. Lors d’une copie binaire d’un HDFS local vers un Blob et d’un HDFS local vers ADLS Gen2, ADF vérifie automatiquement les points de contrôle à grande échelle. Si l’exécution d’une activité de copie a échoué ou a expiré, lors d’une nouvelle tentative (Assurez-vous que le nombre de nouvelles tentatives est > 1), la copie reprend à partir du dernier point d’échec au lieu de commencer depuis le début.

## <a name="network-security"></a>Sécurité du réseau 

Par défaut, ADF transfère les données du HDFS local vers le stockage Blob Azure ou Azure Data Lake Storage Gen2 à l’aide d’une connexion chiffrée via le protocole HTTPS.  Le protocole HTTPS assure le chiffrement des données en transit et empêche les écoutes clandestines et les attaques de l’intercepteur. 

Sinon, si vous ne souhaitez pas que les données soient transférées via l’internet public, vous pouvez obtenir une sécurité accrue en transférant les données via un lien d’appairage privé et via Azure Express Route. Reportez-vous à l’architecture de la solution ci-dessous pour savoir comment procéder.

## <a name="solution-architecture"></a>Architecture de solution

Migrer des données sur l’Internet public :

![solution-architecture-public-network](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- Dans cette architecture, les données sont transférées en toute sécurité à l’aide du protocole HTTPS sur l’Internet public. 
- Le mode DistCp d’ADF est recommandé pour une utilisation dans un environnement de réseau public. Ainsi, vous pouvez non seulement tirer parti de votre cluster puissant existant pour obtenir le meilleur débit de copie, mais également profiter de la planification flexible et de l’expérience de surveillance unifiée d’ADF.
- Vous devez installer le runtime d’intégration auto-hébergé d’ADF sur un ordinateur Windows derrière un pare-feu d’entreprise pour soumettre la commande DistCp à votre cluster Hadoop et surveiller l’état de la copie. Étant donné que cette machine n’est pas le moteur qui déplace les données (à des fins de contrôle uniquement), la capacité de l’a machine n’a pas d’incidence sur le débit du déplacement des données.
- Les paramètres existants de la commande DistCp sont pris en charge.


Migrer des données via un lien privé : 

![solution-architecture-private-network](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- Dans cette architecture, la migration des données s’effectue via un lien d’appairage privé et via Azure Express Route, de telle sorte que les données ne transitent jamais par l’Internet public.
- L’outil DistCp ne prenant pas en charge à la fois l’homologation privée Express Route et le point de terminaison de réseau virtuel de Stockage Azure, il est recommandé d’utiliser la capacité native d’ADF via le runtime d’intégration pour migrer les données.
- Vous devez installer le runtime d'intégration ADF auto-hébergé sur une machine virtuelle Windows au sein de votre réseau virtuel Azure pour réaliser cette architecture. Vous pouvez mettre à l’échelle manuellement vos machines virtuelles ou monter en charge sur plusieurs machines virtuelles pour utiliser pleinement votre réseau et vos IOPS/bande passante de stockage.
- La configuration de démarrage recommandée pour chaque machine virtuelle Azure (avec le runtime d’intégration auto-hébergé d’ADF installé) est Standard_D32s_v3 avec des processeurs virtuels 32 et une mémoire de 128 Go. Vous pouvez conserver la surveillance de l’utilisation du processeur et de la mémoire de la machine virtuelle pendant la migration des données afin de déterminer si vous devez augmenter la taille de la machine virtuelle afin d’améliorer les performances ou en réduire la taille afin de réduire les coûts.
- Vous pouvez également effectuer une montée en charge en associant jusqu’à 4 nœuds de machine virtuelle à un seul runtime d’intégration auto-hébergé. Une seule tâche de copie exécutée sur un runtime d’intégration auto-hébergé partitionne automatiquement le jeu de fichiers et tire parti de tous les nœuds de machine virtuelle pour copier les fichiers en parallèle. Pour une haute disponibilité, il est recommandé de commencer par deux nœuds de machine virtuelle pour éviter un point de défaillance unique pendant la migration des données.
- Cette architecture permet à la fois la migration initiale des données instantanées et la migration des données delta.


## <a name="implementation-best-practices"></a>Meilleures pratiques en matière d’implémentation 

### <a name="authentication-and-credential-management"></a>Authentification et gestion des informations d’identification 

- Pour vous authentifier auprès de HDFS, vous pouvez utiliser [Windows (Kerberos) ou Anonymous](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties). 
- Plusieurs types d’authentification sont pris en charge pour la connexion à Azure Blob Storage.  L’utilisation d’[identités gérées pour les ressources Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) est fortement recommandée : basée sur une identification ADF automatiquement gérée dans Azure AD, elle vous permet de configurer des pipelines sans fournir d’informations d’identification dans la définition de service lié .  Vous pouvez également vous authentifier auprès de Azure Blob Storage à l’aide du [Principal de service](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), de la [signature d’accès partagé](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication) ou de la [clé de compte de stockage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Plusieurs types d’authentification sont également pris en charge pour la connexion à Azure Data Lake Storage Gen2.  L’utilisation d’[identités gérées pour les ressources Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) est fortement recommandée, bien que le [principal de service](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) ou la [clé de compte de stockage](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) puissent également être utilisés. 
- Lorsque vous n’utilisez pas d’identités gérées pour les ressources Azure, il est vivement recommandé de [stocker les informations d’identification dans Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) pour faciliter la gestion et la rotation des clés sans modifier les services liés ADF.  Il s’agit également de l'une des [meilleures pratiques pour CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migration initiale des données d’instantané 

En mode DistCp d’ADF, vous pouvez créer une activité de copie pour envoyer la commande DistCp avec des différents paramètres afin de contrôler le comportement initial de migration des données. 

En mode IR natif d’ADF, la partition de données est recommandée en particulier lors de la migration de plus de 10 To de données. Pour partitionner les données, tirez parti des noms de dossier sur HDFS. Ensuite, chaque travail de copie d’ADF peut copier une partition de dossier à la fois. Vous pouvez exécuter plusieurs tâches de copie ADF simultanément pour un meilleur débit.
Si l’un des travaux de copie échoue en raison d’un problème temporaire de réseau ou de magasin de données, vous pouvez réexécuter la tâche de copie ayant échoué pour recharger cette partition spécifique à partir de HDFS. Tous les autres travaux de copie qui chargent d’autres partitions ne seront pas affectés.

### <a name="delta-data-migration"></a>Migration des données Delta 

En mode DistCp d’ADF, vous pouvez utiliser des paramètres de ligne de commande DistCp (update, pour écrire des données lorsque les tailles du fichier source et du fichier de destination diffèrent) afin d’atteindre différents niveaux de résilience.

En mode IR natif d’ADF, la méthode la plus efficace pour identifier les fichiers nouveaux ou modifiés à partir de HDFS est d’utiliser une convention d’affectation de noms partitionnée : lorsque les données de HDFS ont été partitionnées avec les informations de tranche de temps dans le nom de fichier ou de dossier (par exemple,/yyyy/mm/dd/file.csv), votre pipeline peut facilement identifier les fichiers/dossiers à copier de façon incrémentielle.
En guise d’alternative, si vos données de HDFS ne sont pas partitionnées, ADF peut identifier les fichiers nouveaux ou modifiés par leur LastModifiedDate. La façon de procéder est la suivante : ADF analyse tous les fichiers de HDFS et copie uniquement le nouveau fichier mis à jour dont la date de dernière modification est supérieure à une certaine valeur. Sachez que si vous avez un grand nombre de fichiers dans HDFS, l’analyse de fichiers initiale peut prendre beaucoup de temps, quel que soit le nombre de fichiers correspondant à la condition de filtre. Dans ce cas, vous avez la possibilité de partitionner les données en premier, en utilisant la même partition pour la migration initiale des instantanés, afin que l’analyse des fichiers puisse se produire en parallèle.

### <a name="estimating-price"></a>Estimation du coût 

Considérez le pipeline suivant construit pour la migration de données de HDFS vers le Stockage Blob Azure : 

![pricing-pipeline](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Prenons l’exemple suivant : 

- Le volume total de données est de 1 Po.
- Migration de données à l’aide de la deuxième architecture de solution (mode IR natif d’ADF)
- 1 Po est divisé en 1 000 partitions et chaque copie déplace une partition.
- Chaque activité de copie est configurée avec un IR auto-hébergé associé à 4 machines et atteint un débit de 500 Mbits/s.
- L’accès concurrentiel ForEach est défini sur 4 et le débit agrégé est de 2 Gbits/s.
- Au total, la migration prend 146 heures.


Voici le prix estimé selon les hypothèses ci-dessus : 

![pricing-table](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Il s’agit d’un exemple de tarification hypothétique.  Vos tarifs réels dépendent du débit réel dans votre environnement.
> Le prix de la machine virtuelle Azure Windows (avec le runtime d’intégration auto-hébergé installé) n’est pas inclus.

### <a name="additional-references"></a>Références supplémentaires 
- [Connecteur HDFS](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Connecteur Stockage Blob Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Connecteur Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Guide sur les performances et le réglage de l’activité de copie](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Créer et configurer un runtime d’intégration auto-hébergé](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Extensibilité et haute disponibilité du runtime d’intégration auto-hébergé](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considérations relatives à la sécurité des déplacements de données](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Stocker les informations d’identification dans Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copier le fichier de façon incrémentielle en fonction du nom de fichier partitionné](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Copier les fichiers nouveaux et modifiés basés sur LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Page de tarification ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Étapes suivantes

- [Copier des fichiers issus de plusieurs conteneurs avec Azure Data Factory](solution-template-copy-files-multiple-containers.md)