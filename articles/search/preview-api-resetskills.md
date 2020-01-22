---
title: Réinitialiser les compétences (api-version=2019-050-06-Preview)
titleSuffix: Azure Cognitive Search
description: La version préliminaire de l’API REST est utilisée pour l’enrichissement incrémentiel lorsque vous avez besoin d’un retraitement complet ou partiel d’un ensemble de compétences.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4557e966c431eca31aa7577bebc75caae9365dc9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832160"
---
# <a name="reset-skills-api-version2019-05-06-preview"></a>Réinitialiser les compétences (api-version=2019-05-06-Preview)

> [!IMPORTANT] 
> L’enrichissement incrémentiel est actuellement en version préliminaire publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). L’[API REST version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’y a pas de prise en charge de portail ou de SDK .NET pour l’instant.

La requête **Réinitialiser les compétences** spécifie les compétences à traiter lors de la prochaine exécution de l’indexeur. Pour les indexeurs pour lesquels la mise en cache est activée, vous pouvez demander explicitement le traitement des mises à jour de compétences que l’indexeur ne peut pas détecter. Par exemple, si vous apportez des modifications externes, telles que des révisions à une compétence personnalisée, vous pouvez utiliser cette API pour réexécuter la compétence. Les sorties, telles qu’une base de connaissances ou un index de recherche, sont actualisées à l’aide de données réutilisables provenant du cache et du nouveau contenu selon la compétence mise à jour.

Vous pouvez réinitialiser un [ensemble de compétences](https://docs.microsoft.com/rest/api/searchservice/create-skillset) existant à l’aide d’un HTTP PUT, en spécifiant le nom de l’ensemble de compétences à mettre à jour sur l’URI de requête. Vous devez utiliser **api-version=2019-05-06-Preview** sur la requête.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```  

Vous pouvez également utiliser POST et placer le nom de l’indexeur dans le corps de la requête :

```http
POST https://[service name].search.windows.net/indexers/resetskills?api-version=[api-version]  
api-key: [admin key]  
```  

## <a name="request-headers"></a>En-têtes de requête  

 Le tableau suivant décrit les en-têtes de demande obligatoires et facultatifs.  

|En-tête de requête|Description|  
|--------------------|-----------------|  
|*Content-Type :*|Obligatoire. À définir avec la valeur `application/json`|  
|*api-key :*|Obligatoire. L'en-tête `api-key` est utilisé pour authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de type chaîne de caractères, unique pour votre service. La requête **Réinitialiser l’ensemble de compétences** doit inclure un en-tête `api-key` défini sur votre clé d’administration (par opposition à une clé de requête).|  

Vous avez besoin du nom du service pour construire l’URL de demande. Vous pouvez obtenir le nom du service et `api-key` à partir de votre page de présentation de service dans le Portail Azure. Pour obtenir de l'aide sur la navigation dans les pages, consultez [Créer un service Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-create-service-portal).  

## <a name="request-body-syntax"></a>Syntaxe du corps de la demande  

Le corps de la requête est un groupe de noms de compétences.

```json
{   
    "skillNames" : ["<SKILL-1>", "SKILL-n"]
}  
```

## <a name="response"></a>response  

Code d’état : 204 Pas de contenu pour une réponse réussie. 

## <a name="see-also"></a>Voir aussi

+ [API REST de recherche](https://docs.microsoft.com/rest/api/searchservice)
+ [Codes d’état HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)  
+ [Vue d’ensemble de l’enrichissement de l’IA](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)
+ [Configurer la mise en cache et l’enrichissement incrémentiel](search-howto-incremental-index.md)
+ [Enrichissement incrémentiel](cognitive-search-incremental-indexing-conceptual.md)