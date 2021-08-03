---
title: Vue d’ensemble du compte de stockage
titleSuffix: Azure Storage
description: En savoir plus sur les différents types de comptes de stockage dans Stockage Azure. Passez en revue les noms des comptes, les niveaux de performance, les niveaux d’accès, la redondance, le chiffrement, les points de terminaison, etc.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c5d4c390110a5b3eef7509508d35c9554f7be984
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110065977"
---
# <a name="storage-account-overview"></a>Vue d’ensemble du compte de stockage

Un compte de stockage Azure contient tous vos objets de données de stockage Azure : objets blob, partages de fichiers, files d’attente, tables et disques. Le compte de stockage fournit pour vos données de stockage Azure un espace de noms unique, accessible de n’importe où dans le monde par le biais du protocole HTTP ou HTTPS. Les données dans votre compte de stockage sont durables et hautement disponibles, sécurisées et massivement évolutives.

Pour plus d’informations sur la création d’un compte de stockage Azure, consultez [Créer un compte de stockage](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Types de compte de stockage

Le stockage Azure offre plusieurs types de comptes de stockage. Chaque type prend en charge différentes fonctionnalités et a son propre modèle tarifaire. Avant de créer un compte de stockage, tenez compte de ces différences pour déterminer l’option qui convient le mieux à vos applications.

Le tableau suivant décrit les types de comptes de stockage recommandés par Microsoft pour la plupart des scénarios. Tous ces modèles utilisent le modèle de déploiement [Azure Resource Manager](../../azure-resource-manager/management/overview.md) .

| Type de compte de stockage | Services de stockage pris en charge | Options de redondance | Utilisation |
|--|--|--|--|
| Usage général v2 Standard | Azure Blob (notamment Data Lake Storage<sup>1</sup>), File d'attente et Stockage Table, Azure Files  | LRS/GRS/RA-GRS<br /><br />ZRS/GZRS/RA-GZRS<sup>2</sup> | Type de compte de stockage standard pour les objets blob, les partages de fichiers, les files d’attente et les tables. Recommandé pour la plupart des scénarios utilisant Stockage Azure. Notez que si vous souhaitez une prise en charge des partages de fichiers NFS dans Azure Files, utilisez le type de compte de partages de fichiers Premium. |
| Objets blob de blocs Premium<sup>3</sup> | Stockage Blob (Data Lake Storage Storage<sup>1</sup> inclus) | LRS<br /><br />ZRS<sup>2</sup> | Type de compte de stockage Premium pour les objets blob de blocs et les objets blob d’ajout. Recommandé pour les scénarios aux taux élevés de transactions, ceux utilisant des objets plus petits ou nécessitant une latence de stockage faible. [En savoir plus sur les exemples de charges de travail.](../blobs/storage-blob-performance-tiers.md#premium-performance) |
| Partages de fichiers Premium<sup>3</sup> | Azure Files | LRS<br /><br />ZRS<sup>2</sup> | Type de compte de stockage Premium pour les partages de fichiers uniquement. Recommandé pour l’entreprise ou des applications de mise à l’échelle hautes performances. Utilisez ce type de compte si vous voulez un compte de stockage qui prend en charge les partages de fichiers SMB et NFS. |
| Objets blob de pages Premium<sup>3</sup> | Objets blob de pages uniquement | LRS | Type de compte de stockage Premium pour les objets blob de pages uniquement. [En savoir plus sur les objets blob de pages et les exemples de cas d’usage.](../blobs/storage-blob-pageblob-overview.md) |

<sup>1</sup> Data Lake Storage est un ensemble de fonctionnalités dédiées à l’analytique du Big Data et basées sur le stockage Blob Azure. Pour plus d’informations, consultez [Introduction à Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) et [Créer un compte de stockage à utiliser avec Data Lake Storage Gen2](../blobs/create-data-lake-storage-account.md).

<sup>2</sup> Le stockage redondant interzone (ZRS) et le stockage géo-redondant interzone (GZRS/RA-GZRS) sont disponibles uniquement pour les comptes à usage général v2, d’objets blob de blocs premium et de partages de fichiers premium standard dans certaines régions. Pour plus d’informations, consultez [Redondance de Stockage Azure](storage-redundancy.md).

<sup>3</sup> Les comptes de stockage d’un niveau de performance supérieur utilisent des disques SSD qui offrent une faible latence et un débit élevé.

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

## <a name="migrate-a-storage-account"></a>Migrer un compte de stockage

Le tableau suivant résume et propose des conseils sur le déplacement, la mise à niveau ou la migration d’un compte de stockage :

| Scénario de migration | Détails |
|--|--|
| Déplacer un compte de stockage vers un autre abonnement | Azure Resource Manager propose plusieurs options pour déplacer une ressource vers un autre abonnement. Pour plus d’informations, consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Déplacer un compte de stockage vers un autre groupe de ressources | Azure Resource Manager propose plusieurs options pour déplacer une ressource vers un autre groupe de ressources. Pour plus d’informations, consultez la page [Déplacer des ressources vers un nouveau groupe de ressources ou un abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Déplacer un compte de stockage vers une autre région | Pour déplacer un compte de stockage, créez une copie de votre compte de stockage dans une autre région. Utilisez ensuite AzCopy ou un autre outil de votre choix pour déplacer vos données vers ce compte. Pour plus d’informations, consultez [Déplacer un compte Stockage Azure vers une autre région](storage-account-move.md). |
| Mettre à niveau vers un compte de stockage v2 à usage général | Vous pouvez mettre à niveau un compte de stockage v1 universel ou un compte de stockage Blob vers un compte v2 universel. Notez que cette action ne peut pas être annulée. Pour obtenir plus d’informations, consultez [Mettre à niveau vers un compte de stockage v2 à usage général](storage-account-upgrade.md). |
| Migrer un compte de stockage classique vers Azure Resource Manager | Le modèle de déploiement Azure Resource Manager est plus performant que le modèle de déploiement classique en termes de fonctionnalités, de scalabilité et de sécurité. Pour plus d’informations sur la migration d’un compte de stockage classique vers Azure Resource Manager, consultez la section « Migration de comptes de stockage » dans [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts). |

## <a name="transfer-data-into-a-storage-account"></a>Transférer des données vers un compte de stockage

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

| Type de compte de stockage hérité | Services de stockage pris en charge | Options de redondance | Modèle de déploiement | Utilisation |
|--|--|--|--|--|
| Usage général v1 standard | Azure Blob, File d'attente et Stockage Table, Azure Files | LRS/GRS/RA-GRS | Resource Manager, Classic | Les comptes à usage général v1 ne proposent pas nécessairement les fonctionnalités les plus récentes, ni les tarifs par gigaoctet les plus bas. Envisagez d’utiliser les scénarios suivants :<br /><ul><li>Si vos applications nécessitent le [modèle de déploiement Azure Classic](../../azure-portal/supportability/classic-deployment-model-quota-increase-requests.md).</li><li>Vos applications sont gourmandes en transactions ou utilisent beaucoup de bande passante de géoréplication, mais ne nécessitent pas une capacité importante. Dans ce cas, le compte universel v1 constitue le choix le plus économique.</li><li>Vous utilisez une version de l’API REST de Stockage Azure antérieure à celle du 14/02/2014 ou une bibliothèque cliente avec une version inférieure à 4.x, et vous ne pouvez pas mettre à niveau votre application.</li></ul> |
| Stockage d'objets blob standard | Stockage d’objets blob (objets blob de blocs et objets blob d’ajout uniquement) | LRS/GRS/RA-GRS | Gestionnaire de ressources | Dans la mesure du possible, Microsoft recommande d’utiliser des comptes à usage général v2 standard à la place. |

## <a name="next-steps"></a>Étapes suivantes

- [Créez un compte de stockage](storage-account-create.md)
- [Mettre à niveau vers un compte de stockage v2 à usage général](storage-account-upgrade.md)
- [récupérer un compte de stockage supprimé](storage-account-recover.md)
