---
title: Vue d’ensemble du compte de stockage
titleSuffix: Azure Storage
description: En savoir plus sur les différents types de comptes de stockage dans Stockage Azure. Passez en revue les noms des comptes, les niveaux de performance, les niveaux d’accès, la redondance, le chiffrement, les points de terminaison, etc.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/02/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d7eca7d8f3cd40f4a3961f0ac478fba290be3041
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279661"
---
# <a name="storage-account-overview"></a>Vue d’ensemble du compte de stockage

Un compte de stockage Azure contient tous vos objets de données de stockage Azure : objets blob, fichiers, files d’attente, tables et disques. Le compte de stockage fournit pour vos données de stockage Azure un espace de noms unique, accessible de n’importe où dans le monde via HTTP ou HTTPS. Les données dans votre compte de stockage Azure sont durables et hautement disponibles, sécurisées et massivement évolutives.

Pour plus d’informations sur la création d’un compte de stockage Azure, consultez [Créer un compte de stockage](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Types de compte de stockage

Le stockage Azure offre plusieurs types de comptes de stockage. Chaque type prend en charge différentes fonctionnalités et a son propre modèle tarifaire. Avant de créer un compte de stockage, tenez compte de ces différences pour déterminer l’option qui convient le mieux à vos applications. Les types de comptes de stockage proposés sont les suivants :

- **Comptes de stockage à usage général v2** : Type de compte de stockage de base pour les objets blob, les fichiers, les files d’attente et les tables. Recommandé pour la plupart des scénarios utilisant Stockage Azure.
- **Comptes v1 universels** : Type de compte de stockage hérité pour les objets blob, les fichiers, les files d’attente et les tables. Utilisez ce type de compte à la place des comptes v2 universels lorsque cela est possible.
- **Comptes BlockBlobStorage** : comptes de stockage dotés de caractéristiques de performances Premium pour les objets blob de bloc et les objets blob d'ajout. Recommandé pour les scénarios aux taux élevés de transactions, ceux utilisant des objets plus petits ou nécessitant une latence de stockage faible.
- **Comptes FileStorage** : comptes de stockage de fichiers uniquement ayant des caractéristiques de performances Premium. Recommandé pour l’entreprise ou des applications de mise à l’échelle hautes performances.
- **Comptes BlobStorage** : anciens comptes de stockage Blob uniquement. Utilisez ce type de compte à la place des comptes v2 universels lorsque cela est possible.

Le tableau suivant décrit les types de comptes de stockage, les services qu’ils prennent en charge et les modèles de déploiement pris en charge pour chaque type de compte :

| Type de compte de stockage | Services pris en charge | Options de redondance | Modèle de déploiement<sup>1</sup> |
|--|--|--|--|
| Universel v2 | Blob, fichier, file d’attente, table, disque et lac de données de 2e génération<sup>2</sup> | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS<sup>3</sup> | Gestionnaire de ressources |
| Universel v1 | Objets blob, fichiers, files d’attente, tables et disques | LRS, GRS, RA-GRS | Resource Manager, Classic |
| BlockBlobStorage | Objets blob (objets blob de blocs et objets blob d’ajout uniquement) | LRS, ZRS<sup>3</sup> | Gestionnaire de ressources |
| FileStorage | Fichier uniquement | LRS, ZRS<sup>3</sup> | Gestionnaire de ressources |
| BlobStorage | Objets blob (objets blob de blocs et objets blob d’ajout uniquement) | LRS, GRS, RA-GRS | Gestionnaire de ressources |

<sup>1</sup>L’utilisation du modèle de déploiement Azure Resource Manager est recommandée. Les comptes de stockage qui utilisent le modèle de déploiement classique peuvent toujours être créés à certains emplacements, et les comptes classiques existants continuent d’être pris en charge. Pour plus d’informations, consultez [Déploiement Azure Resource Manager et déploiement classique : comprendre les modèles de déploiement et l’état de vos ressources](../../azure-resource-manager/management/deployment-models.md).

<sup>2</sup>Azure Data Lake Storage Gen2 est un ensemble de capacités dédiées à l’analytique du Big Data et basées sur Stockage Blob Azure. Data Lake Storage Gen2 est pris en charge uniquement sur les comptes de stockage v2 universels avec un espace de noms hiérarchique activé. Pour plus d’informations sur Data Lake Storage Gen2, consultez [Présentation d’Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md).

<sup>3</sup>Le stockage redondant interzone (ZRS) et le stockage géoredondant interzone (GZRS/RA-GZRS) sont disponibles uniquement pour les comptes v2 universels, BlockBlobStorage et FileStorage standard dans certaines régions. Pour plus d’informations sur les options de redondance de Stockage Azure, consultez [Redondance du stockage Azure](storage-redundancy.md).

### <a name="storage-account-redundancy"></a>Redondance du compte de stockage

Les options de redondance pour un compte de stockage comprennent :

- **Stockage localement redondant (LRS)**  : Une stratégie de redondance simple et économique. Les données sont copiées de façon synchrone trois fois au sein d’un même emplacement physique dans la région primaire.
- **Stockage redondant interzone (ZRS)**  : Redondance pour les scénarios nécessitant une haute disponibilité. Les données sont copiées de façon synchrone sur trois zones de disponibilité Azure dans la région primaire.
- **Stockage géo-redondant (GRS)**  : Redondance entre les régions pour vous protéger en cas d’interruption régionale. Les données sont copiées de manière synchrone trois fois dans la région primaire, puis copiées de façon asynchrone dans la région secondaire. Pour un accès en lecture aux données dans la région secondaire, activez le stockage géoredondant avec accès en lecture (RA-GRS).
- **Stockage géoredondant interzone (GZRS)**  : Redondance pour les scénarios nécessitant à la fois une haute disponibilité et une durabilité maximale. Les données sont copiées de manière synchrone dans trois zones de disponibilité Azure dans la région primaire, puis copiées de façon asynchrone dans la région secondaire. Pour un accès en lecture aux données dans la région secondaire, activez le stockage géoredondant interzone avec accès en lecture (RA-GZRS).

Pour plus d’informations sur les options de redondance dans Stockage Azure, consultez [Redondance de Stockage Azure](storage-redundancy.md).

### <a name="general-purpose-v2-accounts"></a>Les comptes de stockage à usage général v2

Les comptes de stockage v2 à usage général prennent en charge les dernières fonctionnalités du Stockage Azure, et intègrent toutes les fonctionnalités des comptes de stockage v1 à usage général et des comptes de stockage d’objets blob. Pour le Stockage Azure, ce sont les comptes v2 à usage général qui offrent les tarifs de capacité par gigaoctet les plus bas. En outre, le prix des transactions est l’un des plus compétitifs du secteur. Les comptes de stockage universels v2 prennent en charge les services de stockage Azure suivants :

- Objets blob (tous les types : de blocs, d’ajout et de pages)
- Data Lake Gen2
- Fichiers
- Disques
- Files d’attente
- Tables

> [!NOTE]
> Microsoft recommande d’utiliser des comptes de stockage universels v2 pour la plupart des scénarios. Vous pouvez facilement mettre à niveau un compte de stockage universel v1 ou un compte de stockage d’objets blob vers un compte universel v2, sans que cela nécessite un temps d’arrêt ou la copie de données. Toutefois, la mise à niveau ne peut pas être annulée.
>
> Pour plus d’informations sur la mise à niveau vers un compte universel v2, consultez [Mettre à niveau vers un compte de stockage universel v2](storage-account-upgrade.md).

Les comptes de stockage universels v2 offrent plusieurs niveaux d’accès pour le stockage des données, qui sont basés sur vos modèles d’utilisation. Pour plus d’informations, consultez [Niveaux d’accès pour les données d’objets blob de blocs](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Comptes GPv1

Les comptes de stockage universels v1 offrent un accès à tous les services des Stockage Azure, mais ils ne proposent pas les fonctionnalités les plus récentes, ni les tarifs par gigaoctet les plus bas. Les comptes de stockage universels v1 prennent en charge les services de stockage Azure suivants :

- Objets blob (tous les types)
- Fichiers
- Disques
- Files d’attente
- Tables

Vous pouvez utiliser des comptes v1 universels pour les scénarios suivants :

- Si vos applications nécessitent le modèle de déploiement Azure Classic. Les comptes universels v2 et les comptes de stockage d’objets blob prennent uniquement en charge le modèle de déploiement Azure Resource Manager.

- Vos applications sont gourmandes en transactions ou utilisent beaucoup de bande passante de géoréplication, mais ne nécessitent pas une capacité importante. Dans ce cas, le compte universel v1 constitue le choix le plus économique.

- Vous utilisez une version de l’[API REST du service Stockage](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) antérieure à celle du 14/02/2014 ou une bibliothèque de client avec une version inférieure à 4.x. Vous ne pouvez pas mettre à niveau votre application.

> [!NOTE]
> Bien que Microsoft recommande les comptes v2 universels pour la plupart des scénarios, Microsoft continuera à prendre en charge les comptes v1 universels pour les clients nouveaux et existants. Vous pouvez créer des comptes de stockage v1 universels dans de nouvelles régions chaque fois que Stockage Azure est disponible dans ces régions. Microsoft ne prévoie pas actuellement de déprécier la prise en charge des comptes v1 universels, et fournira un préavis d’au moins un an avant de déprécier toute fonctionnalité Stockage Azure. Microsoft continuera à fournir des mises à jour de sécurité pour les comptes v1 universels, mais aucun nouveau développement de fonctionnalité n’est attendu pour ce type de compte.
>
> À compter du 1er octobre 2020, les tarifs des comptes v1 universels dans les nouvelles régions Stockage Azure sont équivalents à ceux des comptes v2 universels dans ces régions. Les tarifs dans les régions Stockage Azure existantes n’ont pas changé. Pour plus d’informations sur les tarifs des comptes v1 universels dans une région spécifique, consultez la page sur la tarification de Stockage Azure. Choisissez votre région puis, en regard de **Offres et tarifs**, sélectionnez **Autre**.

### <a name="blockblobstorage-accounts"></a>Comptes BlockBlobStorage

Un compte BlockBlobStorage est un compte de stockage spécialisé dans le niveau de performance Premium pour le stockage des données d’objets non structurées en tant qu’objets BLOB de blocs ou d’ajout. Comparés aux comptes v2 et BlobStorage à usage général, les comptes BlockBlobStorage fournissent une latence faible et cohérente ainsi que des taux de transactions plus élevés.

Les comptes BlockBlobStorage ne prennent actuellement pas en charge la hiérarchisation en niveau d’accès chaud, froid ou archive. Ce type de compte de stockage ne prend pas en charge les objets BLOB de pages, les tables ou les files d’attente.

### <a name="filestorage-accounts"></a>Comptes FileStorage

Un compte FileStorage est un compte de stockage spécialisée utilisé pour stocker et créer des partages de fichiers Premium. Ce genre de compte de stockage prend en charge les fichiers mais pas les objets blob de blocs, d’ajout, de pages, de tables ou de files d’attente.

Les comptes FileStorage offrent des caractéristiques de performances dédiées uniques, telles que l’IOPS en mode rafale. Pour plus d’informations sur ces caractéristiques, consultez la section [Niveaux de stockage de partage de fichiers](../files/storage-files-planning.md#storage-tiers) du guide de planification des fichiers.

## <a name="naming-storage-accounts"></a>Nommage des comptes de stockage

Gardez les règles suivantes à l’esprit lorsque vous nommez votre compte de stockage :

- Les noms de compte de stockage doivent avoir entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.
- Le nom de votre compte de stockage doit être unique dans Azure. Deux comptes de stockage ne peuvent avoir le même nom.

## <a name="performance-tiers"></a>Niveaux de performances

Selon le type de compte de stockage que vous créez, vous pouvez choisir entre les niveaux de performance standard et Premium. Le tableau suivant récapitule les niveaux de performances disponibles pour chaque type de compte de stockage.

| Type de compte de stockage | Niveaux de performances pris en charge |
|--|--|
| Universel v2 | Standard, Premium<sup>1</sup> |
| Universel v1 | Standard, Premium<sup>1</sup> |
| BlockBlobStorage | Premium |
| FileStorage | Premium |
| BlobStorage | Standard |

<sup>1</sup>Les performances Premium pour les comptes v1 et v2 universels sont disponibles uniquement pour les objets blob de page et les disques. Les performances Premium pour les objets blob de blocs ou d’ajout sont uniquement disponibles sur les comptes BlockBlobStorage. Les performances Premium pour les fichiers ne sont disponibles que sur les comptes FileStorage.

### <a name="general-purpose-storage-accounts"></a>Comptes de stockage à usage général

Les comptes de stockage universels peuvent être configurés avec l’un des niveaux de performances suivants :

- Un niveau de performances Standard pour le stockage des objets blob, des fichiers, des tables, des files d’attente et des disques de machine virtuelle Azure Pour plus d’informations sur les objectifs d’évolutivité pour les comptes de stockage standard, consultez [Objectifs d’extensibilité pour les comptes de stockage standard](scalability-targets-standard-account.md).
- Un niveau de performances Premium pour le stockage des disques de machine virtuelle non gérés. Microsoft recommande d’utiliser des disques gérés avec des machines virtuelles Azure au lieu de disques non gérés. Pour plus d’informations sur les objectifs d’extensibilité pour le niveau de performance Premium, consultez [Objectifs d’évolutivité pour les comptes de stockage d’objets blob de pages Premium](../blobs/scalability-targets-premium-page-blobs.md).

### <a name="blockblobstorage-storage-accounts"></a>Comptes de stockage BlockBlobStorage

Les comptes de stockage BlockBlobStorage fournissent un niveau de performances Premium pour stocker des objets blob de blocs et d’ajout. Pour plus d’informations, consultez [Objectifs de scalabilité pour les comptes de stockage d’objets blob de blocs Premium](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>Comptes de stockage FileStorage

Les comptes de stockage FileStorage fournissent un niveau de performances premium pour les partages de fichiers Azure. Pour plus d’informations, voir [Objectifs de performance et d’extensibilité d’Azure Files](../files/storage-files-scale-targets.md).

## <a name="access-tiers-for-block-blob-data"></a>Niveaux d’accès pour les données d’objets blob de blocs

Le stockage Azure propose différentes options permettant d’accéder aux données d’objets blob de blocs en fonction du modèle d’utilisation. Chaque niveau d’accès du stockage Azure est optimisé pour un modèle particulier d’utilisation des données. En sélectionnant le niveau d’accès qui correspond à vos besoins, vous pouvez stocker vos données d’objets blob de blocs de manière plus économique.

Les niveaux d’accès disponibles sont les suivants :

- Niveau d’accès **chaud**. Le niveau est optimisé pour les accès fréquents aux objets du compte de stockage. L’accès aux données de niveau Chaud est le plus économique. Les coûts de stockage sont toutefois plus élevés. Par défaut, les nouveaux comptes de stockage sont créés au niveau Chaud.
- Niveau d’accès **froid**. Le niveau est optimisé pour le stockage de grandes quantités de données rarement sollicitées et stockées depuis au moins 30 jours. Le stockage des données au niveau Froid est plus économique. Toutefois, l’accès à ces données peut être plus onéreux que celui du niveau chaud.
- Niveau d’accès **archive**. Le niveau est disponible uniquement pour chaque objet blob de blocs. Le niveau d’accès archive est optimisé pour les données qui peuvent tolérer plusieurs heures de latence de récupération et qui resteront dans le niveau d’accès archive pendant au moins 180 jours. Ce niveau est l’option la plus économique pour le stockage des données. Toutefois, l’accès à ces données est plus onéreux que l’accès aux données dans les niveaux d’accès chaud ou froid.

En cas de changement de votre modèle d’utilisation des données, vous pouvez basculer d’un niveau d’accès à l’autre à tout moment. Pour plus d’informations sur les niveaux d’accès, consultez [Stockage d’objets blob Azure : niveaux d’accès chaud, froid et archive](../blobs/storage-blob-storage-tiers.md).

Le tableau suivant répertorie les niveaux d’accès disponibles pour les blobs dans chaque type de compte de stockage.

| Type de compte de stockage | Niveaux d’accès pris en charge |
|--|--|
| Universel v2 | Chaud, froid, archive<sup>1</sup> |
| Universel v1 | N/A |
| BlockBlobStorage | N/A |
| FileStorage | N/A |
| BlobStorage | Chaud, froid, archive<sup>1</sup> |

<sup>1</sup> Le stockage archive et la hiérarchisation au niveau de l’objet blob prennent en charge uniquement les objets blob de blocs. L’archivage est disponible uniquement au niveau de chaque objet blob, pas au niveau du compte de stockage. Pour plus d’informations, consultez [Niveaux d’accès pour Stockage Blob Azure : chaud, froid et archive](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Le changement du niveau d’accès pour un compte de stockage ou un objet blob existant peut entraîner des frais supplémentaires. Pour plus d’informations, consultez [Facturation des comptes de stockage](#storage-account-billing).

## <a name="storage-account-endpoints"></a>Points de terminaison d'un compte de stockage

Un compte de stockage fournit un espace de noms unique dans Azure pour vos données. Chaque objet que vous stockez dans le stockage Azure a une adresse qui comprend votre nom de compte unique. La combinaison du nom du compte et du point de terminaison de service du stockage Azure forme les points de terminaison de votre compte de stockage.

Le tableau suivant répertorie les points de terminaison de chacun des services de Stockage Azure.

| Service de stockage | Point de terminaison |
|--|--|
| Stockage Blob | `https://<storage-account>.blob.core.windows.net` |
| Azure Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| Stockage de files d'attente | `https://<storage-account>.queue.core.windows.net` |
| Stockage de tables | `https://<storage-account>.table.core.windows.net` |

> [!NOTE]
> Les comptes d’objets blob de blocs et de stockage d’objets blob exposent uniquement le point de terminaison de service BLOB.

Construisez l’URL permettant d’accéder à un objet dans un compte de stockage en ajoutant l’emplacement de l’objet dans le compte de stockage au point de terminaison. Par exemple, une adresse d’objet blob peut avoir ce format : http://*moncomptedestockage*.blob.core.windows.net/*monconteneur*/*monobjetblob*.

Vous pouvez également configurer votre compte de stockage pour qu’il utilise un domaine personnalisé pour les objets blob. Pour plus d’informations, consultez [Configurer un nom de domaine personnalisé pour votre compte de Stockage Azure](../blobs/storage-custom-domain-name.md).  

## <a name="migrating-a-storage-account"></a>Migration d’un compte de stockage

Le tableau suivant résume et propose des conseils sur le déplacement, la mise à niveau ou la migration d’un compte de stockage :

| Scénario de migration | Détails |
|--|--|
| Déplacer un compte de stockage vers un autre abonnement | Azure Resource Manager propose plusieurs options pour déplacer une ressource vers un autre abonnement. Pour plus d’informations, consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Déplacer un compte de stockage vers un autre groupe de ressources | Azure Resource Manager propose plusieurs options pour déplacer une ressource vers un autre groupe de ressources. Pour plus d’informations, consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Déplacer un compte de stockage vers une autre région | Pour déplacer un compte de stockage, créez une copie de votre compte de stockage dans une autre région. Utilisez ensuite AzCopy ou un autre outil de votre choix pour déplacer vos données vers ce compte. Pour plus d’informations, consultez [Déplacer un compte Stockage Azure vers une autre région](storage-account-move.md). |
| Mettre à niveau vers un compte de stockage v2 à usage général | Vous pouvez mettre à niveau un compte de stockage v1 universel ou un compte de stockage Blob vers un compte v2 universel. Notez que cette action ne peut pas être annulée. Pour obtenir plus d’informations, consultez [Mettre à niveau vers un compte de stockage v2 à usage général](storage-account-upgrade.md). |
| Migrer un compte de stockage classique vers Azure Resource Manager | Le modèle de déploiement Azure Resource Manager est plus performant que le modèle de déploiement classique en termes de fonctionnalités, de scalabilité et de sécurité. Pour plus d’informations sur la migration d’un compte de stockage classique vers Azure Resource Manager, consultez [Migration de comptes de stockage](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts) dans **Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager**. |

## <a name="copying-data-into-a-storage-account"></a>Copie de données dans un compte de stockage

Microsoft fournit des utilitaires et des bibliothèques pour l’importation des données situées sur des appareils de stockage locaux ou dans des fournisseurs de stockage cloud tiers. La solution que vous devez utiliser dépend de la quantité de données à transférer.

Lorsque vous effectuez une mise à niveau vers un compte de stockage universel v2 à partir d’un compte v1 ou d’un compte de stockage d’objets blob, vos données sont automatiquement transférées. Microsoft recommande cette méthode pour la mise à niveau de votre compte. Toutefois, si vous décidez de déplacer les données d’un compte de stockage v1 universel vers un compte de stockage d’objets blob, vous devrez migrer vos données manuellement à l’aide des outils et des bibliothèques mentionnés ci-dessous.

### <a name="azcopy"></a>AzCopy

AzCopy est un utilitaire de ligne de commande Windows conçu pour la copie de données hautes performances vers ou à partir d’Azure Storage. Vous pouvez utiliser AzCopy pour copier des données dans votre compte de stockage d’objets blob à partir d’un compte de stockage universel existant, ou pour charger des données à partir de vos appareils de stockage locaux. Pour plus d’informations, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

### <a name="data-movement-library"></a>Bibliothèque de déplacement des données

La bibliothèque de déplacement de données Stockage Azure pour .NET est basée sur l’infrastructure principale de déplacement de données sous-tendant AzCopy. La bibliothèque est conçue pour assurer des opérations de transfert de données fiables, simples et hautes performances, comme AzCopy. Vous pouvez utiliser la bibliothèque de déplacement des données pour tirer parti des fonctionnalités AzCopy en mode natif. Pour plus d’informations, consultez l’article [Azure Storage Data Movement Library for .Net](https://github.com/Azure/azure-storage-net-data-movement) (Bibliothèque de déplacement de données Stockage Azure pour .NET)

### <a name="rest-api-or-client-library"></a>API REST ou bibliothèque cliente

Vous pouvez créer une application personnalisée pour migrer vos données d’un compte de stockage v1 universel vers un compte de stockage d’objets blob. Utilisez l’une des bibliothèques de client Azure ou l’API REST des services de Stockage Azure. Azure Storage offre des bibliothèques clientes enrichies pour une diversité de langages et plateformes, par exemple .NET, Java, C++, Node.JS, PHP, Ruby et Python. Les bibliothèques clientes offrent des fonctionnalités avancées telles que la logique de nouvelle tentative, la journalisation et les téléchargements parallèles. Vous pouvez également développer votre application directement avec l’API REST, qui peut être appelée à l’aide de n’importe quel langage permettant de créer des requêtes HTTP/HTTPS.

Pour plus d’informations sur l’API REST du stockage Azure, consultez [Référence de l’API REST des services Stockage Azure](/rest/api/storageservices/).

> [!IMPORTANT]
> Les objets blob chiffrés utilisant le chiffrement côté client stockent les métadonnées relatives au chiffrement avec l’objet blob. Si vous copiez un objet blob chiffré avec le chiffrement côté client, assurez-vous que l’opération de copie conserve les métadonnées de l’objet blob, et en particulier les métadonnées relatives au chiffrement. Si vous copiez un objet blob sans ces métadonnées de chiffrement, le contenu de l’objet blob ne peut plus être récupéré. Pour plus d’informations concernant les métadonnées liées au chiffrement, consultez l’article [Chiffrement côté client et Azure Key Vault pour Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="encryption"></a>Chiffrement

Toutes les données de votre compte de stockage sont chiffrées côté service. Pour plus d’informations sur le chiffrement, consultez [Azure Storage Service Encryption pour les données au repos](storage-service-encryption.md).

## <a name="storage-account-billing"></a>Facturation du compte de stockage

Le service Stockage Azure vous est facturé en fonction de l’utilisation de votre compte de stockage. Tous les objets d’un compte de stockage sont facturés ensemble en tant que groupe. Les coûts de stockage sont calculés en fonction des facteurs suivants :

- **Région** fait référence à la région géographique dans laquelle votre compte est basé.
- **Type de compte** fait référence au type de compte de stockage que vous utilisez.
- **Niveau d’accès** fait référence au modèle d’utilisation des données que vous avez spécifié pour votre compte de stockage v2 universel ou blob.
- La **capacité** fait référence à la quantité d’unités de compte de stockage que vous utilisez pour stocker des données.
- La **réplication** détermine le nombre de copies de vos données qui sont conservées simultanément et où.
- Les **transactions** font référence à toutes les opérations de lecture et d’écriture sur Stockage Azure.
- **Sortie de données** fait référence aux données transférées hors d’une région Azure. Lorsque les données de votre compte de stockage sont utilisées par une application qui n’est pas exécutée dans la même région, vous êtes facturé pour la sortie des données. Pour plus d’informations sur l’utilisation de groupes de ressources pour regrouper vos données et vos services dans la même région afin de limiter les coûts liés aux sorties de données, consultez [Qu’est-ce qu’un groupe de ressources Azure ?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

La page [Prix appliqués à Azure Storage](https://azure.microsoft.com/pricing/details/storage/) fournit des informations de tarification détaillées basées sur le type de compte, la capacité de stockage, la réplication et les transactions. La page [Détails de la tarification – Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/) fournit des informations de tarification détaillées pour les acheminements de données. Vous pouvez utiliser le [Calcul des coûts Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) pour faciliter l'estimation des coûts.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Créez un compte de stockage](storage-account-create.md)
- [Créer un compte de stockage d’objet blob de blocs](../blobs/storage-blob-create-account-block-blob.md)
- [Mettre à niveau vers un compte de stockage v2 à usage général](storage-account-upgrade.md)
- [récupérer un compte de stockage supprimé](storage-account-recover.md)