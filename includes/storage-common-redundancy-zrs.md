---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4a43966180850645584043690b1be9d6ae232f6e
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027445"
---
Le stockage redondant dans une zone (ZRS) réplique vos données de façon synchrone sur trois clusters de stockage dans une seule région. Chaque cluster de stockage est physiquement séparé des autres et se trouve dans sa propre zone de disponibilité. Chaque zone de disponibilité, et le cluster ZRS qu’elle contient, est autonome, avec des utilitaires et fonctionnalités de mise en réseau séparés.

Le stockage de vos données dans un compte ZRS vous permet d’accéder et de gérer vos données au cas où une zone n’est plus disponible. Le stockage redondant dans une zone (ZRS) offre d’excellentes performances et une latence faible. Le stockage ZRS offre les mêmes [objectifs d’évolutivité](../articles/storage/common/storage-scalability-targets.md) que le [stockage localement redondant (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Envisagez le stockage ZRS pour des scénarios qui requièrent une cohérence forte, une durabilité élevée et une haute disponibilité, même si une panne ou une catastrophe naturelle rend un centre de données zonal indisponible. Le stockage ZRS offre une durabilité des objets de stockage d’au moins 99,9999999999 % (12 neuf) sur une année donnée.

Pour plus d’informations sur les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview).