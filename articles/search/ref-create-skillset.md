---
title: Création d’un ensemble de compétences (API REST, api-version=2017-11-11-Preview) - Recherche Azure | Microsoft Docs
description: Un ensemble de compétences est une collection de compétences cognitives qui composent un pipeline d’enrichissement.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a5277d4137ede5fe6dacf993413eefd7c9e46ad4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786908"
---
# <a name="create-skillset-api-version2017-11-11-preview"></a>Création d’un ensemble de compétences (api-version=2017-11-11-Preview)

**S’applique à :** api-version=2017-11-11-Preview

Un ensemble de compétences est une collection de compétences cognitives utilisées pour le traitement du langage naturel et d’autres transformations. Les compétences incluent l’extraction d’entités nommées, l’extraction de phrases clés, la segmentation d’un texte en pages logiques, entre autres.

Pour utiliser l’ensemble de compétences, référencez-le dans un indexeur Recherche Azure, puis exécutez l’indexeur pour importer des données, appeler des transformations et un enrichissement et mapper les champs de sortie sur un index. Un ensemble de compétences est une ressource générale, mais il est opérationnel uniquement dans le traitement de l’indexeur. En tant que ressource générale, vous pouvez concevoir un ensemble de compétences une seule fois, puis le référencer dans plusieurs indexeurs. 

Un ensemble de compétences est exprimé dans Recherche Azure par le biais d’une demande HTTP PUT or POST. Pour PUT, le corps de la demande est un schéma JSON qui spécifie les compétences appelées. Les compétences sont chaînées par le biais d’associations entrée/sortie, où la sortie d’une transformation devient l’entrée d’une autre.

Un ensemble de compétences doit avoir au moins une compétence. Il n’existe aucune limite théorique quant au nombre maximal de compétences, mais entre trois et cinq sont une configuration courante.  


> [!NOTE]
> La recherche cognitive est en préversion publique, et l’exécution de l’ensemble de compétences est actuellement offerte gratuitement. Le prix de cette fonctionnalité sera annoncé à une date ultérieure.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```  

## <a name="request"></a>Requête  
 Le protocole HTTPS est requis pour toutes les requêtes de service. Vous pouvez construire la demande de **création d’un ensemble de compétences** à l’aide d’une méthode PUT, en indiquant le nom des compétences dans l’URL. Si l’ensemble de compétences n’existe pas, il est créé. S'il existe déjà, il est mis à jour en fonction de la nouvelle définition. Notez que vous ne pouvez utiliser la méthode PUT que pour un seul ensemble de compétences à la fois.  

 Le nom de l’ensemble de compétences doit répondre aux exigences suivantes :

- Être en minuscules
- Commencer et se terminer par une lettre ou un chiffre
- Ne pas avoir de barres obliques ou points
- Comporter moins de 128 caractères 

Après la lettre ou le chiffre du début, le nom de l’ensemble de compétences peut comprendre des lettres, des chiffres et des tirets (non consécutifs).  

Le paramètre **api-version** est obligatoire. La seule version disponible est `2017-11-11-Preview`. Consultez [Versions d’API dans Azure Search](https://go.microsoft.com/fwlink/?linkid=834796) pour obtenir la liste de toutes les versions. 


### <a name="request-headers"></a>En-têtes de requête  

 Le tableau suivant décrit les en-têtes de demande obligatoires et facultatifs.  

|En-tête de requête|Description|  
|--------------------|-----------------|  
|*Content-Type :*|Requis. À définir avec la valeur `application/json`|  
|*api-key :*|Requis. L'en-tête `api-key` est utilisé pour authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de type chaîne de caractères, unique pour votre service. La demande de **création d’un ensemble de compétences** doit inclure un en-tête `api-key` défini sur votre clé d’administration (par opposition à une clé de requête).|  

Vous avez besoin du nom du service pour construire l’URL de demande. Vous pouvez obtenir le nom du service et l’en-tête `api-key` à partir de votre tableau de bord de service dans le portail Azure. Pour obtenir de l'aide sur la navigation dans les pages, consultez [Création d'un service Azure Search dans le portail](search-create-service-portal.md) .  

### <a name="request-body-syntax"></a>Syntaxe du corps de la demande  

Le corps de la demande contient la définition de l’ensemble de compétences, composée d’une ou plusieurs compétences entièrement spécifiées, ainsi des paramètres de nom et de description facultatifs.  

Vous trouverez ci-dessous la syntaxe de structuration de la charge utile de la requête. Un exemple de demande est fourni plus loin dans cet article, ainsi que dans [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md).  

```
{   
    "name" : "Required for POST, optional for PUT. Friendly name of the skillset",  
    "description" : "Optional. Anything you want, or null",  
    "Skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",  
}  
```

### <a name="request-example"></a>Exemple de demande
 L’exemple suivant crée un ensemble de compétences utilisé pour l’enrichissement d’une collection de documents financiers.

```http
PUT https://[servicename].search.windows.net/skillsets/financedocenricher?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

Le corps de la demande est un document JSON. Cet ensemble de compétences particulier utilise deux compétences de façon asynchrone, qui traitent indépendamment la substance du `contents` comme s’il s’agissait de deux transformations différentes. Vous pouvez également faire de la sortie d’une transformation l’entrée d’une autre. Pour plus d’informations, consultez [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md).

```json
{
  "name": "financedocenricher",
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ]
}
```

## <a name="response"></a>response  

 Pour que votre demande aboutisse, vous devez voir le code d’état « 201 créé ».  

 Par défaut, le corps de la réponse contient le code JSON de la définition de l’ensemble de compétences qui a été créée. Toutefois, si l’en-tête de demande Prefer est défini avec la valeur return=minimal, le corps de la réponse est vide et le code d’état de réussite est « 204 Pas de contenu » au lieu de « 201 Créé ». Cela est vrai, quelle que soit la méthode utilisée (PUT ou POST) pour créer l’ensemble de compétences.   

## <a name="see-also"></a>Voir aussi

+ [Vue d’ensemble de la recherche cognitive](cognitive-search-concept-intro.md)
+ [Démarrage rapide : Essayer la recherche cognitive](cognitive-search-quickstart-blob.md)
+ [Tutoriel : Indexation enrichie des objets BLOB Azure](cognitive-search-tutorial-blob.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Guide pratique pour mapper des champs](cognitive-search-output-field-mapping.md)
+ [Guide pratique pour définir une interface personnalisée](cognitive-search-custom-skill-interface.md)
+ [Exemple : création d’une compétence personnalisée](cognitive-search-create-custom-skill-example.md)
+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)