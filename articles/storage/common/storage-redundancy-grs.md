---
title: Stockage géoredondant (GRS) pour favoriser la durabilité entre les régions
titleSuffix: Azure Storage
description: Le stockage géo-redondant (GRS) réplique vos données entre deux régions séparées par des milliers de kilomètres. GRS protège contre les défaillances matérielles dans le data center et contre certains sinistres régionaux.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 6bb93c3fb6599a05978e11ef5fbc179ccfaa9ec2
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614902"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Stockage géoredondant (GRS) : réplication interrégion pour Stockage Azure

[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Stockage géo-redondant avec accès en lecture

Le stockage géo-redondant avec accès en lecture (RA-GRS) optimise la disponibilité de votre compte de stockage. Le stockage RA-GRS fournit un accès en lecture seule aux données dans l’emplacement secondaire, en plus de la géoréplication entre deux régions.

Lorsque vous activez l’accès en lecture seule à vos données dans la région secondaire, vos données sont disponibles sur un point de terminaison secondaire, ainsi que sur le point de terminaison primaire pour votre compte de stockage. Le point de terminaison secondaire est similaire au point de terminaison principal, mais ajoute le suffixe `–secondary` au nom du compte. Par exemple, si votre point de terminaison principal pour le service d’objets blob est `myaccount.blob.core.windows.net`, votre point de terminaison secondaire est `myaccount-secondary.blob.core.windows.net`. Les clés d’accès pour votre compte de stockage sont les mêmes pour le point de terminaison primaire et secondaire.

Certaines considérations à prendre en compte lors de l’utilisation du stockage RA-GRS :

- Votre application doit gérer le point de terminaison avec lequel l’interaction se fait lors de l’utilisation du stockage RA-GRS.
- Étant donné que la réplication asynchrone implique un délai, il est possible que les modifications n’ayant pas encore été répliquées dans la région secondaire soient perdues si des données ne peuvent pas être récupérées à partir de la région primaire.
- Vous pouvez vérifier la propriété **Heure de la dernière synchronisation** de votre compte de stockage. L’**Heure de la dernière synchronisation** est une valeur de date/heure GMT. Toutes les écritures principales effectuées avant l’**Heure de la dernière synchronisation** sont correctement écrites dans l’emplacement secondaire, ce qui signifie qu’elles peuvent être lues à partir de cet emplacement. Les écritures principales effectuées après l’**Heure de la dernière synchronisation** peuvent, ou non, être déjà disponibles pour les lectures. Vous pouvez interroger cette valeur à l’aide de PowerShell, d’Azure CLI ou de l’une des bibliothèques clientes du Stockage Azure. Pour plus d’informations, consultez **Obtention de l’heure de la dernière synchronisation** dans [Conception d’applications à haute disponibilité à l’aide du stockage géoredondant avec accès en lecture](storage-designing-ha-apps-with-ragrs.md#getting-the-last-sync-time).
- Si vous lancez un basculement de compte (préversion) d’un compte GRS ou RA-GRS vers la région secondaire, l’accès en écriture à ce compte est restauré une fois le basculement terminé. Pour plus d’informations, consultez [Récupération d’urgence et basculement de compte de stockage (préversion)](storage-disaster-recovery-guidance.md).
- Le stockage RA-GRS est destiné à des fins de haute disponibilité. Pour obtenir des conseils sur l’évolutivité, consultez la [liste de vérification de performances](storage-performance-checklist.md).
- Pour obtenir des suggestions de conception pour la haute disponibilité à l’aide du stockage géographiquement redondant avec accès en lecture, voir [Concevoir des applications hautement disponibles à l’aide du stockage géographiquement redondant avec accès en lecture](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Que sont l’objectif de point de récupération (RPO) et l’objectif de délai de récupération (RTO) avec GRS ?

**Objectif de point de récupération (RPO) :** dans les stockages GRS et RA-GRS, le service de stockage effectue de façon asynchrone la géoréplication des données de l’emplacement principal vers l’emplacement secondaire. Dans l’éventualité où la région principale deviendrait indisponible, vous pouvez effectuer un basculement de compte (préversion) vers la région secondaire. Lorsque vous effectuez un basculement, les modifications récentes qui n’ont pas encore été géo-répliquées peuvent être perdues. Le nombre de minutes de données potentielles perdues est appelé RPO. Le RPO indique le moment où les données peuvent être récupérées. Stockage Azure comporte généralement un RPO inférieur à 15 minutes, même s’il n’existe actuellement aucun contrat de niveau de service sur la durée de la géoréplication.

**Objectif de délai de récupération (RTO) :** le RTO est une mesure de la durée nécessaire pour effectuer le basculement et replacer le compte de stockage en ligne. Le temps nécessaire à l’exécution du basculement inclut les actions suivantes :

- Le temps restant jusqu’à ce que le client lance le basculement du compte de stockage de la région principale vers la région secondaire.
- Le temps nécessaire à Azure pour exécuter le basculement en modifiant les entrées DNS principales pour qu’elles pointent vers l’emplacement secondaire.

## <a name="paired-regions"></a>Régions jumelées

Lorsque vous créez un compte de stockage, vous sélectionnez la région primaire pour le compte. La région secondaire couplée est déterminée en fonction de la région primaire et n’est pas modifiable. Pour obtenir des informations actuelles sur les régions prises en charge par Azure, consultez [Continuité d’activité et reprise d’activité (BCDR) : régions couplées Azure](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Voir aussi

- [Réplication Azure Storage](storage-redundancy.md)
- [Stockage localement redondant (LRS) : redondance des données à faible coût pour Stockage Azure](storage-redundancy-lrs.md)
- [Stockage redondant interzone (ZRS) : applications Stockage Azure hautement disponibles](storage-redundancy-zrs.md)
