---
title: Fichier Include
description: Fichier Include
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b298c87d802314bd865bd6f38c35e4361eb69f3f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160508"
---
Le stockage est limité par l’espace disque ou par le *nombre maximum* inconditionnel d’index, de documents ou d’autres ressources de haut niveau, suivant la limite atteinte en premier. Le tableau suivant décrit les limites de stockage. Pour connaître les limites maximales sur les index, les documents et les autres objets, consultez [limites par ressource](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Ressource | Gratuit | Base<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Niveau contrat de service (SLA)<sup>3</sup>  |Non |OUI |OUI |OUI |OUI |OUI |OUI |Oui |
| Stockage par partition |50 Mo |2 Go |25 Go |100 Go |200 Go |200 Go |1 To |2 To |
| Partitions par service |N/A |1 |12 |12 |12 |3 |12 |12 |
| Taille de la partition |N/A |2 Go |25 Go |100 Go |200 Go |200 Go |1 To |2 To |
| Réplicas |N/A |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Le niveau « De base » comporte une partition fixe. À ce niveau, les unités de recherche supplémentaires sont utilisées pour allouer davantage de réplicas pour les charges de travail accrue des requêtes.

<sup>2</sup> S3 HD a une limite inconditionnelle de trois partitions, ce qui est inférieur à la limite de partition de S3. La limite de partition inférieure est imposée car le nombre d’index pour S3 HD est sensiblement plus élevé. En raison des limites existantes de service pour les ressources de calcul (traitement et stockage) et le contenu (index et documents), la limite de contenu est atteinte en premier.

<sup>3</sup> les contrats de niveau de Service sont proposés pour les services facturables sur des ressources dédiées. Les services gratuits et les fonctionnalités en préversion n’en ont pas. En ce qui concerne les services facturables, les contrats SLA s’appliquent dès qu’une redondance suffisante du service est configurée. Deux ou plusieurs réplicas sont nécessaires pour les contrats SLA de requête (lecture). Au moins trois réplicas sont nécessaires pour interroger et indexer des contrats SLA (en lecture-écriture). Le nombre de partitions n’est pas un facteur de contrat SLA. 