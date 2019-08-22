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
ms.openlocfilehash: 5df0fe23183b53f13d6c6545e248724324434cde
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029779"
---
Les options de réplication pour un compte de stockage comprennent :

* [Stockage localement redondant (LRS)](../articles/storage/common/storage-redundancy-lrs.md) : Une stratégie de réplication simple et économique. Vos données sont répliquées de manière synchrone trois fois dans la région principale.
* [Stockage redondant interzone (ZRS)](../articles/storage/common/storage-redundancy-zrs.md) : Réplication pour les scénarios nécessitant une haute disponibilité. Les données sont répliquées de façon synchrone sur trois zones de disponibilité Azure dans la région primaire.
* [Stockage géo-redondant (GRS)](../articles/storage/common/storage-redundancy-grs.md) : Réplication entre les régions pour vous protéger en cas d’interruption régionale. Les données sont répliquées de manière synchrone trois fois dans la région primaire, puis répliquées de façon asynchrone dans la région secondaire. Pour un accès en lecture aux données dans la région secondaire, activez le stockage géoredondant avec accès en lecture (RA-GZRS).
* [Stockage géoredondant interzone (GZRS) (préversion)](../articles/storage/common/storage-redundancy-gzrs.md) : Réplication pour les scénarios nécessitant à la fois une haute disponibilité et une durabilité maximale. Les données sont répliquées de manière synchrone dans trois zones de disponibilité Azure dans la région primaire, puis répliquées de façon asynchrone dans la région secondaire. Pour un accès en lecture aux données dans la région secondaire, activez le stockage géoredondant interzone avec accès en lecture (RA-GZRS).
