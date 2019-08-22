---
title: Utiliser l'outil de l'explorateur pour interroger les données dans le portail Azure - Recherche Azure
description: Utilisez les outils du portail Azure comme l’Explorateur de recherche pour interroger des index dans Recherche Azure. Entrez des termes de recherche ou des chaînes de recherche complètes avec une syntaxe avancée.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: e941e487adaac38c4ec3bd61a58b4b0c61f4c80a
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649964"
---
# <a name="search-explorer-for-querying-data-in-azure-search"></a>Utiliser l'explorateur pour interroger les données Recherche Azure 

Cet article vous explique comment interroger un index de Recherche Azure à l’aide de l’**Explorateur de recherche** dans le Portail Azure. Vous pouvez utiliser l’Explorateur de recherche pour envoyer des chaînes de requête Lucene simples ou complètes à un index existant dans votre service. 

   ![Commande Explorateur de recherche dans le portail](./media/search-explorer/search-explorer-cmd2.png "Commande Explorateur de recherche dans le portail")


Pour obtenir de l'aide, consultez [Démarrer l'Explorateur de recherche](#start-search-explorer).

## <a name="basic-search-strings"></a>Chaînes de recherche de base

Les exemples suivants supposent l’index intégré realestate. Pour créer cet index, consultez [Démarrage rapide : Importer, indexer et interroger dans le portail Azure](search-get-started-portal.md).

### <a name="example-1---empty-search"></a>Exemple 1 : recherche vide

Pour un premier aperçu de votre contenu, exécutez une recherche vide en cliquant sur **Rechercher** sans indiquer de termes. Une recherche vide est utile en tant que première requête, car elle renvoie des documents entiers pour vous permettre d'examiner leur composition. Une recherche vide ne présente aucun classement et les documents sont renvoyés dans une ordre arbitraire (`"@search.score": 1` pour tous les documents). Par défaut, 50 documents sont renvoyés dans une requête de recherche.

La syntaxe équivalente d'une recherche vide est `*` ou `search=*`.

   ```Input
   search=*
   ```

   **Résultats**
   
   ![Exemple de requête vide](./media/search-explorer/search-explorer-example-empty.png "Exemple de requête non qualifiée ou vide")

### <a name="example-2---free-text-search"></a>Exemple 2 : recherche de texte libre

Les requêtes de forme libre, avec ou sans opérateurs, permettent de simuler les requêtes définies par l’utilisateur envoyées à partir d'une application personnalisée vers Recherche Azure. Notez que lorsque vous indiquez des termes ou expressions de requête, le classement de la recherche intervient. L'exemple suivant illustre une recherche de texte libre.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Résultats**

   Vous pouvez utiliser Ctrl+F pour rechercher des termes spécifiques dans les résultats.

   ![Exemple de requête de texte libre](./media/search-explorer/search-explorer-example-freetext.png "Exemple de requête de texte libre")

### <a name="example-3---count-of-matching-documents"></a>Exemple 3 : nombre de documents correspondants 

Ajoutez **$count** pour obtenir le nombre de correspondances trouvées dans un index. Dans une recherche vide, ce nombre correspond au nombre total de documents dans l’index. Dans une recherche qualifiée, il correspond au nombre de documents correspondant à l’entrée de requête.

   ```Input1
   $count=true
   ```
   **Résultats**

   ![Exemple de nombre de documents](./media/search-explorer/search-explorer-example-count.png "Nombre de documents correspondants dans l'index")

### <a name="example-4---restrict-fields-in-search-results"></a>Exemple 4 : restreindre les champs dans les résultats de la recherche

Ajoutez **$select** pour limiter les résultats aux champs explicitement nommés et disposer d'une sortie plus lisible dans l'**Explorateur de recherche**. Pour conserver la chaîne de recherche et **$count = true**, faites précéder les arguments de **&** . 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Résultats**

   ![Exemple de limitation des champs](./media/search-explorer/search-explorer-example-selectfield.png "Restreindre les champs dans les résultats de la recherche")

### <a name="example-5---return-next-batch-of-results"></a>Exemple 5 : renvoyer le jeu de résultats suivant

Recherche Azure renvoie les 50 premières correspondances selon le classement de la recherche. Pour obtenir le jeu de documents correspondants suivant, ajoutez **$top = 100 & $skip = 50** afin d'augmenter le jeu de résultats à 100 documents (50 par défaut, 1 000 maximum), en ignorant les 50 premiers documents. N'oubliez pas qu'il vous faut fournir des critères de recherche, terme ou expression de requête, pour obtenir des résultats classés. Notez que les scores de recherche diminuent au fil des résultats de la recherche.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Résultats**

   ![Résultats de la recherche par lot](./media/search-explorer/search-explorer-example-topskip.png "Renvoyer le lot suivant de résultats de la recherche")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filtrer les expressions (supérieur à, inférieur à, égal à)

Utilisez le paramètre **$filter** lorsque vous souhaitez spécifier des critères précis plutôt qu'une recherche de texte libre. Cet exemple recherche plus de 3 chambres :

   ```Input
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Résultats**

   ![Expression de filtre](./media/search-explorer/search-explorer-example-filter.png "Filtrer par critères")

## <a name="order-by-expressions"></a>Expressions OrderBy

Ajoutez **$orderby** pour trier les résultats selon un autre champ, à côté du score de recherche. Pour le tester, vous pouvez utiliser l'exemple d'expression suivant :

   ```Input
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Résultats**

   ![Expression OrderBy](./media/search-explorer/search-explorer-example-ordery.png "Modifier l’ordre de tri")

Les expressions **$filter** et **$orderby** sont des constructions OData. Pour plus d’informations, consultez l’article [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Syntaxe d’expression de filtre OData).

<a name="start-search-explorer"></a>

## <a name="how-to-start-search-explorer"></a>Démarrer l’Explorateur de recherche

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la page du service de recherche à partir du tableau de bord, ou [recherchez votre service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans la liste.

2. Dans la page Vue d’ensemble du service, cliquez sur **Explorateur de recherche**.

   ![Commande Explorateur de recherche dans le portail](./media/search-explorer/search-explorer-cmd2.png "Commande Explorateur de recherche dans le portail")

3. Sélectionnez l’index à interroger.

   ![Sélectionner l’index à interroger](./media/search-explorer/search-explorer-changeindex-se2.png "Sélectionner l’index")

4. Vous pouvez également définir la version d'API. Par défaut, la version d'API actuellement mise à la disposition générale est sélectionnée, mais vous pouvez choisir une préversion ou une API plus ancienne si la syntaxe que vous souhaitez utiliser est spécifique à la version.

5. Une fois l’index et la version d’API sélectionnés, entrez des termes de recherche ou des expressions de requête complètes dans la barre de recherche, puis cliquez sur **Rechercher**.

   ![Entrer des termes de recherche et cliquer sur Rechercher](./media/search-explorer/search-explorer-query-string-example.png "Entrer des termes de recherche et cliquer sur Rechercher")

Conseils de recherche dans l'**Explorateur de recherche** :

+ Les résultats sont renvoyés sous forme de documents JSON détaillés afin de vous permettre de les consulter dans leur intégralité. Vous pouvez utiliser des expressions de requête, comme indiqué dans les exemples, pour limiter les champs renvoyés.

+ Les documents sont composés de tous les champs marqués comme **récupérables** dans l’index. Pour visualiser les attributs d’index dans le portail, cliquez sur *realestate-us-sample* dans la liste **Index** de la page de vue d'ensemble de la recherche.

+ Les requêtes de forme libre, semblables à celles que vous entrez dans un navigateur web commercial, permettent de tester une expérience d'utilisateur final. Par exemple, dans le cas de l'exemple d'index realestate intégré, vous pourriez entrer « Seattle apartments lake washington », puis utiliser Ctrl+F pour rechercher des termes dans les résultats de la recherche. 

+ Les expressions de requête et de filtre doivent être articulées dans une syntaxe prise en charge par la Recherche Azure. Par défaut, cela correspond à une [syntaxe simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), mais vous pouvez également utiliser [Lucene complète](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) pour de plus puissantes requêtes. Les [expressions de filtre](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) présentent une syntaxe OData.


## <a name="next-steps"></a>Étapes suivantes

Les ressources suivantes fournissent des exemples et des informations supplémentaires concernant la syntaxe de requête.

 + [Syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Syntaxe de requête Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Exemples de requête Lucene](search-query-lucene-examples.md) 
 + [Syntaxe d’expression de filtre OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 
