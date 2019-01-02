---
title: Attacher Cognitive Services à votre ensemble de qualifications - Recherche Azure
description: Instructions à suivre pour attacher un abonnement Cognitive Services tout-en-un à un ensemble de qualifications cognitives
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7f604d1b94e51db11e6d6992b7f070d64ae869dd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317234"
---
# <a name="associate-cognitive-services-resource-with-a-skillset"></a>Associer une ressource Cognitive Services à un ensemble de qualifications 

> [!NOTE]
> À compter du 21 décembre 2018, vous pouvez associer une ressource Cognitive Services à un ensemble de qualifications Recherche Azure. Cela nous permet de commencer la facturation pour l’exécution des ensembles de qualifications. Ce jour-là, nous commencerons également à facturer l’extraction d’images dans le cadre de notre étape de décodage de documents. L’extraction de texte à partir de documents continuera d’être offerte sans frais supplémentaires.
>
> L’exécution des compétences intégrées sera facturée au prix actuel du [paiement à l’utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Les tarifs de l’extraction d’images sont ceux de la préversion. Ils sont décrits dans la page [Tarification de Recherche Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


La recherche cognitive extrait et enrichit les données pour qu’elles puissent faire l’objet de recherches dans Recherche Azure. Nous appelons les étapes d’extraction et d’enrichissement *qualifications cognitives*. Toutes les compétences appelées lors de l’indexation de contenu sont définies dans un *ensemble de qualifications*. Un jeu de compétences peut utiliser des [compétences prédéfinies](cognitive-search-predefined-skills.md) ou des compétences personnalisées. Pour plus d’informations, consultez [Example: create a custom skill](cognitive-search-create-custom-skill-example.md) (Exemple : créer une compétence personnalisée.

Dans cet article, vous allez apprendre à associer la ressource [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) à votre ensemble de qualifications cognitives.

La ressource Cognitive Services que vous sélectionnez alimentera les compétences cognitives intégrées. Cette ressource sera également utilisée à des fins de facturation. Tout enrichissement réalisé à l’aide des qualifications cognitives intégrées sera facturé selon la ressource Cognitive Services sélectionnée. Vous serez facturé au même tarif que si vous aviez effectué la même tâche à l’aide d’une ressource Cognitive Services. Consultez la page [Tarification Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Limites lorsqu’aucune ressource Cognitive Services n’est sélectionnée
À compter du 1er février 2019, si vous n’associez pas d’abonnement Cognitive Services à votre ensemble de qualifications, vous pourrez seulement enrichir un petit nombre de documents gratuitement (20 documents par jour). 

## <a name="associating-a-cognitive-services-resource-with-a-new-skillset"></a>Association d’une ressource Cognitive Services à un nouvel ensemble de qualifications

1. Dans le cadre de *l’importation des données*, après vous être connecté à votre source de données, vous pourrez accéder à l’étape facultative *Ajouter la recherche cognitive*. 

1. Développez la section *Attacher Cognitive Services*. Cela affiche toutes les ressources Cognitive Services se trouvant dans les mêmes régions que votre service de recherche Search Service. 
![Option Attacher Cognitive Services développée](./media/cognitive-search-attach-cognitive-services/attach1.png "Option Attacher Cognitive Services développée")

1. Sélectionnez une ressource Cognitive Services existante ou choisissez de *Créer une ressource Cognitive Services*. Si vous sélectionnez la *ressource gratuite (enrichissements limités)*, vous ne pourrez enrichir qu’un petit nombre de documents gratuitement (20 documents par jour). Si vous avez cliqué sur *Créer une ressource Cognitive Services*, un nouvel onglet s’ouvre. Il vous permet de créer la ressource Cognitive Services. 

1. Si vous avez créé une ressource, cliquez sur *Actualiser* pour rafraîchir la liste des ressources Cognitive Services, et sélectionnez la ressource. 
![Ressource Cognitive Services sélectionnée](./media/cognitive-search-attach-cognitive-services/attach2.png "Ressource Cognitive Services sélectionnée")

1. Ensuite, vous pouvez développer la section *Ajouter des enrichissements* pour sélectionner les qualifications cognitives spécifiques à exécuter sur vos données et poursuivre le processus.

## <a name="associating-a-cognitive-services-resource-with-an-existing-skillset"></a>Association d’une ressource Cognitive Services à un ensemble de qualifications existant

1. Dans la page Présentation du service, sélectionnez l’onglet *Ensembles de qualifications*. ![Onglet Ensemble de qualifications](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Onglet Ensemble de qualifications")

1. *Cliquez* sur l’ensemble de qualifications à modifier. Un panneau vous permettant de modifier un ensemble de qualifications s’ouvre.

1. Sélectionnez une ressource Cognitive Services existante ou choisissez de *Créer une ressource Cognitive Services*. Si vous sélectionnez la *ressource gratuite (enrichissements limités)*, vous ne pourrez enrichir qu’un petit nombre de documents gratuitement (20 documents par jour). Si vous avez cliqué sur *Créer une ressource Cognitive Services*, un nouvel onglet s’ouvre. Il vous permet de créer la ressource Cognitive Services. <n/> 
<img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Si vous avez créé une ressource, cliquez sur *Actualiser* pour rafraîchir la liste des ressources Cognitive Services, et sélectionnez la ressource.
1. Cliquez sur *OK* pour confirmer vos modifications

## <a name="associating-a-cognitive-services-resource-programmatically"></a>Association par programme d’une ressource Cognitive Services

Lorsque vous définissez l’ensemble de qualifications par programme, ajoutez une section `cognitiveServices`. Cette section doit inclure la clé de la ressource Cognitive Services que vous souhaitez associer à l’ensemble de qualifications, ainsi que l’annotation @odata.type, qui doit être définie sur « #Microsoft.Azure.Search.CognitiveServicesByKey ». Ce modèle est illustré dans l’exemple ci-dessous.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey"
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimating-the-cost-of-document-cracking-and-enrichment"></a>Exemple : Estimation du coût du décodage et de l’enrichissement de document
Vous voudrez peut-être estimer le coût de l’enrichissement d’un type de document donné. L’exercice ci-dessous est un exemple uniquement, mais il pourrait vous être utile.

Imaginez que nous disposions de 1 000 documents PDF. On estime que chacun comporte en moyenne 6 pages. Pour cet exercice, supposons que chacun d’eux dispose d’une image par page. Imaginons également qu’en moyenne, chaque page contient environ 3 000 caractères. 

Maintenant, supposons que nous souhaitions effectuer les étapes suivantes dans le cadre du pipeline d’enrichissement :
1. Dans le cadre du décodage de document, extrayez le contenu et les images du document.
1. Dans le cadre de l’enrichissement, passez chaque page extraite à la reconnaissance optique de caractères, combinez le texte de toutes les pages, puis extrayez chacune des organisations dans le texte combiné de toutes les images.

Nous allons estimer combien coûterait l’ingestion de ces documents, pas à pas.

Pour les 1 000 documents :

1. Décodage de document : nous aurions extrait un nombre cumulé de 6 000 images. En comptant 1 $ par lot de 1 000 images extraites, cela nous coûterait 6 $.

2. Nous passerions ensuite à l’extraction du texte de chacune de ces 6 000 images. En anglais, la qualification cognitive de reconnaissance optique de caractères (OCR) utilise le meilleur algorithme (DescribeText). À raison de 2,50 $ par lot de 1 000 images à analyser, cette étape nous coûterait 15 $.

3. Pour l’extraction d’entités, nous aurions un total de 3 enregistrements texte par page (chaque enregistrement contient 1 000 caractères). 3 enregistrements texte/page x 6 000 pages = 18 000 enregistrements texte. À raison de 2$ par lot de 1 000 enregistrements texte, cette étape nous coûterait 36 $.

En additionnant tout cela, l’ingestion de 1 000 documents PDF de cette nature avec l’ensemble de qualifications décrit reviendrait à 57 $.  Dans cet exercice, nous avons estimé le prix par transaction le plus élevé, il aurait pu être inférieur avec la tarification dégressive. Voir [Tarification Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>Étapes suivantes
+ [Page Tarification de Recherche Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Créer un jeu de compétences (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Guide pratique pour mapper des champs enrichis](cognitive-search-output-field-mapping.md)
