---
title: Redondance de données dans le stockage Azure | Microsoft Docs
description: Les données de votre compte Stockage Microsoft Azure sont répliquées à des fins de durabilité et de haute disponibilité. Les options de redondance incluent le stockage localement redondant (LRS), le stockage redondant interzone (ZRS), le stockage géoredondant (GRS) et le stockage géoredondant avec accès en lecture (RA-GRS).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: b4b9e913363938f98999c6a769ba83efbd625b69
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798319"
---
# <a name="azure-storage-redundancy"></a>Redondance de Stockage Azure

Les données de votre compte de stockage Microsoft Azure sont toujours répliquées pour en garantir la durabilité et la haute disponibilité. Stockage Azure copie vos données afin qu’elles soient protégées contre les événements planifiés ou non, notamment les défaillances matérielles temporaires, les pannes de réseau ou de courant et les catastrophes naturelles massives. Vous pouvez choisir de répliquer vos données dans le même centre de données, dans des centres de données zonaux d’une même région ou entre des régions géographiques différentes.

La réplication garantit que votre compte de stockage répond aux exigences du [contrat de niveau de service (SLA) pour le stockage](https://azure.microsoft.com/support/legal/sla/storage/) même en cas de panne. Pour plus d’informations sur les garanties de durabilité et de disponibilité du stockage Azure, consultez le contrat de niveau de service.

Le service Stockage Azure vérifie régulièrement l’intégrité des données stockées à l’aide de contrôles de redondance cycliques (CRC). Si une altération des données est détectée, ces données sont réparées à l’aide de données redondantes. Le service Stockage Azure calcule également des sommes de contrôle sur tout le trafic réseau afin de détecter une altération des paquets de données lors du stockage ou de la récupération des données.

## <a name="choosing-a-redundancy-option"></a>Choix d’une option de redondance

Lorsque vous créez un compte de stockage, vous pouvez sélectionner une des options de redondance suivantes :

* [Stockage localement redondant (LRS)](storage-redundancy-lrs.md)
* [Stockage redondant interzone (ZRS)](storage-redundancy-zrs.md)
* [Stockage géo-redondant (GRS)](storage-redundancy-grs.md)
* [Stockage géo-redondant avec accès en lecture (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

Le tableau suivant fournit une brève vue d’ensemble de l’étendue de la durabilité et de la disponibilité que chaque stratégie de réplication vous fournit pour un type d’événement donné (ou un événement d’impact similaire).

| Scénario                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Indisponibilité des nœuds dans un centre de données                                                                 | OUI                             | OUI                              | OUI                                  | OUI                                  |
| Indisponibilité d’un centre de données complet (zonal ou non)                                           | Non                              | OUI                              | OUI                                  | OUI                                  |
| Panne à l’échelle d’une région                                                                                     | Non                              | Non                               | OUI                                  | OUI                                  |
| Accès en lecture aux données (dans une région distante, géorépliquée) en cas d’indisponibilité à l’échelle de la région | Non                              | Non                               | Non                                   | OUI                                  |
| Conçu pour assurer une durabilité des objets \_\_ sur une année donnée                                          | Au moins 99,999999999 % (11 chiffres 9) | Au moins 99,9999999999 % (12 chiffres 9) | Au moins 99,99999999999999 % (16 chiffres 9) | Au moins 99,99999999999999 % (16 chiffres 9) |
| Types de compte de stockage pris en charge                                                                   | GPv2, GPv1, Blob                | GPv2                             | GPv2, GPv1, Blob                     | GPv2, GPv1, Blob                     |
| Contrat SLA de disponibilité pour les requêtes de lecture | Au moins 99,9 % (99 % pour le niveau d’accès froid) | Au moins 99,9 % (99 % pour le niveau d’accès froid) | Au moins 99,9 % (99 % pour le niveau d’accès froid) | Au moins 99,99 % (99,9 % pour le niveau d’accès froid) |
| Contrat SLA de disponibilité pour les requêtes d’écriture | Au moins 99,9 % (99 % pour le niveau d’accès froid) | Au moins 99,9 % (99 % pour le niveau d’accès froid) | Au moins 99,9 % (99 % pour le niveau d’accès froid) | Au moins 99,9 % (99 % pour le niveau d’accès froid) |

Toutes les données de votre compte de stockage sont répliquées, à savoir, les objets blob de blocs, les objets blob d’ajout, les objets blob de pages, les files d’attente, les tables et les fichiers. Tous les types de comptes de stockage sont répliqués, bien que la stockage redondant interzone requière un compte de stockage v2 universel.

Pour obtenir des informations sur les prix des différentes options de redondance, consultez [Prix de Stockage Azure](https://azure.microsoft.com/pricing/details/storage/). 

Pour obtenir des informations sur les garanties de Stockage Azure en matière de durabilité et de disponibilité, consultez le [contrat de niveau de service de Stockage Azure](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Le Stockage Premium Azure prend en charge uniquement un stockage localement redondant (LRS).

## <a name="changing-replication-strategy"></a>Modification de la stratégie de réplication
Vous pouvez modifier la stratégie de réplication de votre compte de stockage à l’aide du [portail Azure](https://portal.azure.com/), [d’Azure PowerShell](storage-powershell-guide-full.md), [d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou de l’une des [bibliothèques clientes Azure](https://docs.microsoft.com/azure/index#pivot=sdkstools). La modification du type de réplication de votre compte de stockage n’entraîne pas de temps d’arrêt.

   > [!NOTE]
   > Actuellement, vous ne pouvez utiliser ni le portail ni une API pour convertir votre compte en stockage ZRS. Si vous souhaitez convertir la réplication de votre compte en stockage redondant interzone (ZRS), consultez [Stockage redondant interzone (ZRS)](storage-redundancy-zrs.md) pour plus d’informations.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>La modification de la stratégie de réplication de mon compte implique-t-elle des coûts ?
Cela dépend de votre chemin de conversion. Voici le classement des offres de redondance de la moins coûteuse à la plus chère : LRS, ZRS, GRS et RA-GRS. Par exemple, une migration *à partir de* LRS implique des frais supplémentaires, car vous passez à un niveau de redondance plus sophistiqué. Une migration *vers* GRS ou RA-GRS entraîne des frais de sortie de la bande passante, car vos données (dans la région primaire) sont répliquées vers la région secondaire distante. Il s’agit de frais uniques dus lors de l’installation initiale. Une fois les données copiées, aucuns frais supplémentaires de conversion ne sont générés. Vous serez facturé uniquement pour répliquer des données nouvelles ou mises à jour vers des données existantes. Pour plus d’informations sur les coûts de bande passante, consultez la [page de tarification de Stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Si vous convertissez votre compte de stockage géoredondant en compte de stockage localement redondant, aucun coût supplémentaire ne vous est facturé, mais vos données répliquées sont supprimées de l’emplacement secondaire.

Si vous convertissez votre compte de stockage géographiquement redondant avec accès en lecture en compte de stockage géoredondant ou localement redondant, ce compte est facturé comme stockage géographiquement redondant avec accès en lecture pendant une période supplémentaire de 30 jours après la date de sa conversion.

## <a name="see-also"></a>Voir aussi

- [Stockage localement redondant (LRS) : redondance des données à faible coût pour Stockage Azure](storage-redundancy-lrs.md)
- [Stockage redondant interzone (ZRS) : applications Stockage Azure hautement disponibles](storage-redundancy-zrs.md)
- [Stockage géo-redondant (GRS) : réplication interrégion pour Stockage Azure](storage-redundancy-grs.md)
- [Objectifs de performance et d’extensibilité du Stockage Azure](storage-scalability-targets.md)
- [Conception d’applications hautement disponibles à l’aide du stockage RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Options de redondance et stockage géo-redondant avec accès en lecture de Stockage Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [Document SOSP - Stockage Azure : service de stockage cloud hautement disponible à cohérence forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
