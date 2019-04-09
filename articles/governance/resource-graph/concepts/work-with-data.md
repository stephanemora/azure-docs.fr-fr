---
title: Utiliser de grands jeux de données
description: Découvrez comment récupérer et contrôler des jeux de données volumineux lors de l’utilisation d’Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/01/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 729e9fe749212942c6dc18fc7d6301934e7dd184
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262463"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Utilisation de jeux de données volumineux d’Azure Resource Graph

Azure Resource Graph est conçu pour obtenir et utiliser des informations sur des ressources dans votre environnement Azure. Resource Graph permet d’obtenir ces données rapidement, même en cas d’interrogation de milliers d’enregistrements. Resource Graph offre plusieurs options pour utiliser ces jeux de données volumineux.

## <a name="data-set-result-size"></a>Taille du jeu de résultats

Par défaut, Resource Graph limite à **100** le nombre d’enregistrements que retourne toute requête. Ce contrôle protège l’utilisateur et le service contre des requêtes involontaires qui auraient pour effet de retourner des jeux de données volumineux. Cela se produit le plus souvent quand un client expérimente des requêtes pour rechercher et filtrer des ressources d’une manière correspondant à ses besoins particuliers. Ce contrôle diffère de l’utilisation des opérateurs linguistiques [top](/azure/kusto/query/topoperator) ou [limit](/azure/kusto/query/limitoperator) d’Azure Data Explorer pour limiter les résultats.

> [!NOTE]
> Lorsque vous utilisez **première**, il est recommandé de classer les résultats au moins une colonne avec `asc` ou `desc`. Sans effectuer de tri, les résultats retournés sont aléatoires et pas reproductibles.

La limite par défaut peut être modifiée via toutes les méthodes d’interaction avec Resource Graph. Les exemples suivants montrent comment modifier la limite de taille de jeu de données en la définissant sur _200_ :

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

Dans l’[API REST](/rest/api/azureresourcegraph/resources/resources), le contrôle est **$top** et fait partie de **QueryRequestOptions**.

Le contrôle _le plus restrictif_ l’emporte. Par exemple, si votre requête utilise les opérateurs **top** ou **limit** et aurait pour effet de retourner plus d’enregistrements qu’avec l’opérateur **First**, le nombre maximal d’enregistrements retournés est égal à celui obtenu avec l’opérateur **First**. De même, si les opérateurs **top** ou **limit** ont pour effet de retourner un nombre d’enregistrements inférieur à celui que retournerait l’opérateur **First**, le jeu d’enregistrements retourné est celui comprenant le plus petit nombre d’enregistrements retournés par les opérateurs **top** ou **limit**.

La valeur maximale autorisée de l’opérateur **First** est actuellement de _5 000_.

## <a name="skipping-records"></a>Omission d’enregistrements

L’option suivante pour la manipulation de jeux de données volumineux est l’utilisation du contrôle **Skip**. Ce contrôle permet à votre requête d’omettre ou d’ignorer le nombre défini d’enregistrements avant de retourner les résultats. Le contrôle **Skip** est utile pour les requêtes qui trient les résultats de façon explicite lorsque l’intention est d’accéder à des enregistrements quelque part au milieu de jeu de résultats. Si les résultats nécessaires figurent à la fin du jeu de données retourné, il est plus efficace d’utiliser une configuration de tri différente afin de récupérer à la place les résultats à partir de la partie supérieure du jeu de données.

> [!NOTE]
> Lorsque vous utilisez **Skip**, il est recommandé de classer les résultats au moins une colonne avec `asc` ou `desc`. Sans effectuer de tri, les résultats retournés sont aléatoires et pas reproductibles.

Les exemples suivants montrent comment ignorer les _10_ premiers enregistrements qu’une requête retournerait, en retournant le jeu de résultats à partir du 11e enregistrement :

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

Dans l’[API REST](/rest/api/azureresourcegraph/resources/resources), le contrôle est **$skip** et fait partie de **QueryRequestOptions**.

## <a name="paging-results"></a>Résultats de pagination

Lorsqu’il est nécessaire de diviser un jeu de résultats en jeux d’enregistrements pour le traitement de plus petits ou, car un jeu de résultats dépasse la valeur maximale autorisée de _1000_ enregistrements retournés, utilisez la pagination. Pour indiquer qu’un jeu de résultats a été divisé, l’[API REST](/rest/api/azureresourcegraph/resources/resources) **QueryResponse** fournit les valeurs **resultTruncated** et **$skipToken**.
La valeur **resultTruncated** est une valeur booléenne qui informe l’utilisateur de l’existence d’enregistrements supplémentaires non retournés dans la réponse. Cette condition peut également être identifiée lorsque la valeur de la propriété **count** est inférieure à celle de la propriété **totalRecords**. La valeur **totalRecords** définit le nombre d’enregistrements correspondant à la requête.

Lorsque la valeur **resultTruncated** est **true**, la propriété **$skipToken** est définie dans la réponse. Cette valeur est utilisée avec les mêmes valeurs de requête et d’abonnement pour obtenir le jeu d’enregistrements suivant correspondant à la requête.

> [!IMPORTANT]
> Pour que la pagination fonctionne, la requête doit **projeter** le champ **id**. S’il est manquant à partir de la requête, la réponse de l’API REST n’inclut pas le **$skipToken**.

Pour obtenir un exemple, voir [Requête Page suivante](/rest/api/azureresourcegraph/resources/resources#next_page_query) dans la documentation de l’API REST.

## <a name="next-steps"></a>Étapes suivantes

- Voir l’utilisation du langage dans les [requêtes de démarrage](../samples/starter.md)
- Voir les utilisations avancées dans les [Requêtes avancées](../samples/advanced.md)
- Apprendre à [explorer les ressources](explore-resources.md)