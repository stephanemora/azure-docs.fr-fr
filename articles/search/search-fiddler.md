---
title: 'Démarrage rapide : Postman et les API REST – Recherche Azure'
description: Découvrez comment appeler les API REST de Recherche Azure à l’aide de Postman ainsi que d’exemples de données et de définitions.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: eaf594286e5ffc101ad2d24e808fcb806998d053
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66471559"
---
# <a name="quickstart-explore-azure-search-rest-apis-using-postman"></a>Démarrage rapide : Explorer les API REST de la Recherche Azure avec Postman
> [!div class="op_single_selector"]
> * [Postman](search-fiddler.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

Une des méthodes les plus simples permettant d’explorer les [API REST de Recherche Azure](https://docs.microsoft.com/rest/api/searchservice) consiste à utiliser Postman pour formuler des requêtes HTTP et inspecter les réponses. Avec les bons outils et ces instructions, vous pouvez envoyer des requêtes et afficher les réponses avant d’écrire un code.

Si vous ne disposez d’aucun abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer, puis [inscrivez-vous auprès de la fonction Recherche Azure](search-create-service-portal.md).

## <a name="prerequisites"></a>Prérequis

Les services et les outils qui suivent sont utilisés dans ce guide de démarrage rapide. 

+ [Créez un service Recherche Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce guide de démarrage rapide. 

+ L’[application de bureau Postman](https://www.getpostman.com/) ou [Telerik Fiddler](https://www.telerik.com/fiddler) sont utilisés pour envoyer des requêtes à la Recherche Azure.

## <a name="get-a-key-and-url"></a>Obtenir une clé et une URL

Les appels REST requièrent l’URL du service et une clé d’accès et ce, sur chaque demande. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté votre abonnement à la fonction Recherche Azure, procédez comme suit pour obtenir les informations nécessaires :

1. [Connectez-vous au portail Azure](https://portal.azure.com/), puis dans la page **Vue d’ensemble** du service de recherche, récupérez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

1. Dans **Paramètres** > **Clés**, obtenez une clé d’administration pour avoir des droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour assurer la continuité de l’activité au cas où vous deviez en remplacer une. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, de modification et de suppression d’objets.

![Obtenir une clé d’accès et un point de terminaison HTTP](media/search-fiddler/get-url-key.png "Obtenir une clé d’accès et un point de terminaison HTTP")

Toutes les demandes nécessitent une clé API sur chaque demande envoyée à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

## <a name="connect-to-azure-search"></a>Se connecter à la Recherche Azure

Dans cette section, utilisez l’outil web de votre choix pour configurer les connexions à la Recherche Azure. Chaque outil conserve les informations d’en-tête de requête de la session, ce qui signifie que vous n’avez à indiquer la clé d’API et le type de contenu qu’une seule fois.

Quel que soit l’outil, vous devez choisir une commande (GET, POST, PUT, etc.), fournir un point de terminaison d’URL et, pour certaines tâches, fournir du code JSON dans le corps de la requête. Remplacez le nom du service de recherche (YOUR-SEARCH-SERVICE-NAME) par une valeur valide. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06

Remarquez le préfixe HTTPS, le nom du service, le nom de l’objet (dans ce cas, la collection d’index) et la [version de l’API](search-api-versions.md). La version de l'API est une chaîne en minuscules obligatoire, spécifiée au format `?api-version=2019-05-06` pour la version actuelle. Les versions d’API sont régulièrement mises à jour. Le fait d’inclure la version d’API sur chaque demande vous permet de bénéficier du contrôle absolu sur la version utilisée.  

Un en-tête de requête est composé de deux éléments, à savoir le type de contenu et la clé d’API utiliser pour s’authentifier auprès de la Recherche Azure. Remplacez la clé API de l’administrateur (YOUR-ADMIN-API-KEY) par une valeur valide. 

    api-key: <YOUR-ADMIN-API-KEY>
    Content-Type: application/json

Dans Postman, formulez une requête similaire à celle de la capture d’écran suivante. Choisissez **GET** comme verbe, indiquez l’URL, puis cliquez sur **Envoyer**. Cette commande établit la connexion à la Recherche Azure, lit la collection d’index et retourne le code d’état HTTP 200 en cas de connexion réussie. Si votre service a déjà des index, la réponse inclut également les définitions de ces index.

![En-tête de demande Postman][6]

## <a name="1---create-an-index"></a>1 – Créer un index

Dans la Recherche Azure, vous créez généralement les index avant de charger les données. L’[API REST Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) est utilisée pour cette tâche. 

L’URL est étendue pour inclure le nom d’index `hotels`.

Pour faire cela dans Postman :

1. Remplacez le verbe par **PUT**.

2. Copiez dans cette URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels?api-version=2019-05-06`.

3. Fournissez la définition d’index (ci-dessous) dans le corps de la requête.

4. Cliquez sur **Envoyer**.

![Corps de la demande Postman][8]

### <a name="index-definition"></a>Définition de l’index

La collection de champs définit la structure du document. Chaque document doit comporter ces champs, et chaque champ doit avoir un type de données. Les champs de chaîne sont utilisés lors de recherches en texte intégral. Vous devrez peut-être transtyper les données numériques sous forme de chaînes, pour qu’il soit possible d’effectuer des recherches dans le contenu.

Les attributs du champ déterminent l’action autorisée. Les API REST autorisent de nombreuses actions par défaut. Par exemple, toutes les chaînes peuvent être soumises à des recherches, récupérables et filtrables et sont à choix multiples par défaut. Bien souvent, il suffit de définir des attributs pour désactiver un comportement.

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }

Lorsque vous envoyez cette demande, vous devez obtenir une réponse HTTP 201, indiquant que l’index a bien été créé. Vous pouvez vérifier cette action dans le portail, mais n’oubliez pas que la page du portail est actualisée à intervalles réguliers. Il se peut donc que le résultat de l’action s’affiche au bout d’une minute ou deux.

> [!TIP]
> Si vous obtenez HTTP 504, vérifiez que l'URL spécifie HTTPS. Si vous voyez HTTP 400 ou 404, contrôlez le corps de la demande pour vérifier l'absence d'erreurs de copier-coller. HTTP 403 indique normalement qu'il y a un problème avec la clé API (soit la clé n'est pas valide, soit il y a un problème de syntaxe avec la façon dont elle est spécifiée).

## <a name="2---load-documents"></a>2 – Charger des documents

Les étapes de création et de remplissage d’index sont des opérations distinctes. Dans la fonction Recherche Azure, l’index contient toutes les données pouvant faire l’objet de recherches, que vous pouvez fournir sous forme de documents JSON. L’[API REST Add, Update ou Delete Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) est utilisée pour cette tâche. 

L’URL est étendue pour inclure les collections `docs` et l’opération `index`.

Pour faire cela dans Postman :

1. Remplacez le verbe par **POST**.

2. Copiez dans cette URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs/index?api-version=2019-05-06`.

3. Fournissez les documents JSON (ci-dessous) dans le corps de la requête.

4. Cliquez sur **Envoyer**.

![En-tête de la demande Postman][10]

### <a name="json-documents-to-load-into-the-index"></a>Documents JSON à charger dans l’index

Le corps de la demande contient quatre documents à ajouter à l'index des hôtels.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "description_fr": "Meilleur hôtel en ville",
             "hotelName": "Fancy Stay",
             "category": "Luxury",
             "tags": ["pool", "view", "wifi", "concierge"],
             "parkingIncluded": false,
             "smokingAllowed": false,
             "lastRenovationDate": "2010-06-27T00:00:00Z",
             "rating": 5,
             "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "2",
             "baseRate": 79.99,
             "description": "Cheapest hotel in town",
             "description_fr": "Hôtel le moins cher en ville",
             "hotelName": "Roach Motel",
             "category": "Budget",
             "tags": ["motel", "budget"],
             "parkingIncluded": true,
             "smokingAllowed": true,
             "lastRenovationDate": "1982-04-28T00:00:00Z",
             "rating": 1,
             "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

Après quelques secondes, la réponse HTTP 200 apparaît dans la liste de sessions. Cela indique que les documents ont été correctement créés. 

Si vous obtenez HTTP 207, cela signifie qu'au moins un document n'a pas pu être chargé. Si l’erreur HTTP 404 s’affiche, cela signifie que vous avez fait une erreur de syntaxe dans l’en-tête ou le corps de la demande. Vérifiez que le point de terminaison a bien été modifié de manière à inclure `/docs/index`.

> [!Tip]
> Pour les sources de données sélectionnées, vous pouvez choisir l’approche *d’indexeur*, qui simplifie et réduit la quantité de code nécessaire pour l’indexation. Pour en savoir plus, consultez la section relative aux [opérations de l’indexeur](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3 – Rechercher dans un index

Maintenant qu’un index et des documents sont chargés, vous pouvez émettre des requêtes les concernant à l’aide de l’[API REST Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents).

L’URL est étendue pour inclure une chaîne de requête spécifiée à l’aide de l’opérateur de recherche.

Pour faire cela dans Postman :

1. Remplacez le verbe par **GET**.

2. Copiez dans cette URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2019-05-06`.

3. Cliquez sur **Envoyer**.

Cette requête effectue des recherches sur la base du mot « motel » et renvoie un certain nombre de documents dans les résultats de recherche. La requête et la réponse doivent ressembler à la capture d’écran suivante pour Postman, une fois que vous avez cliqué sur **Envoyer**. Le code d’état doit être 200.

 ![Réponse à la requête Postman][11]


## <a name="get-index-properties"></a>Obtenez les propriétés de l’index
Vous pouvez également interroger les informations du système pour connaître le nombre de documents et l’espace de stockage utilisé : `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/stats?api-version=2019-05-06`

Dans Postman, votre requête doit ressembler à ce qui suit ; la réponse inclut un nombre de documents et la quantité d’espace utilisé, en octets.

 ![Requête système Postman][12]

Notez que la syntaxe de la version d’API diffère. Pour cette requête, utilisez `?` pour ajouter la version d’API. `?` sépare le chemin de l’URL de la chaîne de requête, alors que le signe & sépare chaque paire « nom=valeur » dans la chaîne de requête. Pour cette requête, la version d’API est le premier et seul élément de la chaîne de requête.

Pour en savoir plus sur cette API, consultez [API REST Obtenir des statistiques d’index](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).


## <a name="use-fiddler"></a>Utiliser Fiddler

Cette section est équivalente aux sections précédentes, mais présente les captures d’écran et les instructions concernant Fiddler.

### <a name="connect-to-azure-search"></a>Se connecter à la Recherche Azure

Formulez une requête comparable à la capture d’écran suivante. Choisissez le verbe **GET**. Fiddler ajoute la chaîne `User-Agent=Fiddler`. Vous pouvez coller les deux en-têtes de demande supplémentaires sur les nouvelles lignes en dessous. Incluez le type de contenu et la clé d’API de votre service, en utilisant la clé d’accès d’administrateur pour votre service.

Pour la cible, copiez une version modifiée de cette URL : `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06`

![En-tête de demande Fiddler][1]

> [!Tip]
> Désactivez le trafic web pour masquer l’activité HTTP extérieure non liée. Dans le menu **Fichier** de Fiddler, désactivez l’option **Capturer le trafic**. 

### <a name="1---create-an-index"></a>1 – Créer un index

Remplacez le verbe par **PUT**. Copiez une version modifiée de cette URL : `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels?api-version=2019-05-06`. Copiez la définition d’index fournie ci-dessus dans le corps de la requête. Votre page doit ressembler à la capture d’écran suivante. Cliquez sur **Exécuter** dans le coin supérieur droit pour envoyer la requête terminée.

![Corps de la demande Fiddler][7]

### <a name="2---load-documents"></a>2 – Charger des documents

Remplacez le verbe par **POST**. Modifiez l’URL pour inclure `/docs/index`. Copiez les documents dans le corps de la demande, comme dans la capture d’écran suivante, puis exécutez la requête.

![Charge utile de la demande Fiddler][9]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Conseils relatifs à l’exécution de nos exemples de requêtes dans Fiddler

L’exemple de requête suivant provient de l’article [API REST Search Documents](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Plusieurs exemples de requêtes de cette rubrique comportent des espaces, qui ne sont pas autorisés dans Fiddler. Remplacez chaque espace par un caractère + avant de coller la chaîne de requête et d’essayer la requête dans Fiddler.

**Avant le remplacement des espaces (dans la description de lastRenovationDate) :**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06

**Avant le remplacement des espaces (dans lastRenovationDate+desc) :**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2019-05-06

### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Conseils pour l’affichage des statistiques d’index dans Fiddler

Dans Fiddler, cliquez sur l’onglet **Inspecteurs**, sur l’onglet **En-têtes**, puis sélectionnez le format JSON. Le nombre de documents et la taille de stockage (en Ko) doivent s'afficher.

## <a name="next-steps"></a>Étapes suivantes

Les clients REST sont très utiles pour l’exploration ad hoc, mais vous connaissez le fonctionnement de l’API REST, donc vous pouvez avancer avec le code. Pour connaître les étapes suivantes, consultez les liens suivants :

+ [Démarrage rapide : Créer un index à l’aide du Kit de développement logiciel .NET](search-create-index-dotnet.md)
+ [Démarrage rapide : Créer un index (REST) à l’aide de PowerShell](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png
