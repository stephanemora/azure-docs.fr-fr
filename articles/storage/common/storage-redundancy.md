---
title: Réplication de données dans le stockage Azure | Microsoft Docs
description: Les données de votre compte Stockage Microsoft Azure sont répliquées à des fins de durabilité et de haute disponibilité. Les options de réplication incluent le stockage localement redondant (LRS), le stockage redondant dans une zone (ZRS), le stockage géo-redondant (GRS) et le stockage géo-redondant avec accès en lecture (RA-GRS).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: bdb9bfaa85f526af0c5e42294a75664fa7137849
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="azure-storage-replication"></a>Réplication Azure Storage

Les données de votre compte de stockage Microsoft Azure sont toujours répliquées pour en garantir la durabilité et la haute disponibilité. La réplication Stockage Azure copie vos données afin qu’elles soient protégées contre les événements planifiés ou non, notamment les défaillances matérielles temporaires, les pannes de réseau ou de courant, les catastrophes naturelles massives, etc. Vous pouvez choisir de répliquer vos données dans le même centre de données, dans des centres de données zonaux d’une même région, voire entre des régions.

La réplication garantit que votre compte de stockage répond aux exigences du [contrat de niveau de service (SLA) pour le stockage](https://azure.microsoft.com/support/legal/sla/storage/) même en cas de panne. Pour plus d’informations sur les garanties de durabilité et de disponibilité du stockage Azure, consultez le contrat de niveau de service.

## <a name="choosing-a-replication-option"></a>Choix d’une option de réplication

Lorsque vous créez un compte de stockage, vous pouvez sélectionner une des options de réplication suivantes :

* [Stockage localement redondant (LRS)](storage-redundancy-lrs.md)
* [Stockage redondant dans une zone (ZRS)](storage-redundancy-zrs.md)
* [Stockage géo-redondant (GRS)](storage-redundancy-grs.md)
* [Stockage géo-redondant avec accès en lecture (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

Le tableau suivant fournit une brève vue d’ensemble de l’étendue de la durabilité et de la disponibilité que chaque stratégie de réplication vous fournit pour un type d’événement donné (ou un événement d’impact similaire).

| Scénario | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Indisponibilité des nœuds dans un centre de données |OUI |OUI |OUI |OUI
| Indisponibilité d’un centre de données complet (zonal ou non) |Non  |OUI |OUI |OUI |
| Panne à l’échelle d’une région |Non  |Non  |OUI |OUI |
| Accès en lecture aux données (dans une région distante, géorépliquée) en cas d’indisponibilité à l’échelle de la région |Non  |Non  |Non  |OUI |
| Conçu pour fournir une certaine durabilité des objets sur une année donnée |Au moins 99,999999999 % (11 chiffres 9)|Au moins 99,9999999999 % (12 chiffres 9)|Au moins 99,99999999999999 % (16 chiffres 9)|Au moins 99,99999999999999 % (16 chiffres 9)|
| Disponible dans les types de compte de stockage ___ |GPv1, GPv2, blob |GPv2 |GPv1, GPv2, blob |GPv1, GPv2, blob

Consultez [Présentation de la tarification Stockage Azure ](https://azure.microsoft.com/pricing/details/storage/) pour connaître les informations de tarification des différentes options de redondance.

> [!NOTE]
> Stockage Premium prend en charge uniquement un stockage localement redondant (LRS). Pour plus d’informations sur Stockage Premium, consultez [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../../virtual-machines/windows/premium-storage.md).

## <a name="changing-replication-strategy"></a>Modification de la stratégie de réplication
Vous pouvez modifier la stratégie de réplication de votre compte de stockage à l’aide du [portail Azure](https://portal.azure.com/), [d’Azure PowerShell](storage-powershell-guide-full.md), [d’Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) ou de l’une des nombreuses [bibliothèques clientes Azure](https://docs.microsoft.com/en-us/azure/index?view=azure-dotnet#pivot=sdkstools). La modification du type de réplication de votre compte de stockage n’entraîne pas de temps d’arrêt.

   > [!NOTE]
   > Actuellement, vous ne pouvez utiliser ni le portail ni une API pour convertir votre compte en stockage ZRS. Toutefois, nous prévoyons d’assurer la prise en charge de la migration du stockage LRS, GRS et RA-GRS vers ZRS une fois que ce stockage ZRS sera généralement disponible. Pour plus d’informations, consultez [Stockage redondant dans une zone (ZRS)](storage-redundancy-zrs.md).
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>La modification de la stratégie de réplication de mon compte implique-t-elle des coûts ?
Cela dépend de votre chemin de conversion. Voici le classement des offres de redondance de la moins coûteuse à la plus chère : LRS, ZRS, GRS et RA-GRS. Par exemple, une migration *à partir de* LRS implique des frais supplémentaires, car vous passez à un niveau de redondance plus sophistiqué. Une migration *vers* GRS ou RA-GRS entraîne des frais de sortie de la bande passante, car vos données (dans la région primaire) sont répliquées vers la région secondaire distante. Il s’agit de frais uniques dus lors de l’installation initiale. Une fois les données copiées, aucuns frais supplémentaires de conversion ne sont générés. Vous serez facturé uniquement pour répliquer des données nouvelles ou mises à jour vers des données existantes. Pour plus d’informations sur les coûts de bande passante, consultez la [page de tarification de Stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Si vous remplacez un stockage GRS par un stockage LRS, il n’existe aucun coût supplémentaire, mais vos données répliquées sont supprimées de l’emplacement secondaire.

## <a name="see-also"></a>Voir aussi

- [Stockage localement redondant (LRS) : redondance des données à faible coût pour le stockage Azure](storage-redundancy-lrs.md)
- [Stockage redondant dans une zone (ZRS) : applications de stockage Azure hautement disponibles](storage-redundancy-zrs.md)
- [Stockage géoredondant (GRS) : réplication interrégion pour le stockage Azure](storage-redundancy-grs.md)
- [Objectifs de performance et d’extensibilité du Stockage Azure](storage-scalability-targets.md)
- [Conception d’applications hautement disponibles à l’aide du stockage RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Options de redondance et stockage géo-redondant avec accès en lecture de Stockage Microsoft Azure ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [Document SOSP - Stockage Azure : service de stockage cloud à haute disponibilité et à cohérence forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
