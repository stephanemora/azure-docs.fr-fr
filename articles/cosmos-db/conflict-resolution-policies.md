---
title: Types de résolution des conflits et stratégies de résolution avec plusieurs régions d’écriture dans Azure Cosmos DB
description: Cet article décrit les catégories de conflits et les stratégies de résolution des conflits dans Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ebea55f769ca16bfa344d0a100fe16cec6d784d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892589"
---
# <a name="conflict-types-and-resolution-policies"></a>Types de conflits et stratégies de résolution

Les conflits et les stratégies de résolution de conflits sont applicables si votre compte Azure Cosmos DB est configuré avec plusieurs régions d’écriture.

Pour les comptes Azure Cosmos configurés avec plusieurs régions d’écriture, les conflits de mise à jour peuvent se produire lorsque les auteurs mettent à jour le même élément dans plusieurs régions. Conflits de mise à jour peuvent être des trois types suivants :

* **Conflits d’insertion** : Ces conflits peuvent se produire lorsqu’une application insère simultanément deux ou plusieurs éléments avec le même index unique dans deux ou plusieurs régions. Par exemple, ce conflit peut se produire avec une propriété ID.

* **Conflits de remplacement** : Ces conflits peuvent se produire lorsqu’une application met à jour le même élément simultanément dans deux ou plusieurs régions.

* **Conflits de suppression** : Ces conflits peuvent se produire lorsqu’une application supprime un élément dans une région simultanément et met à jour dans une autre région.

## <a name="conflict-resolution-policies"></a>Stratégies de résolution des conflits

Azure Cosmos DB offre un mécanisme flexible pilotée par des stratégies pour résoudre les conflits d’écriture. Vous pouvez sélectionner à partir de deux stratégies de résolution de conflit sur un conteneur Azure Cosmos :

- **Dernière écriture prioritaire (LWW)** : cette stratégie de résolution, par défaut, utilise une propriété d’horodatage définie par le système. Elle est basée sur le protocole d’horloge de synchronisation de l’heure. Si vous utilisez l’API SQL, vous pouvez spécifier n’importe quel autre numérique propriété personnalisée (par exemple, votre propre notion d’un horodateur) à utiliser pour la résolution des conflits. Une propriété numérique personnalisée est également appelée le *chemin d’accès de résolution de conflit*. 

  Si deux ou plusieurs éléments sont en conflit lors d’opérations d’insertion ou de remplacement, l’élément ayant la valeur la plus élevée pour le chemin de résolution du conflit l’emporte. Le système détermine le gagnant si plusieurs éléments présentent la même valeur numérique pour le chemin de résolution du conflit. Toutes les régions sont assurées de converger vers un seul gagnant et obtiennent une version identique de l’élément validé. Lorsque des conflits de suppression sont impliqués, la version supprimée l’emporte toujours sur les conflits d’insertion ou de remplacement. Ce résultat se produit quelle que soit la valeur du chemin d’accès de résolution de conflit.

  > [!NOTE]
  > La stratégie Dernière écriture prioritaire (LWW) est la stratégie de résolution de conflit par défaut. Il est disponible pour les API suivantes : SQL, MongoDB, Cassandra, Gremlin et Table.

  Pour plus d’informations, consultez les [exemples qui utilisent des stratégies de résolution de conflit LWW](how-to-manage-conflicts.md).

- **Personnalisé** : cette stratégie de résolution est destinée à la sémantique définie au niveau de l’application pour le rapprochement des conflits. Lorsque vous définissez cette stratégie sur votre conteneur Azure Cosmos, vous devez également inscrire une *fusionner la procédure stockée*. Cette procédure est automatiquement appelée lorsque les conflits sont détectés sous une transaction de base de données sur le serveur. Le système fournit exactement une garantie pour l’exécution d’une procédure de fusion dans le cadre du protocole d’engagement.  

  Si vous configurez votre conteneur avec l’option de résolution personnalisé et vous ne parvenez pas à enregistrer une procédure de fusion sur le conteneur, ou la procédure de fusion lève une exception lors de l’exécution, les conflits sont écrits dans le *conflits flux*. Les conflits de l’application doivent ensuite être résolus manuellement dans le flux de conflits. Pour plus d’informations, consultez les [exemples illustrant la façon d’utiliser la stratégie de résolution personnalisée, et comment utiliser le flux de conflits](how-to-manage-conflicts.md).

  > [!NOTE]
  > La stratégie personnalisée de résolution de conflits est disponible uniquement pour les comptes d’API SQL.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer des stratégies de résolution des conflits :

* [Configurer la fonction multimaître dans vos applications](how-to-multi-master.md)
* [Comment gérer les stratégies de résolution de conflit](how-to-manage-conflicts.md)
* [Guide pratique pour lire le flux de conflits](how-to-manage-conflicts.md#read-from-conflict-feed)
