---
title: Associer une ressource Cognitive Services à un ensemble de qualifications dans le service Recherche Azure
description: Instructions à suivre pour joindre un abonnement Cognitive Services tout-en-un à un ensemble de qualifications cognitives dans le service Recherche Azure
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 52efa685bba330879365f56e547881d62a52a185
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000153"
---
# <a name="associate-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Associer une ressource Cognitive Services à un ensemble de qualifications dans le service Recherche Azure 

La recherche cognitive extrait et enrichit les données pour qu’elles puissent faire l’objet de recherches dans Recherche Azure. Nous appelons les étapes d’extraction et d’enrichissement *qualifications cognitives*. Toutes les compétences appelées lors de l’indexation de contenu sont définies dans un *ensemble de qualifications*. Un ensemble de qualifications peut contenir des [qualifications prédéfinies](cognitive-search-predefined-skills.md) ou des qualifications personnalisées. Pour plus d’informations, consultez [Exemple : Créer une qualification personnalisée](cognitive-search-create-custom-skill-example.md).

Dans cet article, vous allez apprendre à associer une ressource [Azure Cognitive Services ](https://azure.microsoft.com/services/cognitive-services/) à votre ensemble de qualifications.

La ressource Cognitive Services que vous sélectionnez alimentera les qualifications cognitives intégrées. Cette ressource sera également utilisée à des fins de facturation. Tout enrichissement réalisé à l’aide des qualifications cognitives intégrées sera facturé selon la ressource Cognitive Services sélectionnée. Vous serez facturé au même tarif que si vous aviez effectué la même tâche à l’aide d’une ressource Cognitive Services. Consultez la page [Tarification Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

> [!NOTE]
> Depuis le 21 décembre 2018, vous pouvez associer une ressource Cognitive Services à un ensemble de qualifications du service Recherche Azure. Cela nous permet de facturer l’exécution d’un ensemble de qualifications. Ce jour-là, nous avons également commencé à facturer l’extraction d’images dans le cadre de notre étape de décodage de documents. L’extraction de texte à partir de documents est toujours offerte sans frais supplémentaires.
>
> L’exécution de qualifications intégrées est facturée selon le [paiement à l’utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Les tarifs de l’extraction d’images sont ceux de la préversion. Ils sont décrits dans la page [Tarification de Recherche Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Limites lorsqu’aucune ressource Cognitive Services n’est sélectionnée
À compter du 1er février 2019, si vous n’associez pas d’abonnement Cognitive Services à votre ensemble de qualifications, vous pourrez seulement enrichir un petit nombre de documents gratuitement (20 documents par jour). 

## <a name="associate-a-cognitive-services-resource-with-a-new-skillset"></a>Association d’une ressource Cognitive Services à un nouvel ensemble de qualifications

1. Dans l’Assistant [Importer des données](search-import-data-portal.md), après votre connexion à votre source de données, vous pouvez accéder à l’étape facultative **Ajouter la recherche cognitive**. Il s’agit de la deuxième étape de l’Assistant.

1. Développez la section **Attacher Cognitive Services**. Cette étape vous montre toutes les ressources Cognitive Services que vous avez dans les mêmes régions que le service Recherche Azure.

   ![Section Option Attacher Cognitive Services développée](./media/cognitive-search-attach-cognitive-services/attach1.png "Option Attacher Cognitive Services développée")

1. Sélectionnez une ressource Cognitive Services existante ou **Créer une ressource Cognitive Services**. Si vous sélectionnez **Gratuit (enrichissements limités)**, vous pourrez uniquement enrichir un petit nombre de documents gratuitement (20 documents par jour). Si vous sélectionnez **Créer une ressource Cognitive Services**, un onglet s’ouvre, dans lequel vous pouvez créer la ressource. 

1. Si vous avez créé une ressource, sélectionnez **Actualiser** pour rafraîchir la liste des ressources Cognitive Services, puis sélectionnez la ressource. 

   ![Ressource Cognitive Services sélectionnée](./media/cognitive-search-attach-cognitive-services/attach2.png "Ressource Cognitive Services sélectionnée")

1. Développez la section **Ajouter des enrichissements** pour sélectionner les qualifications cognitives spécifiques à exécuter sur vos données et poursuivre le processus.

## <a name="associate-a-cognitive-services-resource-with-an-existing-skillset"></a>Association d’une ressource Cognitive Services à un ensemble de qualifications existant

1. Dans la page **Présentation du service**, sélectionnez l’onglet **Ensembles de qualifications**.

   ![Onglet Ensemble de qualifications](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Onglet Ensemble de qualifications")

1. Sélectionnez l’ensemble de qualifications que vous souhaitez modifier. Cette étape ouvre un panneau où vous pouvez modifier les qualifications.

1. Sélectionnez une ressource Cognitive Services existante ou **Créer une ressource Cognitive Services**. Si vous sélectionnez **Gratuit (enrichissements limités)**, vous pourrez uniquement enrichir un petit nombre de documents gratuitement (20 documents par jour). Si vous sélectionnez **Créer une ressource Cognitive Services**, un onglet s’ouvre, dans lequel vous pouvez créer la ressource.

   <n/> 
   <img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Si vous avez créé une ressource, sélectionnez **Actualiser** pour rafraîchir la liste des ressources Cognitive Services, puis sélectionnez la ressource.

1. Cliquez sur **OK** pour confirmer vos modifications.

## <a name="associate-a-cognitive-services-resource-programmatically"></a>Association par programme d’une ressource Cognitive Services

Lorsque vous définissez l’ensemble de qualifications par programme, ajoutez une section `cognitiveServices`. Dans la section, incluez la clé de la ressource Cognitive Services que vous souhaitez associer à l’ensemble de qualifications. Incluez également `@odata.type`, que vous définissez sur `#Microsoft.Azure.Search.CognitiveServicesByKey`. L’exemple ci-après illustre ce modèle.

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
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimate-the-cost-of-document-cracking-and-enrichment"></a>Exemple : Estimation du coût du décodage et de l’enrichissement de document
Vous souhaiterez peut-être estimer le coût de l’enrichissement d’un type de document. L’exercice suivant est fourni uniquement à titre d’exemple, mais il peut vous être utile.

Imaginez que vous avez 1 000 documents PDF. Vous estimez qu’en moyenne, chacun de ces documents contient 6 pages. Chaque page possède une 1 image. En moyenne, chaque page contient environ 3 000 caractères. 

Maintenant, supposons que vous souhaitez effectuer les étapes suivantes dans le cadre du processus d’enrichissement :
1. Dans le cadre du décodage de document, extrayez le contenu et les images du document.
1. Dans le cadre de l’enrichissement, passez chaque page extraite à la reconnaissance optique de caractères (OCR), combinez le texte de toutes les pages, puis extrayez chacune des organisations dans le texte combiné de toutes les images.

Nous allons estimer combien coûterait l’ingestion de ces 1 000 documents, étape par étape :

1. Décodage de document : vous auriez extrait un nombre cumulé de 6 000 images. En comptant 1 $ par lot de 1 000 images extraites, cela vous coûterait 6 $.

2. Nous passerions ensuite à l’extraction du texte de chacune de ces 6 000 images. En anglais, la qualification cognitive de reconnaissance optique de caractères (OCR) utilise le meilleur algorithme (DescribeText). À raison de 2,50 $ par lot de 1 000 images à analyser, cette étape vous coûterait 15 $.

3. Pour l’extraction d’entités, vous auriez un total de 3 enregistrements texte par page. Chaque enregistrement contient 1 000 caractères. Trois enregistrements texte par page X 6 000 pages = 18 000 enregistrements texte. À raison de 2 $ par lot de 1 000 enregistrements texte, cette étape vous coûterait 36 $.

En additionnant tout cela, l’ingestion de 1 000 documents PDF de cette nature avec l’ensemble de qualifications décrit vous reviendrait à 57 $. Dans cet exercice, nous avons appliqué le prix plus élevé pour chaque transaction. Il aurait pu être inférieur selon les promotions en vigueur. Voir [Tarification Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>Étapes suivantes
+ [Page Tarification de Recherche Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Créer un jeu de compétences (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Guide pratique pour mapper des champs enrichis](cognitive-search-output-field-mapping.md)
