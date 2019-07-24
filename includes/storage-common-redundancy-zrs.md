---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d25efa8c666fa91c7bd652a7cf931ea6ca379aa2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133144"
---
Le stockage redondant interzone (ZRS) réplique vos données de façon synchrone sur trois clusters de stockage dans une seule région. Chaque cluster de stockage est séparé physiquement des autres et se trouve dans sa propre zone de disponibilité. Chaque zone de disponibilité &mdash;et le cluster ZRS qu’elle contient&mdash; est autonome et inclut des utilitaires et fonctionnalités de mise en réseau séparés. Une demande d’écriture adressée à un compte de stockage ZRS renvoie un indicateur de réussite seulement une fois que les données sont écrites dans tous les réplicas des trois clusters.

Lorsque vous stockez vos données dans un compte de stockage à l’aide d’une réplication ZRS, vous pouvez continuer à accéder à vos données et à gérer celles-ci si une zone de disponibilité devient indisponible. Le stockage redondant dans une zone (ZRS) offre d’excellentes performances et une latence faible. Un stockage ZRS poursuit les mêmes [objectifs d’extensibilité](../articles/storage/common/storage-scalability-targets.md) qu’un [stockage localement redondant (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Envisagez un stockage ZRS pour des scénarios qui requièrent une cohérence, une durabilité et une haute disponibilité. Même si une panne ou une catastrophe naturelle révèle l’existence d’une zone de disponibilité indisponible, le stockage ZRS assure une durabilité d’objet de stockage d’au moins 99,9999999999 % (12 9) sur une année donnée.

Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview).