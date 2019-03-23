---
title: Créer, charger et interroger un index à l’aide de PowerShell et l’API REST - recherche Azure
description: Créer, charger et interroger un index à l’aide de PowerShell, Invoke-RestMethod et l’API REST Azure Search.
ms.date: 03/15/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 9e1b6fc0dc4e6a6c2c191960fa061c810e3a2e79
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372112"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell-and-the-rest-api"></a>Démarrage rapide : Créer un index Azure Search à l’aide de PowerShell et l’API REST
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Portal](search-create-index-portal.md)
> 

Cet article vous guide dans le processus de création, de charger et d’interroger une recherche Azure [index](search-what-is-an-index.md) à l’aide de PowerShell et le [API REST de Service Azure Search](https://docs.microsoft.com/rest/api/searchservice/). La définition d’index et le contenu de recherche est fourni dans le corps de la demande en tant qu’un contenu JSON correct.

## <a name="prerequisites"></a>Conditions préalables

[Créer un service Azure Search](search-create-service-portal.md) ou [trouver un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sous votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce démarrage rapide. Autres conditions préalables incluent les éléments suivants.

[PowerShell 5.1 ou version ultérieure](https://github.com/PowerShell/PowerShell), à l’aide [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) pour connaître les étapes séquentielles et interactives.

Obtenir le point de terminaison URL et l’administrateur la clé api de votre service de recherche. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté votre abonnement à la fonction Recherche Azure, procédez comme suit pour obtenir les informations nécessaires :

1. Dans le portail Azure, dans votre service de recherche **vue d’ensemble** page, obtenez l’URL. Un point de terminaison exemple peut se présenter comme https :\//my-service-name.search.windows.net.

2. Dans **paramètres** > **clés**, obtenir une clé d’administration pour les droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour la continuité au cas où vous deviez interchanger. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, modification et suppression d’objets.

   ![Obtenir une clé d’accès et le point de terminaison HTTP](media/search-fiddler/get-url-key.png "obtenir une clé d’accès et le point de terminaison HTTP")

   Toutes les demandes nécessitent une clé d’api sur chaque demande envoyée à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

## <a name="connect-to-azure-search"></a>Se connecter à recherche Azure

Dans PowerShell, créez un **$headers** objet à stocker le content-type et la clé d’API. Vous devez uniquement définir cet en-tête qu’une seule fois pendant la durée de la session, mais vous allez l’ajouter à chaque requête. 

```powershell
$headers = @{
   'api-key' = '<your-admin-api-key>'
   'Content-Type' = 'application/json' 
   'Accept' = 'application/json' }
```

Créer un **$url** objet qui spécifie le service indexe la collection. Le `mydemo` nom du service est conçu comme un espace réservé. Remplacez-le par un service de recherche valide dans un abonnement actif tout au long de cet exemple.

```powershell
$url = "https://mydemo.search.windows.net/indexes?api-version=2017-11-11"
```

Exécutez **Invoke-RestMethod** pour envoyer une demande GET au service et de vérifier la connexion. Ajouter **ConvertTo-Json** afin que vous puissiez afficher les réponses envoyées vers le service.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

Si le service est vide et ne possède aucun index, les résultats sont similaires à l’exemple suivant. Sinon, vous verrez une représentation JSON de définitions d’index.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
    "value":  [

              ]
}
```

## <a name="1---create-an-index"></a>1 - Créer un index

Sauf si vous utilisez le portail, un index doit exister sur le service avant de pouvoir charger des données. Cette étape définit l’index et l’intègre au service. Le [Create Index (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index) est utilisée pour cette étape.

Les éléments requis d’un index incluent un nom et une collection de champs. La collection fields définit la structure d’un *document*. Chaque champ a un nom, type et les attributs qui déterminent la façon dont il est utilisé (par exemple, si elle est en texte intégral consultable, filtrable ou récupérables dans les résultats de la recherche). Dans un index, l’un des champs de type `Edm.String` doit être désigné comme le *clé* pour l’identité du document.

Cet index est nommé « hotels » et a les définitions de champ que vous voyez ci-dessous. La définition d’index spécifie un [analyseur linguistique](index-add-language-analyzers.md) pour le `description_fr` champ, car il est destiné à stocker du texte Français, que nous allons ajouter dans un autre exemple.

Collez cet exemple dans PowerShell pour créer un **$body** objet contenant le schéma d’index.

```powershell
$body = @"
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
"@
```

Définir l’URI à la collection d’index sur votre service et le *hôtels* index.

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11"
```

Exécutez la commande avec **$url**, **$headers**, et **$body** pour créer l’index sur le service. 

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
```
Résultats doivent ressembler à ceci (tronquée pour les deux premiers champs par souci de concision) :

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag":  "\"0x8D6A99E2DED96B0\"",
    "name":  "hotels",
    "defaultScoringProfile":  null,
    "fields":  [
                   {
                       "name":  "hotelId",
                       "type":  "Edm.String",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  false,
                       "facetable":  false,
                       "key":  true,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
                   {
                       "name":  "baseRate",
                       "type":  "Edm.Double",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  true,
                       "facetable":  true,
                       "key":  false,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
. . .
```

> [!Tip]
> Pour la vérification, vous pourrez également vérifier la liste des index dans le portail, ou réexécutez la commande utilisée pour vérifier la connexion de service pour voir les *hôtels* index répertoriés dans la collection d’index.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - charger des documents

Pour transmettre des documents, utilisez une requête HTTP POST au point de terminaison URL de votre index. L’API REST pour cette tâche est [Ajout, mise à jour ou supprimer des Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

Collez cet exemple dans PowerShell pour créer un **$body** objet contenant les documents que vous souhaitez télécharger. 

Cette demande comprend deux intégral et un seul enregistrement partiels. L’enregistrement partielle montre que vous pouvez télécharger des documents incomplètes. Le `@search.action` paramètre spécifie la façon dont l’indexation est effectuée. Les valeurs valides incluent le téléchargement, fusion, mergeOrUpload et delete. Le comportement mergeOrUpload crée un nouveau document hotelId = 3, ou met à jour le contenu s’il existe déjà.

```powershell
$body = @"
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
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
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        }
    ]
}
"@
```

La valeur est le point de terminaison le *hôtels* collection de documents et inclure l’opération d’index (index/hotels/docs/index).

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11"
```

Exécutez la commande avec **$url**, **$headers**, et **$body** pour charger des documents dans l’index des hôtels.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
```
Résultats doivent ressembler à l’exemple suivant. Vous devriez voir un code d’état de 201. Pour obtenir une description de tous les codes d’état, consultez [codes d’état HTTP (recherche Azure)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2017_11_11.IndexResult)",
    "value":  [
                  {
                      "key":  "1",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  },
                  {
                      "key":  "2",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  },
                  {
                      "key":  "3",
                      "status":  true,
                      "errorMessage":  null,
                      "statusCode":  201
                  }
              ]
}
```

## <a name="3---search-an-index"></a>3 - Rechercher dans un index

Cette étape vous montre comment interroger un index à l’aide de la [recherche Documents API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

La valeur est le point de terminaison le *hôtels* collection de documents et ajoutez un **recherche** paramètre pour inclure les chaînes de requête. Cette chaîne est une recherche vide et retourne une liste unranked de tous les documents.

```powershell
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*'
```

Exécutez la commande pour envoyer le **$url** au service.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

Résultats doivent ressembler à la sortie suivante.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#docs(*)",
    "value":  [
                  {
                      "@search.score":  1.0,
                      "hotelId":  "1",
                      "baseRate":  199.0,
                      "description":  "Best hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Fancy Stay",
                      "category":  "Luxury",
                      "tags":  "pool view wifi concierge",
                      "parkingIncluded":  false,
                      "smokingAllowed":  false,
                      "lastRenovationDate":  "2010-06-27T00:00:00Z",
                      "rating":  5,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "2",
                      "baseRate":  79.99,
                      "description":  "Cheapest hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Roach Motel",
                      "category":  "Budget",
                      "tags":  "motel budget",
                      "parkingIncluded":  true,
                      "smokingAllowed":  true,
                      "lastRenovationDate":  "1982-04-28T00:00:00Z",
                      "rating":  1,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "3",
                      "baseRate":  129.99,
                      "description":  "Close to town hall and the river",
                      "description_fr":  null,
                      "hotelName":  null,
                      "category":  null,
                      "tags":  "",
                      "parkingIncluded":  null,
                      "smokingAllowed":  null,
                      "lastRenovationDate":  null,
                      "rating":  null,
                      "location":  null
                  }
              ]
}
```

Essayez quelques autres exemples de requête pour obtenir un aperçu de la syntaxe. Vous pouvez effectuer une recherche de chaîne, les requêtes $filter textuelle, limiter le jeu de résultats de la portée de la recherche à des champs spécifiques et bien plus encore.

```powershell
# Query example 1
# Search the entire index for the term 'budget'
# Return only the `hotelName` field, "Roach hotel"
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=budget&$select=hotelName'

# Query example 2 
# Apply a filter to the index to find hotels cheaper than $150 per night
# Returns the `hotelId` and `description`. Two documents match.
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$filter=baseRate lt 150&$select=hotelId,description'

# Query example 3
# Search the entire index, order by a specific field (`lastRenovationDate`) in descending order
# Take the top two results, and show only `hotelName` and `lastRenovationDate`
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate'
```
## <a name="clean-up"></a>Nettoyer 

Vous devez supprimer l’index si vous n’avez plus besoin. Un service gratuit est limité à trois index. Vous souhaiterez peut-être supprimer n’importe quel index que vous n’utilisez pas activement afin que vous pouvez parcourir d’autres didacticiels.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>Étapes suivantes

Essayez d’ajouter des descriptions Français à l’index. L’exemple suivant inclut des chaînes Français et montre les actions de recherche supplémentaires. Utilisez mergeOrUpload pour créer ou ajouter des champs existants. Les chaînes suivantes doivent être encodé en UTF-8.

```json
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "1",
            "description_fr": "Meilleur hôtel en ville"
        },
        {
            "@search.action": "merge",
            "hotelId": "2",
            "description_fr": "Hôtel le moins cher en ville"
        }
    ]
}
```
