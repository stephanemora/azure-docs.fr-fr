---
title: Stratégies d’indexation d’Azure Cosmos DB
description: Comprendre le fonctionnement de l’indexation dans Azure Cosmos DB. Découvrez comment configurer et modifier la stratégie d’indexation pour bénéficier d’une indexation automatique et de meilleures performances.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: 6c145b58a1f0eaaf93fb5797028e11ba8338d6be
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460231"
---
# <a name="index-policy-in-azure-cosmos-db"></a>Stratégie d’indexation dans Azure Cosmos DB

Vous pouvez remplacer la stratégie d’indexation par défaut sur un conteneur Azure Cosmos en configurant les paramètres suivants :

* **Inclure ou exclure des éléments et des chemins dans l’index** : vous pouvez exclure ou inclure des éléments spécifiques dans l’index quand vous insérez ou remplacez les éléments au sein d’un conteneur. Vous pouvez aussi inclure ou exclure des chemins/propriétés spécifiques à indexer sur tous les conteneurs. Les chemins peuvent être des modèles de caractère générique, par exemple, *.

* **Configurer les types d’index** : en plus de limiter les chemins indexés, vous pouvez ajouter d’autres types d’index, comme les index spatiaux.

* **Configurer les modes d’index** : en utilisant la stratégie d’indexation sur un conteneur, vous pouvez configurer différents modes d’indexation, comme *Cohérent* ou *Aucun*.

## <a name="indexing-modes"></a>Modes d’indexation 

Azure Cosmos DB prend en charge deux modes d’indexation configurables sur un conteneur Azure Cosmos. Vous pouvez configurer les deux modes d’indexation suivants avec la stratégie d’indexation : 

* **Cohérent** : si la stratégie d’un conteneur Azure Cosmos DB est définie sur Cohérent, les requêtes sur un conteneur spécifique suivent le même niveau de cohérence que celui spécifié pour les lectures ponctuelles (par exemple, forte, session, obsolescence limitée ou éventuelle). 

  L’index est mis à jour de manière synchrone en même temps que les éléments. Par exemple, les opérations d’insertion, de remplacement, de mise à jour et de suppression sur un élément entraînent la mise à jour de l’index. L’indexation cohérente prend en charge les requêtes cohérentes au détriment du débit d’écriture. La réduction du débit d’écriture dépend des « chemins inclus dans l’indexation » et du « niveau de cohérence ». Le mode d’indexation Cohérent est conçu pour écrire rapidement et interroger immédiatement les charges de travail.

* **Aucun** : un conteneur en mode d’indexation Aucun n’est associé à aucun index. Ce mode est souvent employé si une base de données Azure Cosmos est utilisée comme stockage de clés-valeurs et si les éléments sont accessibles seulement par le biais de leur propriété ID.

  > [!NOTE]
  > La configuration du mode d’indexation Aucun a pour effet secondaire de supprimer tout index existant. Vous devez utiliser cette option si vos modèles d’accès nécessitent seulement un ID ou un lien vers lui-même.

Les niveaux de cohérence de requête sont gérés de la même façon que les opérations de lecture normales. Une base de données Azure Cosmos retourne une erreur si vous interrogez un conteneur avec un mode d’indexation Aucun. Vous pouvez exécuter les requêtes comme des analyses avec l’en-tête explicite  **x-ms-documentdb-enable-scan** dans l’API REST ou l’option de requête  **EnableScanInQuery** à l’aide du SDK .NET. Certaines fonctionnalités de requête, comme ORDER BY, ne sont actuellement pas prises en charge avec **EnableScanInQuery**, car elles mandatent un index correspondant.

## <a name="modifying-the-indexing-policy"></a>Modification de la stratégie d’indexation

Dans Azure Cosmos DB, vous pouvez mettre à jour à tout moment la stratégie d’indexation d’un conteneur. Un changement dans la stratégie d’indexation sur un conteneur Azure Cosmos DB peut entraîner un changement dans la forme de l’index. Ce changement affecte les chemins qui peuvent être indexés, leur précision et le mode de cohérence de l’index lui-même. Un changement dans la stratégie d’indexation nécessite donc une transformation de l’ancien index en un nouvel index.

### <a name="index-transformations"></a>Transformations d’index

Toutes les transformations d’index s’effectuent en ligne. Les éléments indexés par l’ancienne stratégie sont transformés efficacement par la nouvelle stratégie, sans affecter la disponibilité d’écriture ou le débit provisionné sur le conteneur. La cohérence des opérations de lecture et d’écriture effectuées à l’aide de l’API REST, des SDK ou à partir de déclencheurs et de procédures stockées n’est pas affectée au cours de la transformation de l’index.

Le changement de la stratégie d’indexation est une opération asynchrone dont le temps d’exécution varie selon le nombre d’éléments, le débit provisionné et la taille des éléments. Quand la réindexation est en cours, votre requête peut ne pas retourner tous les résultats correspondants si elle utilise l’index en cours de modification. Les requêtes ne retournent alors aucun échec/erreur. Quand la réindexation est en cours, les requêtes sont cohérentes quelle que soit la configuration du mode d’indexation. Après la transformation de l’index, vous continuerez à voir des résultats cohérents. Cela s’applique aux requêtes envoyées par les interfaces comme l’API REST, les SDK ou les déclencheurs et procédures stockées. La transformation de l’index est exécutée de façon asynchrone en arrière-plan sur les réplicas à l’aide de ressources d’échange disponibles pour un réplica spécifique.

Toutes les transformations d’index s’effectuent sur place. Azure Cosmos DB ne gère pas deux copies de l’index. Par conséquent, aucun espace disque supplémentaire n’est nécessaire ou utilisé dans vos conteneurs pendant la transformation d’index.

Quand vous changez la stratégie d’indexation, les changements qui sont appliqués pour passer de l’ancien index au nouvel index sont principalement basés sur les configurations du mode d’indexation. Les configurations de mode d’indexation jouent un rôle majeur par rapport à d’autres propriétés comme les chemins inclus/exclus, les genres d’index et la précision.

Si vos anciennes et nouvelles stratégies utilisent une indexation en mode **Cohérent**, la base de données Azure Cosmos effectue une transformation d’index en ligne. Vous ne pouvez pas appliquer une autre modification de stratégie d’indexation qui a le mode d’indexation Cohérent pendant que la transformation est en cours. Quand vous basculez sur le mode Aucun, l’index est supprimé immédiatement. Passez au mode Aucun quand vous voulez annuler une transformation en cours et lancer une stratégie d’indexation différente.

Pour que la transformation d’index s’effectue, vérifiez que le conteneur a un espace de stockage suffisant. Si le conteneur atteint son quota de stockage, la transformation d’index est interrompue. La transformation d’index reprend automatiquement dès qu’un espace de stockage est disponible, par exemple, si vous supprimez certains éléments.

## <a name="modifying-the-indexing-policy---examples"></a>Modification de la stratégie d’indexation - Exemples

Voici les cas d’usage les plus courants de mise à jour d’une stratégie d’indexation :

* Si vous voulez avoir des résultats cohérents pendant le fonctionnement normal, mais que vous revenez au mode d’indexation **Aucun** pendant l’importation de données en bloc.

* Si vous voulez commencer à utiliser des fonctionnalités d’indexation sur vos conteneurs Azure Cosmos DB actuels. Par exemple, vous pouvez utiliser l’interrogation géospatiale, qui nécessite le genre d’index Spatial, ou des requêtes de plage de chaînes/ORDER BY, qui nécessitent le genre d’index Plage de chaînes.

* Si vous voulez sélectionner manuellement les propriétés à indexer et les changer au fil du temps pour les adapter à vos charges de travail.

* Si vous voulez ajuster la précision d’indexation pour améliorer les performances de requête ou réduire le stockage utilisé.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’indexation, consultez les articles suivants :

* [Vue d’ensemble de l’indexation](index-overview.md)
* [Types d’index](index-types.md)
* [Chemins des index](index-paths.md)
* [Guide pratique pour gérer la stratégie d’indexation](how-to-manage-indexing-policy.md)
