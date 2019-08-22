---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 28b53864abc37a880a9573cc9657231aff862336
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029782"
---
Le stockage géoredondant (GRS) est conçu pour fournir au moins 99,99999999999999 % de durabilité des objets sur une année données en répliquant vos données vers une région secondaire se situant à des centaines de kilomètres de la région primaire. Si le GRS est activé pour votre compte de stockage, vos données restent durables, même en cas de panne régionale totale ou d’incident empêchant la récupération depuis la région primaire.

Si vous optez pour le stockage géoredondant, vous avez le choix entre deux options liées :

* Le stockage géoredondant réplique les données vers un autre centre de données dans une région secondaire, mais elles ne peuvent être lues que si Microsoft initie un basculement de la région primaire vers la région secondaire.
* Le stockage géoredondant avec accès en lecture (RA-GRS) s’appuie sur le stockage géoredondant. Il réplique les données vers un autre centre de données dans une région secondaire, et offre également la possibilité de les lire dans la région secondaire, que Microsoft initie ou non un basculement de la région primaire vers la région secondaire.

Dans le cas d’un compte de stockage GRS ou RA-GRS, toutes les données sont d’abord répliquées avec le stockage localement redondant (LRS). Une mise à jour est au préalable validée dans la région primaire et répliquée avec le stockage localement redondant. Elle est ensuite répliquée de manière asynchrone dans la région secondaire avec le stockage géoredondant. Lors de l’écriture des données dans l’emplacement secondaire, celles-ci sont également répliquées au sein de cet emplacement avec le stockage localement redondant.

Les régions primaire et secondaire gèrent les réplicas dans des domaines d’erreur et de mise à niveau distincts, au sein d’une unité d’échelle de stockage. Cette unité représente l’unité de réplication de base au sein du centre de données. La réplication à ce niveau est assurée par le stockage localement redondant ; pour en savoir plus, voir [Stockage localement redondant (LRS) : redondance des données à faible coût pour le stockage Azure](../articles/storage/common/storage-redundancy-lrs.md).

Gardez ces points à l’esprit au moment de choisir une option de réplication :

* Le stockage géoredondant interzone (GZRS) (préversion) offre une haute disponibilité et une durabilité maximale en répliquant les données de manière synchrone sur trois zones de disponibilité Azure, puis en répliquant les données de manière asynchrone dans la région secondaire. Vous pouvez également activer l’accès en lecture à la région secondaire. Le stockage GZRS est conçu pour fournir une durabilité des objets d’au moins 99,99999999999999 % (16 chiffres 9) sur une année donnée. Pour plus d’informations sur le stockage GZRS, consultez l’article [Stockage géo-redondant dans une zone pour la haute disponibilité et la durabilité maximale (préversion)](../articles/storage/common/storage-redundancy-gzrs.md).
* Le stockage redondant interzone (ZRS), qui assure la haute disponibilité avec réplication synchrone sur trois zones de disponibilité Azure, peut représenter dans certains scénarios un meilleur choix que le stockage GRS ou RA-GRS. Pour plus d’informations sur le stockage redondant dans une zone, voir [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Une réplication asynchrone implique un délai entre le moment où les données sont écrites dans la région primaire et celui où elles sont répliquées dans la région secondaire. En cas de sinistre régional, les modifications qui n’ont pas encore été répliquées vers la région secondaire risquent d’être perdues si ces données ne peuvent pas être récupérées à partir de la région primaire.
* Avec le stockage géoredondant, le réplica n’est disponible pour l’accès en lecture ou écriture que si Microsoft lance un basculement vers la région secondaire. En cas de basculement, vous aurez accès en lecture et écriture à ces données après le basculement. Pour plus d’informations, voir [Conseils sur la récupération d’urgence](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Si votre application doit lire les données de la région secondaire, activez le stockage RA-GRS ou RA-GZRS (préversion).
