---
title: Fichier include
description: Fichier include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77157214"
---
Les options de redondance pour un compte de stockage comprennent :

* Stockage localement redondant (LRS) : Une stratégie de redondance simple et économique. Les données sont copiées de manière synchrone trois fois dans la région primaire.
* Stockage redondant interzone (ZRS) : Redondance pour les scénarios nécessitant une haute disponibilité. Les données sont copiées de façon synchrone sur trois zones de disponibilité Azure dans la région primaire.
* Stockage géoredondant (GRS) : Redondance entre les régions pour vous protéger en cas d’interruption régionale. Les données sont copiées de manière synchrone trois fois dans la région primaire, puis copiées de façon asynchrone dans la région secondaire. Pour un accès en lecture aux données dans la région secondaire, activez le stockage géoredondant avec accès en lecture (RA-GRS).
* Stockage géoredondant interzone (GZRS) (préversion) : Redondance pour les scénarios nécessitant à la fois une haute disponibilité et une durabilité maximale. Les données sont copiées de manière synchrone dans trois zones de disponibilité Azure dans la région primaire, puis copiées de façon asynchrone dans la région secondaire. Pour un accès en lecture aux données dans la région secondaire, activez le stockage géoredondant interzone avec accès en lecture (RA-GZRS).

Pour plus d’informations sur les options de redondance dans Stockage Azure, consultez [Redondance de Stockage Azure](../articles/storage/common/storage-redundancy.md).
