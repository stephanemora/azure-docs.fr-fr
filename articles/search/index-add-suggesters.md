---
title: Ajouter des suggesteurs à un index Recherche Azure
description: Active les champs pour des actions de requêtes prédictives, où les suggestions de requête sont composées de champs dans un index Recherche Azure.
ms.date: 02/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 7128e4d3b0675775dc713451ef672b28a4991499
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269924"
---
# <a name="add-suggesters-to-an-azure-search-index"></a>Ajouter des suggesteurs à un index Recherche Azure

Un **suggesteur** est une construction Recherche Azure qui prend en charge la fonctionnalité [Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) de recherche lors de la saisie et la fonctionnalité [semi-automatique (préversion)](search-autocomplete-tutorial.md). Avant d'appeler l’API Suggestions, vous devez définir un **suggesteur** dans un index pour activer les suggestions sur des champs spécifiques.

Bien qu’un **suggesteur** possède plusieurs propriétés, il se compose principalement d'une collection de champs pour lesquels vous activez l'[API Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions). Par exemple, une application de voyage peut souhaiter activer la recherche prédictive sur des destinations, villes et attractions. Dans un tel scénario, ces trois champs rejoindraient la collection de champs.

Vous ne pouvez disposer que d'une seule ressource **suggesteur** par index (plus précisément, un **suggesteur** dans la collection **suggesteurs**).

## <a name="creating-a-suggester"></a>Création d’un suggesteur 

Vous pouvez créer un **suggesteur** à tout moment, mais l’impact sur votre index varie en fonction des champs. 

+ Les nouveaux champs ajoutés à un suggesteur dans le cadre de la même mise à jour ont moins d'impact du fait qu'il n'est pas nécessaire de regénérer l'index.
+ Cela étant, l’ajout de champs existants à un suggesteur modifie la définition des champs, ce qui nécessite une regénération complète de l’index.

 Les **suggesteurs** fonctionnent de façon optimale quand ils sont utilisés pour suggérer des documents spécifiques plutôt que des expressions ou des termes isolés. Les champs les plus appropriés sont les titres, les noms et d’autres expressions relativement courtes qui peuvent identifier un élément. Les champs les moins efficaces sont les champs répétitifs, tels que les catégories et les balises, ou les champs très longs, tels que les champs des descriptions ou des commentaires.  

Après avoir créé un suggesteur, ajoutez l'[API Suggestions](https://docs.microsoft.com/rest/api/searchservice/suggestions) à votre logique de requête pour appeler la fonctionnalité.  

Les propriétés suivantes définissent un **suggesteur** :  

|Propriété|Description|  
|--------------|-----------------|  
|`name`|Nom du **suggesteur**. Vous utilisez le nom du **suggesteur** lors de l’appel de [Suggestions &#40;API REST du service Recherche Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions).|  
|`searchMode`|Stratégie utilisée pour rechercher des expressions candidates. Le seul mode actuellement pris en charge est `analyzingInfixMatching`, qui effectue une correspondance flexible des expressions en début ou au milieu des phrases.|  
|`sourceFields`|Liste d’un ou de plusieurs champs constituant la source du contenu pour des suggestions. Seuls les champs de type `Edm.String` et `Collection(Edm.String)` peuvent être des sources pour des suggestions. Seuls les champs qui n’ont pas un analyseur de langue personnalisé défini peuvent être utilisés. |  

## <a name="suggester-example"></a>Exemple de suggesteur  
 Un **suggesteur** fait partie de la définition de l’index. Un seul **suggesteur** peut exister dans la collection de **suggesteurs** de la version actuelle, à côté de la collection des **champs** et de **scoringProfiles**.  

```  
{  
  "name": "hotels",  
  "fields": [  
     . . .   
   ],  
  "suggesters": [  
    {  
    "name": "sg",  
    "searchMode": "analyzingInfixMatching",  
    "sourceFields": ["hotelName", "category"]  
    }  
  ],  
  "scoringProfiles": [  
     . . .   
  ]  
}  

```  

## <a name="see-also"></a>Voir aussi  
 [Créer un index &#40;API REST du service Recherche Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [Mettre à jour un index &#40;API REST du service Recherche Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/update-index)   
 [Suggestions &#40;API REST du service Recherche Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions)   
 [Opérations d'index &#40;API REST du service Recherche Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/index-operations)   
 [API REST du service Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/)   
 [Kit de développement logiciel .NET (SDK) Azure Search](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
