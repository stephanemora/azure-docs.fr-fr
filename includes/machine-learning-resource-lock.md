---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92204458"
---
Azure vous permet de placer des _verrous_ sur les ressources, afin qu’elles ne puissent pas être supprimées ou restent en lecture seule. __Le verrouillage d’une ressource peut entraîner des résultats inattendus.__ Certaines opérations qui, en apparence, ne modifient pas la ressource nécessitent en réalité des actions qui sont bloquées par ce verrou. 

Par exemple, l’application d’un verrou de suppression au groupe de ressources pour votre espace de travail empêchera les opérations de mise à l’échelle pour les clusters de calcul Azure ML.

Pour plus d’informations, consultez [Verrouiller les ressources pour empêcher les modifications inattendues](../articles/azure-resource-manager/management/lock-resources.md).