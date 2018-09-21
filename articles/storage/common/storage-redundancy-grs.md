---
title: Stockage géo-redondant (GRS) pour une durabilité interrégion du stockage Azure | Microsoft Docs
description: Le stockage géo-redondant (GRS) réplique vos données entre deux régions séparées par des milliers de kilomètres. GRS protège contre les défaillances matérielles dans le data center et contre certains sinistres régionaux.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 03/20/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: eee9998280f2105fee5144b520bc8000b086f941
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603923"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Stockage géoredondant (GRS) : réplication interrégion pour le stockage Azure
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Stockage géo-redondant avec accès en lecture
Le stockage géo-redondant avec accès en lecture (RA-GRS) optimise la disponibilité de votre compte de stockage. Le stockage RA-GRS fournit un accès en lecture seule aux données dans l’emplacement secondaire, en plus de la géoréplication entre deux régions.

Lorsque vous activez l’accès en lecture seule à vos données dans la région secondaire, vos données sont disponibles sur un point de terminaison secondaire, ainsi que sur le point de terminaison primaire pour votre compte de stockage. Le point de terminaison secondaire est similaire au point de terminaison principal, mais ajoute le suffixe `–secondary` au nom du compte. Par exemple, si votre point de terminaison principal pour le service d’objets blob est `myaccount.blob.core.windows.net`, votre point de terminaison secondaire est `myaccount-secondary.blob.core.windows.net`. Les clés d’accès pour votre compte de stockage sont les mêmes pour le point de terminaison primaire et secondaire.

Certaines considérations à prendre en compte lors de l’utilisation du stockage RA-GRS :

* Votre application doit gérer le point de terminaison avec lequel l’interaction se fait lors de l’utilisation du stockage RA-GRS.
* Étant donné que la réplication asynchrone implique un délai, il est possible que les modifications n’ayant pas encore été répliquées dans la région secondaire soient perdues si des données ne peuvent pas être récupérées à partir de la région primaire, par exemple en cas de sinistre régional.
* Vous pouvez vérifier l’heure de dernière synchronisation de votre compte de stockage. La dernière heure de synchronisation correspond à une valeur de date/heure GMT. Toutes les écritures principales effectuées avant l’heure de la dernière synchronisation sont correctement écrites à l’emplacement secondaire, ce qui signifie qu’elles peuvent être lues à partir de l’emplacement secondaire. Les écritures principales effectuées après l’heure de la dernière synchronisation peuvent ou non être encore disponibles pour les lectures. Vous pouvez interroger cette valeur à l’aide du [portail Azure](https://portal.azure.com/), d’[Azure PowerShell](storage-powershell-guide-full.md) ou de l’une des bibliothèques clientes Stockage Azure.
* Si Microsoft initie un basculement vers la région secondaire, vous obtiendrez un accès en lecture et écriture à ces données une fois le basculement effectué. Pour plus d’informations, consultez [Conseils sur la récupération d’urgence](storage-disaster-recovery-guidance.md).
* Pour plus d’informations sur le basculement vers la région secondaire, voir [Que faire en cas d’interruption du Stockage Azure](storage-disaster-recovery-guidance.md).
* Le stockage RA-GRS est destiné à des fins de haute disponibilité. Pour obtenir des conseils sur l’évolutivité, consultez la [liste de vérification de performances](storage-performance-checklist.md).
* Pour obtenir des suggestions de conception pour la haute disponibilité à l’aide du stockage géographiquement redondant avec accès en lecture, voir [Concevoir des applications hautement disponibles à l’aide du stockage géographiquement redondant avec accès en lecture](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Que sont l’objectif de point de récupération (RPO) et l’objectif de délai de récupération (RTO) avec GRS ?
**Objectif de point de récupération (RPO) :** dans le stockage géoredondant et le stockage géographiquement redondant avec accès en lecture, le service de stockage effectue de façon asynchrone la géoréplication des données de l’emplacement primaire vers l’emplacement secondaire. En cas de sinistre régional majeur dans la région principale, Microsoft effectue un basculement vers la région secondaire. Si un basculement se produit, les modifications récentes qui n’ont pas encore été géo-répliquées peuvent être perdues. Le nombre de minutes de perte de données potentielle correspond au point RPO, et il indique le point dans le temps auquel les données peuvent être récupérées. Stockage Azure comporte généralement un point RPO inférieur à 15 minutes, même s’il n’existe actuellement aucun contrat de niveau de service sur la durée de la géoréplication.

**Objectif de délai de récupération (RTO, Recovery Time Objective) :** le RTO est une mesure de la durée nécessaire pour effectuer le basculement et replacer le compte de stockage en ligne. Le temps nécessaire à l’exécution du basculement inclut les actions suivantes :

   * Le temps nécessaire à Microsoft pour déterminer si les données peuvent être récupérées à l’emplacement principal ou si un basculement est nécessaire.
   * Le temps nécessaire à l’exécution du basculement du compte de stockage en modifiant les entrées DNS principales pour pointer vers l’emplacement secondaire.

   Microsoft prend la responsabilité de conserver vos données de manière rigoureuse. Si la récupération des données dans la région principale est possible, Microsoft retarde le basculement et se concentre sur la récupération de vos données. 

## <a name="paired-regions"></a>Régions jumelées 
Lorsque vous créez un compte de stockage, vous sélectionnez la région primaire pour le compte. La région secondaire couplée est déterminée en fonction de la région primaire ; elle n’est pas modifiable. Pour plus d’informations sur les régions prises en charge par Azure, voir [Continuité d’activité et reprise d’activité (BCDR) : régions couplées Azure](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Voir aussi
- [Réplication Azure Storage](storage-redundancy.md)
- [Stockage localement redondant (LRS) : redondance des données à faible coût pour le stockage Azure](storage-redundancy-lrs.md)
- [Stockage redondant dans une zone (ZRS) : applications de stockage Azure hautement disponibles](storage-redundancy-zrs.md)