---
title: Fichier Include
description: Fichier Include
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: fc0451aa89da9b84a5a01a0762425f7533dded3c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553768"
---
Le stockage est limité par l’espace disque ou par le *nombre maximum* inconditionnel d’index, de documents ou d’autres ressources de haut niveau, suivant la limite atteinte en premier. Le tableau suivant décrit les limites de stockage. Pour connaître les limites maximales sur les index, les documents et les autres objets, consultez [limites par ressource](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Ressource | Gratuit | Base<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| Niveau contrat de service (SLA)<sup>3</sup>  |Non  |OUI |OUI |OUI |OUI |Oui |
| Stockage par partition |50 Mo |2 Go |25 Go |100 Go |200 Go |200 Go |
| Partitions par service |S.O. |1 |12 |12 |12 |3 |
| Taille de partition |S.O. |2 Go |25 Go |100 Go |200 Go |200 Go |
| Réplicas |S.O. |3 |12 |12 |12 |12 |

<sup>1</sup>basic a une partition fixe. À ce niveau, les unités de recherche supplémentaires sont utilisées pour allouer davantage de réplicas pour les charges de travail accrue des requêtes.

<sup>2</sup>S3 HD a une limite inconditionnelle de trois partitions, ce qui est inférieur à la limite de partition de S3. La limite de partition inférieure est imposée car le nombre d’index pour S3 HD est sensiblement plus élevé. En raison des limites existantes de service pour les ressources de calcul (traitement et stockage) et le contenu (index et documents), la limite de contenu est atteinte en premier.

<sup>3</sup>les contrats de niveau de Service sont proposés pour les services facturables sur des ressources dédiées. Les services gratuits et les fonctionnalités en préversion n’en ont pas. En ce qui concerne les services facturables, les contrats SLA s’appliquent dès qu’une redondance suffisante du service est configurée. Deux ou plusieurs réplicas sont nécessaires pour les contrats SLA de requête (lecture). Au moins trois réplicas sont nécessaires pour interroger et indexer des contrats SLA (en lecture-écriture). Le nombre de partitions n’est pas un facteur de contrat SLA. 