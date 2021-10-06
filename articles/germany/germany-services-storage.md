---
title: Services de stockage Azure Allemagne | Microsoft Docs
description: Cette rubrique fournit une comparaison des services de stockage pour Azure Allemagne. Vous y trouverez également d’autres informations pertinentes.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 840ac16914196a238bcf8fdaaa518799dcb104e9
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275028"
---
# <a name="azure-germany-storage-services"></a>Services de stockage Azure Allemagne

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="storage"></a>Stockage
Pour plus d’informations sur le stockage Azure et son utilisation, consultez la [documentation générale sur le stockage](../storage/index.yml).

Les données stockées dans le stockage Azure sont répliquées pour garantir une haute disponibilité. Pour assurer le stockage géoredondant et le stockage géoredondant avec accès en lecture, Azure réplique les données entre des *régions associées*. Pour Azure Allemagne, les régions associées sont les suivantes :

| Région primaire | Région secondaire (associée) |
| --- | --- |
| Centre de l’Allemagne | Nord-Est de l’Allemagne |
| Nord-Est de l’Allemagne | Centre de l’Allemagne |

Les données répliquées restent stockées en Allemagne. Les régions primaire et secondaire sont associées pour maintenir la distance nécessaire entre les centres de données et ainsi garantir la disponibilité en cas d’interruption ou de sinistre. Pour bénéficier d’un stockage géoredondant à haute disponibilité, sélectionnez le stockage géoredondant (GRS) ou le stockage géoredondant avec accès en lecture (RA-GRS) au moment de la création d’un compte de stockage.  

La fonctionnalité Storage Service Encryption protège les données au repos dans les comptes de stockage Azure. Si vous activez cette fonctionnalité, Azure chiffre automatiquement les données avant de les rendre persistantes dans le stockage. Les données sont chiffrées à l’aide du chiffrement AES 256 bits. Storage Service Encryption prend en charge le chiffrement des objets blob de blocs, des objets blob d’ajout et des objets blob de pages.

### <a name="storage-service-availability-by-azure-germany-region"></a>Disponibilité des services de stockage par région Azure Allemagne

| Service | Centre de l’Allemagne | Nord-Est de l’Allemagne |
| --- | --- | --- |
| [Stockage Blob](../storage/common/storage-introduction.md#blob-storage) |GA |GA |
| [Azure Files](../storage/common/storage-introduction.md#azure-files) | GA | GA |
| [Stockage Table](../storage/common/storage-introduction.md#table-storage) |GA  |GA |
| [Stockage File d’attente](../storage/common/storage-introduction.md#queue-storage) |GA | GA |
| [Stockage Blob chaud/froid](../storage/blobs/access-tiers-overview.md) |GA |GA |
| [Storage Service Encryption](../storage/common/storage-service-encryption.md) |GA |GA |
| Importer/Exporter |N/D |N/D |
| StorSimple |N/D |N/D |

### <a name="variations"></a>Variantes
Les URL pour les comptes de stockage dans Azure Allemagne sont différents de ceux dans Azure global :

| Type de service | Azure global | Azure Allemagne |
| --- | --- | --- |
| Stockage d’objets blob | *.blob.core.windows.net | *.blob.core.cloudapi.de |
| Azure Files | *.file.core.windows.net | *.file.core.cloudapi.de | 
| Stockage de files d'attente | *.queue.core.windows.net | *.queue.core.cloudapi.de |
| Stockage de tables | *.table.core.windows.net | *.table.core.cloudapi.de |

> [!NOTE]
> Tous vos scripts et tout votre code doivent prendre en compte les points de terminaison appropriés. Pour plus d’informations, consultez [Configuration des chaînes de connexion Stockage Azure](../storage/common/storage-configure-connection-string.md). 
>
>

Pour plus d’informations sur les API, consultez le [constructeur CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.-ctor).

Le suffixe de point de terminaison à utiliser dans ces surcharges est *core.cloudapi.de*.

> [!NOTE]
> Si l’erreur 53 (« Le chemin réseau est introuvable ») est retournée pendant que vous êtes en train de [monter le partage de fichiers](../storage/files/storage-dotnet-how-to-use-files.md), il se peut qu’un pare-feu bloque le port de sortie. Essayez de monter le partage de fichiers sur une machine virtuelle qui se trouve dans le même abonnement Azure que le compte de stockage.
>
>


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, abonnez-vous au [blog Azure Allemagne](/archive/blogs/azuregermany/).