---
title: Types de résolution des conflits et stratégies de résolution dans Azure Cosmos DB
description: Cet article décrit les catégories de conflits et les stratégies de résolution des conflits dans Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 4af3f4c60f186c34d0f5522461ba70c68b59d1ab
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033891"
---
# <a name="conflict-types-and-resolution-policies"></a>Types de conflits et stratégies de résolution

Les conflits et les stratégies de résolution de conflits sont applicables si votre compte Azure Cosmos DB est configuré avec plusieurs régions d’écriture.

Pour les comptes Azure Cosmos DB configurés avec plusieurs régions d’écriture, des conflits de mise à jour peuvent se produire lorsque des rédacteurs mettent à jour simultanément le même élément dans plusieurs régions. Les conflits de mise à jour sont classés selon les trois types suivants :

* **Conflits d’insertion** : ces conflits peuvent se produire quand une application insère simultanément deux éléments, ou plus, avec le même index unique à partir de deux régions, ou plus. Par exemple, ce conflit peut apparaître avec une propriété ID. Toutes les écritures peuvent réussir initialement dans leur région locale respective. Toutefois, selon la stratégie de résolution de conflit que vous choisissez, seul un élément doté de l’ID d’origine est finalement validé.

* **Conflits de remplacement** : ces conflits peuvent se produire quand une application met à jour un seul élément à partir de deux régions, ou plus, simultanément.

* **Conflits de suppression** : ces conflits peuvent se produire quand une application supprime un élément à partir d’une région et le met à jour à partir d’une autre région, simultanément.

## <a name="conflict-resolution-policies"></a>Stratégies de résolution des conflits

Azure Cosmos DB offre un mécanisme souple, piloté par les stratégies, pour résoudre les conflits de mise à jour. Vous pouvez choisir entre deux stratégies de résolution de conflits sur un conteneur Azure Cosmos DB :

- **Dernière écriture prioritaire (LWW)** : cette stratégie de résolution, par défaut, utilise une propriété d’horodatage définie par le système. Elle est basée sur le protocole d’horloge de synchronisation de l’heure. Si vous utilisez l’API SQL Azure Cosmos DB, vous pouvez spécifier toute autre propriété numérique personnalisée à utiliser pour la résolution de conflits. Une propriété numérique personnalisée est également appelée « chemin de résolution du conflit ». 

  Si deux ou plusieurs éléments sont en conflit lors d’opérations d’insertion ou de remplacement, l’élément ayant la valeur la plus élevée pour le chemin de résolution du conflit l’emporte. Le système détermine le gagnant si plusieurs éléments présentent la même valeur numérique pour le chemin de résolution du conflit. Toutes les régions sont assurées de converger vers un seul gagnant et obtiennent une version identique de l’élément validé. Lorsque des conflits de suppression sont impliqués, la version supprimée l’emporte toujours sur les conflits d’insertion ou de remplacement. Ce résultat se produit quelle que soit la valeur du chemin de résolution du conflit.

  > [!NOTE]
  > La stratégie Dernière écriture prioritaire (LWW) est la stratégie de résolution de conflit par défaut. Elle est disponible pour les comptes d’API SQL, MongoDB, Cassandra, Gremlin et Table Azure Cosmos DB.

  Pour plus d’informations, consultez les [exemples qui utilisent des stratégies de résolution de conflit LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Personnalisé** : cette stratégie de résolution est destinée à la sémantique définie au niveau de l’application pour le rapprochement des conflits. Lorsque vous définissez cette stratégie sur votre conteneur Azure Cosmos DB, vous devez également inscrire une procédure stockée de fusion. Cette procédure est automatiquement appelée lorsque des conflits sont détectés sous une transaction de base de données côté serveur. Le système fournit exactement une garantie pour l’exécution d’une procédure de fusion dans le cadre du protocole d’engagement.  

  Deux points sont à retenir si vous configurez votre conteneur avec l’option de résolution personnalisée. Si vous ne procédez pas à l’inscription d’une procédure de fusion sur le conteneur, ou si la procédure de fusion lève une exception lors de l’exécution, les conflits sont écrits dans le flux de conflits. Les conflits de l’application doivent ensuite être résolus manuellement dans le flux de conflits. Pour plus d’informations, consultez les [exemples illustrant la façon d’utiliser la stratégie de résolution personnalisée, et comment utiliser le flux de conflits](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > La stratégie personnalisée de résolution de conflits est disponible uniquement pour les comptes d’API SQL.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer des stratégies de résolution de conflits. Consultez les articles suivants :

* [Utiliser la stratégie de résolution de conflits LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Utiliser la stratégie personnalisée de résolution de conflits](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Utiliser le flux de conflits](how-to-manage-conflicts.md#read-from-conflict-feed)
