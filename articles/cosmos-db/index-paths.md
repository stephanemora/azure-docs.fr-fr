---
title: Utilisation des chemins d’index dans Azure Cosmos DB
description: Vue d’ensemble des chemins d’index dans Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/13/2019
ms.author: mjbrown
ms.openlocfilehash: d0fce763822ded374eab2f70c3f319aba0c89267
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992835"
---
# <a name="index-paths-in-azure-cosmos-db"></a>Chemins d’index dans Azure Cosmos DB

En utilisant les chemins d’index dans Azure Cosmos DB, vous pouvez choisir d’inclure ou d’exclure un chemin spécifique de l’indexation. Le choix de chemins spécifiques permet d’améliorer les performances d’écriture et d’abaisser l’index de stockage pour les scénarios dans lesquels vous connaissez les modèles de requête. Les chemins d’index commencent par l’opérateur de caractères génériques racine (`/`) et se terminent généralement par l’opérateur de caractères génériques `?`. Ce modèle indique qu’il y a plusieurs valeurs possibles pour le préfixe. Par exemple, pour servir la requête `SELECT * FROM Families F WHERE F.familyName = "Andersen"`, vous devez inclure un chemin d’index pour `/familyName/?` dans la politique d’index du conteneur.

Les chemins d'index peuvent aussi utiliser l'opérateur générique `*` pour spécifier le comportement des chemins de manière récursive sous le préfixe. Par exemple, `/payload/*` permet d’exclure de l’indexation tout ce qui figure sous la propriété « payload ».

## <a name="common-patterns-for-index-paths"></a>Modèles courants pour les chemins d’index

Voici les modèles courants de spécification des chemins d'index :

| **Chemin d’accès** | **Description/Cas d’utilisation** |
| ---------- | ------- |
| /          | Chemin par défaut de la collection. Récursif et s’applique à toute l’arborescence du document.|
| /prop/?    | Chemin d’accès de l’index requis pour traiter les requêtes comme les suivantes (avec les types de plage, respectivement) : <br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5 <br><br>SELECT FROM collection c ORDER BY c.prop  |
| /prop/*    | Chemin d'index pour tous les chemins d'accès sous l'étiquette spécifiée. Fonctionne avec les requêtes suivantes <br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop |
| /props/[]/?| Chemin d’accès de l’index requis pour traiter l’itération et les requêtes JOIN dans les tableaux de scalaires comme ["a", "b", "c"] :<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5 |
| /props/[] /subprop/? | Chemin d’accès de l’index requis pour traiter l’itération et les requêtes JOIN dans les tableaux d’objets comme [{subprop: "a"}, {subprop: "b"}] :<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value" |
| /prop/subprop/? | Chemin d’accès de l’index requis pour traiter les requêtes (avec les types de plage, respectivement) :<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5  |

Quand vous définissez des chemins d’index personnalisé, vous devez spécifier la règle d’indexation par défaut pour la totalité de l’élément, désignée par le chemin spécial `/*`.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’indexation dans Azure Cosmos DB, consultez les articles suivants :

- [Vue d’ensemble de l’indexation](index-overview.md)
- [Stratégie d’indexation dans Azure Cosmos DB](indexing-policies.md)
- [Types d’index dans Azure Cosmos DB](index-types.md)
