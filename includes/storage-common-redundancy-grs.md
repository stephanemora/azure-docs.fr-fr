---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: f5b6e0e74bbab33b9ae6fbacca5c55ea434d3e41
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399955"
---
Le stockage géoredondant (GRS) est conçu pour fournir au moins 99,99999999999999 % de durabilité des objets sur une année données en répliquant vos données vers une région secondaire se situant à des centaines de kilomètres de la région primaire. Si le GRS est activé pour votre compte de stockage, vos données restent durables, même en cas de panne régionale totale ou d’incident empêchant la récupération depuis la région primaire.

Si vous optez pour le stockage géoredondant, vous avez le choix entre deux options liées :

* Le stockage géoredondant réplique les données vers un autre centre de données dans une région secondaire, mais elles ne peuvent être lues que si Microsoft initie un basculement de la région primaire vers la région secondaire. 
* Le stockage géoredondant avec accès en lecture (RA-GRS) s’appuie sur le stockage géoredondant. Il réplique les données vers un autre centre de données dans une région secondaire, et offre également la possibilité de les lire dans la région secondaire, que Microsoft initie ou non un basculement de la région primaire vers la région secondaire. 

Dans le cas d’un compte de stockage GRS ou RA-GRS, toutes les données sont d’abord répliquées avec le stockage localement redondant (LRS). Une mise à jour est au préalable validée dans la région primaire et répliquée avec le stockage localement redondant. Elle est ensuite répliquée de manière asynchrone dans la région secondaire avec le stockage géoredondant. Lors de l’écriture des données dans l’emplacement secondaire, elles sont également répliquées au sein de cet emplacement avec le stockage localement redondant. 

Les régions primaire et secondaire gèrent les réplicas dans des domaines d’erreur et de mise à niveau distincts, au sein d’une unité d’échelle de stockage. Cette unité représente l’unité de réplication de base au sein du centre de données. La réplication à ce niveau est assurée par le stockage localement redondant ; pour plus d’informations, voir [Stockage localement redondant (LRS) : redondance des données à faible coût pour le Stockage Azure](../articles/storage/common/storage-redundancy-lrs.md).

Gardez ces points à l’esprit au moment de choisir une option de réplication :

* Le stockage redondant dans une zone (ZRS), qui assure la haute disponibilité avec réplication synchrone, peut représenter dans certains scénarios un meilleur choix que le stockage géoredondant et le stockage géographiquement redondant avec accès en lecture. Pour plus d’informations sur le stockage redondant dans une zone, voir [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* La réplication asynchrone impliquant un délai, il est possible que, en cas de sinistre régional, les modifications n’ayant pas encore été répliquées dans la région secondaire soient perdues si les données ne sont pas récupérables à partir de la région primaire.
* Avec le stockage géoredondant, le réplica n’est disponible que si Microsoft lance le basculement vers la région secondaire. Si Microsoft initie un basculement vers la région secondaire, vous obtiendrez un accès en lecture et écriture à ces données une fois le basculement effectué. Pour plus d’informations, consultez [Conseils sur la récupération d’urgence](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Si votre application doit lire les données de la région secondaire, activez le stockage géographiquement redondant avec accès en lecture.

## <a name="read-access-geo-redundant-storage"></a>Stockage géo-redondant avec accès en lecture

Le stockage géo-redondant avec accès en lecture (RA-GRS) optimise la disponibilité de votre compte de stockage. Le stockage RA-GRS fournit un accès en lecture seule aux données dans l’emplacement secondaire, en plus de la géoréplication entre deux régions.

Lorsque vous activez l’accès en lecture seule à vos données dans la région secondaire, vos données sont disponibles sur un point de terminaison secondaire, ainsi que sur le point de terminaison primaire pour votre compte de stockage. Le point de terminaison secondaire est similaire au point de terminaison principal, mais ajoute le suffixe `–secondary` au nom du compte. Par exemple, si votre point de terminaison principal pour le service d’objets blob est `myaccount.blob.core.windows.net`, votre point de terminaison secondaire est `myaccount-secondary.blob.core.windows.net`. Les clés d’accès pour votre compte de stockage sont les mêmes pour le point de terminaison primaire et secondaire.

Certaines considérations à prendre en compte lors de l’utilisation du stockage RA-GRS :

* Votre application doit gérer le point de terminaison avec lequel l’interaction se fait lors de l’utilisation du stockage RA-GRS.
* Étant donné que la réplication asynchrone implique un délai, il est possible que les modifications n’ayant pas encore été répliquées dans la région secondaire soient perdues si des données ne peuvent pas être récupérées à partir de la région primaire, par exemple en cas de sinistre régional.
* Vous pouvez vérifier l’heure de dernière synchronisation de votre compte de stockage. La dernière heure de synchronisation correspond à une valeur de date/heure GMT. Toutes les écritures principales effectuées avant l’heure de la dernière synchronisation sont correctement écrites à l’emplacement secondaire, ce qui signifie qu’elles peuvent être lues à partir de l’emplacement secondaire. Les écritures principales effectuées après l’heure de la dernière synchronisation peuvent ou non être encore disponibles pour les lectures. Vous pouvez interroger cette valeur à l’aide du [portail Azure](https://portal.azure.com/), d’[Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md) ou de l’une des bibliothèques clientes Stockage Azure.
* Si Microsoft initie un basculement vers la région secondaire, vous obtiendrez un accès en lecture et écriture à ces données une fois le basculement effectué. Pour plus d’informations, consultez [Conseils sur la récupération d’urgence](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Pour plus d’informations sur le basculement vers la région secondaire, voir [Que faire en cas d’interruption du Stockage Azure](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Le stockage RA-GRS est destiné à des fins de haute disponibilité. Pour obtenir des conseils sur l’évolutivité, consultez la [liste de vérification de performances](../articles/storage/common/storage-performance-checklist.md).
* Pour obtenir des suggestions de conception pour la haute disponibilité à l’aide du stockage géographiquement redondant avec accès en lecture, voir [Concevoir des applications hautement disponibles à l’aide du stockage géographiquement redondant avec accès en lecture](../articles/storage/common/storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Que sont l’objectif de point de récupération (RPO) et l’objectif de délai de récupération (RTO) avec GRS ?
**Objectif de point de récupération (RPO) :** dans le stockage géoredondant et le stockage géographiquement redondant avec accès en lecture, le service de stockage effectue de façon asynchrone la géoréplication des données de l’emplacement primaire vers l’emplacement secondaire. En cas de sinistre régional majeur dans la région principale, Microsoft effectue un basculement vers la région secondaire. Si un basculement se produit, les modifications récentes qui n’ont pas encore été géo-répliquées peuvent être perdues. Le nombre de minutes de perte de données potentielle correspond au point RPO, et il indique le point dans le temps auquel les données peuvent être récupérées. Stockage Azure comporte généralement un point RPO inférieur à 15 minutes, même s’il n’existe actuellement aucun contrat de niveau de service sur la durée de la géoréplication.

**Objectif de délai de récupération (RTO, Recovery Time Objective) :** le RTO est une mesure de la durée nécessaire pour effectuer le basculement et replacer le compte de stockage en ligne. Le temps nécessaire à l’exécution du basculement inclut les actions suivantes :

   * Le temps nécessaire à Microsoft pour déterminer si les données peuvent être récupérées à l’emplacement principal ou si un basculement est nécessaire.
   * Le temps nécessaire à l’exécution du basculement du compte de stockage en modifiant les entrées DNS principales pour pointer vers l’emplacement secondaire.

   Microsoft prend la responsabilité de conserver vos données de manière rigoureuse. Si la récupération des données dans la région principale est possible, Microsoft retarde le basculement et se concentre sur la récupération de vos données. Une version future du service vous permettra de déclencher un basculement au niveau d’un compte afin que vous puissiez contrôler vous-même le RTO.

## <a name="paired-regions"></a>Régions jumelées 

Lorsque vous créez un compte de stockage, vous sélectionnez la région primaire pour le compte. La région secondaire couplée est déterminée en fonction de la région primaire ; elle n’est pas modifiable. Pour plus d’informations sur les régions prises en charge par Azure, voir [Continuité d’activité et reprise d’activité (BCDR) : régions couplées Azure](../articles/best-practices-availability-paired-regions.md).
