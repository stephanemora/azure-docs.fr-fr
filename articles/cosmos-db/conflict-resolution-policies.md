---
title: Types de résolution des conflits et stratégies de résolution dans Azure Cosmos DB
description: Cet article décrit les catégories de conflits et les stratégies de résolution des conflits dans Azure Cosmos DB.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 1b2a122cc8a04d4f0044ecb0fe0341357bc29c0f
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514823"
---
# <a name="conflict-types-and-resolution-policies"></a>Types de conflits et stratégies de résolution

Les conflits et les stratégies de résolution des conflits sont applicables si votre compte Cosmos est configuré avec plusieurs régions d’écriture.

Pour les comptes Cosmos configurés avec plusieurs régions d’écriture, des conflits de mise à jour peuvent se produire lorsque plusieurs rédacteurs mettent à jour simultanément le même élément dans plusieurs régions. Les conflits de mise à jour sont classés selon les trois types suivants :

1. **Conflits d’insertion** : ces conflits peuvent se produire quand une application insère simultanément deux éléments, ou plus, avec le même index unique (par exemple, propriété ID) à partir de deux régions, ou plus. Dans ce cas, toutes les écritures peuvent réussir initialement dans leur région locale respective, mais en fonction de la stratégie de résolution des conflits que vous choisissez, un seul élément avec l’ID d’origine est validé.

1. **Conflits de remplacement** : ces conflits peuvent se produire quand une application met à jour un seul élément à partir de deux régions, ou plus, simultanément.

1. **Conflits de suppression** : ces conflits peuvent se produire quand une application supprime un élément à partir d’une région et le met à jour à partir d’une autre région, simultanément.

## <a name="conflict-resolution-policies"></a>Stratégies de résolution des conflits

Cosmos DB offre un mécanisme flexible piloté par la stratégie pour résoudre les conflits de mise à jour. Vous pouvez sélectionner parmi les deux stratégies de résolution des conflits suivantes sur un conteneur Cosmos :

- **Dernière écriture prioritaire (LWW)**  : cette stratégie de résolution par défaut utilise une propriété timestamp définie par le système (basée sur le protocole d’horloge de synchronisation de l’heure). Ou bien, lorsque vous utilisez l’API SQL, Cosmos DB vous permet de spécifier toute autre propriété numérique personnalisée (également appelée « chemin de résolution des conflits ») à utiliser pour la résolution des conflits.  

  Si deux ou plusieurs éléments sont en conflit lors d’opérations d’insertion ou de remplacement, l’élément qui contient la valeur la plus élevée pour le « chemin de résolution des conflits » devient le « gagnant ». Si plusieurs éléments ont la même valeur numérique pour le chemin de résolution des conflits, la version du « gagnant » sélectionné est déterminée par le système. Toutes les régions sont assurées de converger vers un seul gagnant et obtiennent une version identique de l’élément validé. Si des conflits de suppression sont impliqués, la version supprimée gagne toujours sur les conflits d’insertion ou de remplacement, quelle que soit la valeur du chemin de résolution du conflit.

  > [!NOTE]
  > Last-Write-Wins (LWW) est la stratégie de résolution des conflits par défaut et est disponible pour les comptes d’API SQL, Table, MongoDB, Cassandra et Gremlin.

  Pour plus d’informations, consultez les [exemples d’utilisation des stratégies de résolution des conflits LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Personnalisé** : cette stratégie de résolution est conçue pour la sémantique définie au niveau de l’application pour le rapprochement des conflits. Lors de la définition de cette stratégie sur votre conteneur Cosmos, vous devez également inscrire une procédure stockée de fusion, qui est appelée automatiquement lorsque des conflits de mise à jour sont détectés sous une transaction de base de données sur le serveur. Le système fournit exactement une garantie pour l’exécution d’une procédure de fusion dans le cadre du protocole d’engagement.  

  Toutefois, si vous configurez votre conteneur avec l’option de résolution personnalisée, mais qu’il est impossible d’inscrire une procédure de fusion sur le conteneur, ou si la procédure de fusion lève une exception lors de l’exécution, les conflits sont écrits dans le flux de conflits. Votre application devra ensuite résoudre manuellement les conflits dans le flux de conflits. Pour plus d’informations, consultez les [exemples d’utilisation de la stratégie personnalisée de résolution et comment utiliser le flux de conflits](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > La stratégie personnalisée de résolution des conflits est uniquement disponible pour les comptes d’API SQL.

## <a name="next-steps"></a>Étapes suivantes

Ensuite, vous pouvez apprendre à configurer des stratégies de résolution des conflits en utilisant les articles suivants :

* [Comment utiliser la stratégie de résolution des conflits LWW](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Comment utiliser la stratégie personnalisée de résolution des conflits](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Comment utiliser le flux de conflits](how-to-manage-conflicts.md#read-from-conflict-feed)
