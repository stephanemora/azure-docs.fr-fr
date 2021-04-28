---
title: Vue d’ensemble du compte de stockage
titleSuffix: Azure Storage
description: En savoir plus sur les différents types de comptes de stockage dans Stockage Azure. Passez en revue les noms des comptes, les niveaux de performance, les niveaux d’accès, la redondance, le chiffrement, les points de terminaison, etc.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d4874ad6688fa85f0c511632498938817bb218f7
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714197"
---
# <a name="storage-account-overview"></a>Vue d’ensemble du compte de stockage

Un compte de stockage Azure contient tous vos objets de données de stockage Azure : objets blob, fichiers, files d’attente, tables et disques. Le compte de stockage fournit pour vos données de stockage Azure un espace de noms unique, accessible de n’importe où dans le monde via HTTP ou HTTPS. Les données dans votre compte de stockage Azure sont durables et hautement disponibles, sécurisées et massivement évolutives.

Pour plus d’informations sur la création d’un compte de stockage Azure, consultez [Créer un compte de stockage](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Types de compte de stockage

Le stockage Azure offre plusieurs types de comptes de stockage. Chaque type prend en charge différentes fonctionnalités et a son propre modèle tarifaire. Avant de créer un compte de stockage, tenez compte de ces différences pour déterminer l’option qui convient le mieux à vos applications.

Le tableau suivant décrit les types de comptes de stockage recommandés par Microsoft pour la plupart des scénarios :

| Type de compte de stockage | Services pris en charge | Options de redondance | Modèle de déploiement | Usage |
|--|--|--|--|--|
| Usage général v2 standard | Objet blob, fichier, file d’attente, table et Data Lake Storage<sup>1</sup> | LRS/GRS/RA-GRS<br /><br />ZRS/GZRS/RA-GZRS<sup>2</sup> | Gestionnaire des ressources<sup>3</sup> | Type de compte de stockage de base pour les objets blob, les fichiers, les files d’attente et les tables. Recommandé pour la plupart des scénarios utilisant Stockage Azure. |
| Objets blob de blocs Premium<sup>4</sup> | Objets blob de blocs uniquement | LRS<br /><br />ZRS<sup>2</sup> | Gestionnaire des ressources<sup>3</sup> | comptes de stockage dotés de caractéristiques de performances Premium pour les objets blob de bloc et les objets blob d'ajout. Recommandé pour les scénarios aux taux élevés de transactions, ceux utilisant des objets plus petits ou nécessitant une latence de stockage faible.<br />[En savoir plus…](../blobs/storage-blob-performance-tiers.md) |
| Partages de fichiers Premium<sup>4</sup> | Partages de fichiers uniquement | LRS<br /><br />ZRS<sup>2</sup> | Gestionnaire des ressources<sup>3</sup> | comptes de stockage de fichiers uniquement ayant des caractéristiques de performances Premium. Recommandé pour l’entreprise ou des applications de mise à l’échelle hautes performances.<br />[En savoir plus…](../files/storage-files-planning.md#management-concepts) |
| Objets blob de pages Premium<sup>4</sup> | Objets blob de pages uniquement | LRS | Gestionnaire des ressources<sup>3</sup> | Type de compte de stockage Premium pour les objets blob de pages uniquement.<br />[En savoir plus…](../blobs/storage-blob-pageblob-overview.md) |

<sup>1</sup> Azure Data Lake Storage est un ensemble de fonctionnalités dédiées à l’analytique du Big Data et basées sur le stockage Blob Azure. Data Lake Storage est pris en charge uniquement sur les comptes de stockage à usage général V2 avec un espace de noms hiérarchique activé. Pour plus d’informations sur Data Lake Storage Gen2, consultez [Présentation de Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md).

<sup>2</sup> Le stockage redondant interzone (ZRS) et le stockage géo-redondant interzone (GZRS/RA-GZRS) sont disponibles uniquement pour les comptes à usage général v2, d’objet blob de blocs premium et de partage de fichier premium standard dans certaines régions. Pour plus d’informations sur les options de redondance de Stockage Azure, consultez [Redondance du stockage Azure](storage-redundancy.md).

<sup>3</sup> Azure Resource Manager est le modèle de déploiement recommandé pour les ressources Azure, y compris les comptes de stockage. Pour plus d’informations, consultez [Présentation d’Azure Resource Manager.](../../azure-resource-manager/management/overview.md)

<sup>4</sup> Les comptes de stockage dans un niveau de performance Premium utilisent des disques SSD pour une faible latence et un débit élevé.

Les comptes de stockage hérités sont également pris en charge. Pour plus d’informations, consultez [Types de comptes de stockage hérités](#legacy-storage-account-types).

## <a name="storage-account-endpoints"></a>Points de terminaison d'un compte de stockage

Un compte de stockage fournit un espace de noms unique dans Azure pour vos données. Chaque objet que vous stockez dans le stockage Azure a une adresse qui comprend votre nom de compte unique. La combinaison du nom du compte et du point de terminaison de service du stockage Azure forme les points de terminaison de votre compte de stockage.

Gardez les règles suivantes à l’esprit lorsque vous nommez votre compte de stockage :

- Les noms de compte de stockage doivent avoir entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.
- Le nom de votre compte de stockage doit être unique dans Azure. Deux comptes de stockage ne peuvent avoir le même nom.

Le tableau suivant répertorie le format du point de terminaison de chacun des services de Stockage Azure.

| Service de stockage | Point de terminaison |
|--|--|
| Stockage Blob | `https://<storage-account>.blob.core.windows.net` |
| Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| Stockage de files d'attente | `https://<storage-account>.queue.core.windows.net` |
| Stockage de tables | `https://<storage-account>.table.core.windows.net` |

Construisez l’URL permettant d’accéder à un objet dans un compte de stockage en ajoutant l’emplacement de l’objet dans le compte de stockage au point de terminaison. Par exemple, l’URL d’un objet blob ressemblera à :

`http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*`

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

## <a name="transferring-data-into-a-storage-account"></a>Transfert de données dans un compte de stockage

Microsoft fournit des services et des utilitaires pour l’importation des données situées sur des appareils de stockage locaux ou dans des fournisseurs de stockage cloud tiers. La solution que vous devez utiliser dépend de la quantité de données à transférer. Pour plus d’informations, consultez [Vue d’ensemble de la migration de Stockage Azure](storage-migration-overview.md).

## <a name="storage-account-encryption"></a>Chiffrement du compte de stockage

Toutes les données de votre compte de stockage sont automatiquement chiffrées côté service. Pour plus d’informations sur le chiffrement et la gestion des clés, consultez [Chiffrement du stockage Azure pour les données au repos](storage-service-encryption.md).

## <a name="storage-account-billing"></a>Facturation du compte de stockage

Le service Stockage Azure vous est facturé en fonction de l’utilisation de votre compte de stockage. Tous les objets d’un compte de stockage sont facturés ensemble en tant que groupe. Les coûts de stockage sont calculés en fonction des facteurs suivants :

- **Région** fait référence à la région géographique dans laquelle votre compte est basé.
- **Type de compte** fait référence au type de compte de stockage que vous utilisez.
- **Niveau d’accès** fait référence au modèle d’utilisation des données que vous avez spécifié pour votre compte de stockage v2 universel ou blob.
- La **capacité** fait référence à la quantité d’unités de compte de stockage que vous utilisez pour stocker des données.
- La **redondance** détermine le nombre de copies de vos données qui sont conservées simultanément et à quels emplacements.
- Les **transactions** font référence à toutes les opérations de lecture et d’écriture sur Stockage Azure.
- **Sortie de données** fait référence aux données transférées hors d’une région Azure. Lorsque les données de votre compte de stockage sont utilisées par une application qui n’est pas exécutée dans la même région, vous êtes facturé pour la sortie des données. Pour plus d’informations sur l’utilisation de groupes de ressources pour regrouper vos données et vos services dans la même région afin de limiter les coûts liés aux sorties de données, consultez [Qu’est-ce qu’un groupe de ressources Azure ?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

La [page Prix appliqués à Azure Storage](https://azure.microsoft.com/pricing/details/storage/) fournit des informations de tarification détaillées basées sur le type de compte, la capacité de stockage, la réplication et les transactions. La page [Détails de la tarification – Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/) fournit des informations de tarification détaillées pour les acheminements de données. Vous pouvez utiliser la [calculatrice de prix Stockage Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) pour faciliter l’estimation des coûts.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="legacy-storage-account-types"></a>Types de comptes de stockage hérités

Le tableau suivant décrit les types de comptes de stockage hérités. Ces types de comptes ne sont pas recommandés par Microsoft, mais peuvent être utilisés dans certains scénarios :

| Type de compte de stockage hérité | Services pris en charge | Options de redondance | Modèle de déploiement | Usage |
|--|--|--|--|--|
| Usage général v1 standard | Objet blob, fichier, file d’attente, table et Data Lake Storage | LRS/GRS/RA-GRS | Resource Manager, Classic | Les comptes à usage général v1 ne proposent pas nécessairement les fonctionnalités les plus récentes, ni les tarifs par gigaoctet les plus bas. Envisagez d’utiliser les scénarios suivants :<br /><ul><li>Si vos applications nécessitent le modèle de déploiement Azure Classic.</li><li>Vos applications sont gourmandes en transactions ou utilisent beaucoup de bande passante de géoréplication, mais ne nécessitent pas une capacité importante. Dans ce cas, le compte universel v1 constitue le choix le plus économique.</li><li>Vous utilisez une version de l’API REST de Stockage Azure antérieure à celle du 14/02/2014 ou une bibliothèque cliente avec une version inférieure à 4.x, et vous ne pouvez pas mettre à niveau votre application.</li></ul> |
| Stockage d'objets blob standard | Objets blob (objets blob de blocs et objets blob d’ajout uniquement) | LRS/GRS/RA-GRS | Gestionnaire de ressources | Dans la mesure du possible, Microsoft recommande d’utiliser des comptes à usage général v2 standard à la place. |

## <a name="next-steps"></a>Étapes suivantes

- [Créez un compte de stockage](storage-account-create.md)
- [Mettre à niveau vers un compte de stockage v2 à usage général](storage-account-upgrade.md)
- [récupérer un compte de stockage supprimé](storage-account-recover.md)