---
title: Différences entre les API de gestion et les API de service - Azure Batch | Microsoft Docs
description: Les API fonctionnent sur les différentes couches du service Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672757"
---
# <a name="service-level-and-management-level-apis"></a>API au niveau de service et API au niveau de gestion

Azure Batch comprend deux ensembles d’API : l’un pour le niveau de service et l’autre pour le niveau de gestion. L’attribution de noms est souvent similaire, mais ils retournent des résultats différents. Si vous souhaitez obtenir des journaux d’activité, utilisez les API de gestion. Les API de niveau de service ignorent la couche de gestion des ressources Azure, et elles ne sont pas journalisées.


La gestion Batch et le service Batch ont des API pour le pool, par exemple. 
- Cette API pour supprimer le pool est ciblée directement sur le compte Batch : https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Cette API pour supprimer le pool https://docs.microsoft.com/rest/api/batchmanagement/pool/delete est ciblée sur la couche management.azure.com.

