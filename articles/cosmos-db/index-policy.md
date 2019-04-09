---
title: Stratégies d’indexation d’Azure Cosmos DB
description: Comprendre le fonctionnement de l’indexation dans Azure Cosmos DB. Découvrez comment configurer et modifier la stratégie d’indexation pour bénéficier d’une indexation automatique et de meilleures performances.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: 6998db1679e67f8ac4bf7c81ea9373c66a9618ee
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278562"
---
# <a name="index-policy-in-azure-cosmos-db"></a>Stratégie d’indexation dans Azure Cosmos DB

Vous pouvez remplacer la stratégie d’indexation par défaut sur un conteneur Azure Cosmos en configurant les paramètres suivants :

* **Inclure ou exclure des éléments et des chemins dans l’index** : Vous pouvez exclure ou inclure des éléments spécifiques dans l’index, quand vous insérez ou remplacez les éléments dans un conteneur. Vous pouvez aussi inclure ou exclure des chemins/propriétés spécifiques à indexer sur tous les conteneurs. Les chemins peuvent être des modèles de caractère générique, par exemple, *.

* **Configurer les types d’index** : En outre pour la plage des chemins d’accès indexés, vous pouvez ajouter les autres types d’index, tel que spatiale.

* **Configurer les modes d’index** : en utilisant la stratégie d’indexation sur un conteneur, vous pouvez configurer différents modes d’indexation, comme *Cohérent* ou *Aucun*.

## <a name="indexing-modes"></a>Modes d’indexation

Azure Cosmos DB prend en charge deux modes d’indexation que vous pouvez configurer sur un conteneur Azure Cosmos via la stratégie d’indexation :

* **Cohérent** : Si la stratégie d’un conteneur Azure Cosmos est définie sur *cohérent*, les requêtes sur un conteneur spécifique suivent le même niveau de cohérence que celui spécifié pour les lectures ponctuelles (par exemple, fort, obsolescence limitée, session ou éventuel). 

  L’index est mis à jour de manière synchrone en même temps que les éléments. Par exemple, les opérations d’insertion, de remplacement, de mise à jour et de suppression sur un élément entraînent la mise à jour de l’index. L’indexation cohérente prend en charge les requêtes cohérentes au détriment du débit d’écriture. La réduction du débit d’écriture dépend des « chemins inclus dans l’index » et « niveau de cohérence ». Mode d’indexation cohérent est conçu pour conserver l’index à jour avec les mises à jour et immédiatement à répondre aux requêtes.

* **Aucun** : un conteneur en mode d’indexation Aucun n’est associé à aucun index. Ce mode est souvent employé si une base de données Azure Cosmos est utilisée comme stockage de clés-valeurs et si les éléments sont accessibles seulement par le biais de leur propriété ID.

  > [!NOTE]
  > Configuration du mode d’indexation en tant qu’un *aucun* a pour effet secondaire de la suppression de tous les index existants. Vous devez utiliser cette option si vos modèles d’accès nécessitent seulement un ID ou un lien vers lui-même.

Les niveaux de cohérence de requête sont gérés de la même façon que les opérations de lecture normales. Base de données Azure Cosmos retourne une erreur si vous interrogez le conteneur qui a un *aucun* mode d’indexation. Vous pouvez exécuter les requêtes en tant qu’analyses via explicites **x-ms-documentdb-enable-scan** en-tête dans l’API REST ou le **EnableScanInQuery** option de requête à l’aide du SDK .NET. Certaines fonctionnalités de requête, comme ORDER BY, ne sont actuellement pas prises en charge avec **EnableScanInQuery**, car elles mandatent un index correspondant.

## <a name="modifying-the-indexing-policy"></a>Modification de la stratégie d’indexation

Dans Azure Cosmos DB, vous pouvez mettre à jour à tout moment la stratégie d’indexation d’un conteneur. Une modification de la stratégie d’indexation sur un conteneur Azure Cosmos peut entraîner une modification dans la forme de l’index. Ce changement affecte les chemins qui peuvent être indexés, leur précision et le mode de cohérence de l’index lui-même. Un changement dans la stratégie d’indexation nécessite donc une transformation de l’ancien index en un nouvel index.

### <a name="index-transformations"></a>Transformations d’index

Toutes les transformations d’index s’effectuent en ligne. Les éléments indexés par l’ancienne stratégie sont transformés efficacement par la nouvelle stratégie sans affecter la disponibilité de l’écriture ou le débit approvisionné sur le conteneur. La cohérence de lire et écrire des opérations qui sont effectuées à l’aide de l’API REST, SDK, ou à l’aide des procédures stockées et déclencheurs n’est pas affectée au cours de la transformation d’index.

Modification de stratégie d’indexation est une opération asynchrone, et le temps nécessaire à l’opération varie selon le nombre d’éléments, un débit approvisionné et la taille des éléments. Alors que la réindexation est en cours d’exécution, votre requête ne peut pas retourner tous les résultats de correspondance, si les requêtes sont effectuées à utiliser l’index est en cours de modification, et les requêtes ne retournent pas les erreurs/échecs. Alors que la réindexation est en cours d’exécution, les requêtes sont cohérents, indépendamment de la configuration du mode d’indexation. Après la transformation de l’index, vous continuerez à voir des résultats cohérents. Cela s’applique aux requêtes envoyées par les interfaces comme l’API REST, les SDK ou les déclencheurs et procédures stockées. Transformation d’index est exécutée de façon asynchrone, en arrière-plan, sur les réplicas à l’aide des ressources d’échange qui sont disponibles pour les réplicas spécifiques.

Toutes les transformations d’index s’effectuent sur place. Azure Cosmos DB ne gère pas deux copies de l’index. Par conséquent, aucun espace disque supplémentaire n’est nécessaire ou utilisé dans vos conteneurs pendant la transformation d’index.

Lorsque vous modifiez la stratégie d’indexation, les modifications sont appliquées pour passer de l’ancien index vers le nouvel index et reposent principalement sur les configurations de mode d’indexation. Les configurations de mode d’indexation jouent un rôle majeur par rapport à d’autres propriétés comme les chemins inclus/exclus, les genres d’index et la précision.

Si vos anciennes et nouvelles stratégies utilisent une indexation en mode **Cohérent**, la base de données Azure Cosmos effectue une transformation d’index en ligne. Vous ne pouvez pas appliquer une autre modification de stratégie d’indexation qui a le mode d’indexation Cohérent pendant que la transformation est en cours. Quand vous basculez sur le mode Aucun, l’index est supprimé immédiatement. Passez au mode Aucun quand vous voulez annuler une transformation en cours et lancer une stratégie d’indexation différente.

## <a name="modifying-the-indexing-policy---examples"></a>Modification de la stratégie d’indexation - Exemples

Voici les cas d’usage courants lorsque vous souhaitez mettre à jour une stratégie d’indexation :

* Si vous souhaitez avoir des résultats cohérents de fonctionnement normal, mais à revenir à la **aucun** mode d’indexation lors de l’importation de données en bloc.

* Si vous voulez commencer à utiliser des fonctionnalités d’indexation sur vos conteneurs Azure Cosmos DB actuels. Par exemple, vous pouvez utiliser l’interrogation géospatiale, qui nécessite le genre d’index Spatial, ou des requêtes de plage de chaînes/ORDER BY, qui nécessitent le genre d’index Plage de chaînes.

* Si vous voulez sélectionner manuellement les propriétés à indexer et les changer au fil du temps pour les adapter à vos charges de travail.

* Si vous voulez ajuster la précision d’indexation pour améliorer les performances de requête ou réduire le stockage utilisé.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’indexation, consultez les articles suivants :

* [Vue d’ensemble d’indexation](index-overview.md)
* [Types d’index](index-types.md)
* [Chemins d’accès de l’index](index-paths.md)
* [Comment gérer la stratégie d’indexation](how-to-manage-indexing-policy.md)
