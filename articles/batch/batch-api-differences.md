---
title: Différences entre les API de gestion et les API de service
description: Les API fonctionnent sur les différentes couches du service Azure Batch.
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867587"
---
# <a name="service-level-and-management-level-apis"></a>API au niveau de service et API au niveau de gestion

Azure Batch comprend deux ensembles d’API : l’un pour le niveau de service et l’autre pour le niveau de gestion. L’attribution de noms est souvent similaire, mais ils retournent des résultats différents. Si vous souhaitez obtenir des journaux d’activité, utilisez les API de gestion. Les API de niveau de service ignorent la couche de gestion des ressources Azure, et elles ne sont pas journalisées.


La gestion Batch et le service Batch ont des API pour le pool, par exemple. 
- Cette API pour supprimer le pool est ciblée directement sur le compte Batch : https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Cette API pour supprimer le pool https://docs.microsoft.com/rest/api/batchmanagement/pool/delete est ciblée sur la couche management.azure.com.

