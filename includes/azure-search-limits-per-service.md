---
title: Fichier Include
description: Fichier Include
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 179f525b7b6a7e51889b14b66df6c537ca56bd75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272799"
---
Le stockage est limité par l’espace disque ou par le *nombre maximum* inconditionnel d’index, de documents ou d’autres ressources de haut niveau, suivant la limite atteinte en premier. Le tableau suivant décrit les limites de stockage. Pour connaître les limites maximales imposées aux index, aux documents et à d’autres objets, consultez les [limites par ressource](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Ressource | Gratuit | De base<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Contrat de Niveau de Service (SLA)<sup>3</sup>  |Non |Oui |Oui |Oui |Oui |Oui |Oui |Oui |
| Stockage par partition |50 Mo |2 Go |25 Go |100 Go |200 Go |200 Go |1 To |2 To |
| Partitions par service |N/A |1 |12 |12 |12 |3 |12 |12 |
| Taille de partition |N/A |2 Go |25 Go |100 Go |200 Go |200 Go |1 To |2 To |
| Réplicas |N/A |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Le niveau « De base » comporte une partition fixe. À ce niveau, des unités de recherche supplémentaires sont utilisées pour allouer davantage de réplicas pour les charges de travail de requête accrues.

<sup>2</sup> S3 HD a une limite inconditionnelle de trois partitions, ce qui est inférieur à la limite de partition de S3. La limite de partition inférieure est imposée car le nombre d’index pour S3 HD est sensiblement plus élevé. En raison des limites existantes de service pour les ressources de calcul (traitement et stockage) et le contenu (index et documents), la limite de contenu est atteinte en premier.

<sup>3</sup> Des contrats de niveau de service sont proposés pour les services facturables sur les ressources dédiées. Les services gratuits et les fonctionnalités en préversion n’en ont pas. En ce qui concerne les services facturables, les contrats SLA s’appliquent dès qu’une redondance suffisante du service est configurée. Un SLA de requête (lecture) requiert au moins deux réplicas. Un SLA de requête et d’indexation (lecture-écriture) nécessite au moins trois réplicas. Le nombre de partitions n’est pas pris en compte dans les SLA. 