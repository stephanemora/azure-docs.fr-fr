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
ms.openlocfilehash: ac3e87d7f921da2c1089eb6f2c7e61fc2c432f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463942"
---
Le stockage redondant interzone (ZRS) réplique vos données de façon synchrone sur trois clusters de stockage dans une seule région. Chaque cluster de stockage est séparé physiquement des autres et se trouve dans sa propre zone de disponibilité. Chaque zone de disponibilité &mdash;et le cluster ZRS qu’elle contient&mdash; est autonome et inclut des utilitaires et fonctionnalités de mise en réseau séparés. Une demande d’écriture adressée à un compte de stockage ZRS renvoie un indicateur de réussite seulement une fois que les données sont écrites dans tous les réplicas des trois clusters.

Lorsque vous stockez vos données dans un compte de stockage à l’aide d’une réplication ZRS, vous pouvez continuer à accéder à vos données et à gérer celles-ci si une zone de disponibilité devient indisponible. Le stockage redondant dans une zone (ZRS) offre d’excellentes performances et une latence faible. Un stockage ZRS poursuit les mêmes objectifs d’extensibilité qu’un [stockage localement redondant (LRS)](../articles/storage/common/storage-redundancy-lrs.md). Pour plus d’informations au sujet des objectifs d’évolutivité pour les comptes de stockage standard, consultez [Objectifs d’extensibilité pour les comptes de stockage standard](../articles/storage/common/scalability-targets-standard-account.md).

Envisagez un stockage ZRS pour des scénarios qui requièrent une cohérence, une durabilité et une haute disponibilité. Même si une panne ou une catastrophe naturelle révèle l’existence d’une zone de disponibilité indisponible, le stockage ZRS assure une durabilité d’objet de stockage d’au moins 99,9999999999 % (12 9) sur une année donnée.

Le stockage géoredondant interzone (GZRS) (préversion) réplique vos données de manière synchrone sur les trois zones de disponibilité Azure de la région primaire, puis réplique les données de manière asynchrone dans la région secondaire. Le stockage GZRS fournit une haute disponibilité et une durabilité maximale. Le stockage GZRS est conçu pour fournir une durabilité des objets d’au moins 99,99999999999999 % (16 chiffres 9) sur une année donnée. Pour un accès en lecture aux données dans la région secondaire, activez le stockage géoredondant interzone avec accès en lecture (RA-GZRS). Pour plus d’informations sur le stockage GZRS, consultez l’article [Stockage géo-redondant dans une zone pour la haute disponibilité et la durabilité maximale (préversion)](../articles/storage/common/storage-redundancy-gzrs.md).

Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview).
