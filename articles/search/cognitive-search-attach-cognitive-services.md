---
title: Attacher une ressource Cognitive Services à un ensemble de qualifications – Recherche Azure
description: Instructions à suivre pour attacher un abonnement Cognitive Services tout-en-un à un ensemble d’enrichissement cognitif dans Recherche Azure.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bfa9bbb9816148182b79a8231f2ddb3e46433804
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413241"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Attacher une ressource Cognitive Services à un ensemble de qualifications dans Recherche Azure 

Les algorithmes d’intelligence artificielle pilotent les [pipelines de recherche cognitive](cognitive-search-concept-intro.md) utilisés pour le traitement de données non structurées dans une opération d’indexation de Recherche Azure. Ces algorithmes sont basés sur les [ressources Cognitive Services](https://azure.microsoft.com/services/cognitive-services/), dont [Vision par ordinateur](https://azure.microsoft.com/services/cognitive-services/computer-vision/) pour l’analyse d’image et la reconnaissance optique de caractères (OCR), et [Analyse de texte](https://azure.microsoft.com/services/cognitive-services/text-analytics/) pour la reconnaissance des entités, l’extraction d’expressions clés et d’autres enrichissements.

Vous pouvez enrichir gratuitement un nombre limité de documents, ou attacher une ressource Cognitive Services facturable pour les charges de travail plus volumineuses et plus fréquentes. Cet article explique comment associer une ressource Azure Cognitive Services à votre ensemble de qualifications cognitives pour enrichir des données durant l’[indexation de Recherche Azure](search-what-is-an-index.md).

Si votre pipeline est composé de compétences non liées aux API Cognitive Services, vous devez toujours attacher une ressource Cognitive Services. Vous remplacez ainsi la ressource **Gratuit** qui vous limite à un petit nombre d’enrichissements par jour. Aucuns frais ne s’appliquent pour les compétences qui ne sont pas liées aux API Cognitive Services. Ces compétences sont notamment les [compétences personnalisées](cognitive-search-create-custom-skill-example.md), la compétence de [fusion de texte](cognitive-search-skill-textmerger.md), de [fractionnement de texte](cognitive-search-skill-textsplit.md) et de [modélisation](cognitive-search-skill-shaper.md).

> [!NOTE]
> Depuis le 21 décembre 2018, vous pouvez associer une ressource Cognitive Services à un ensemble de qualifications du service Recherche Azure. Cela nous permet de facturer l’exécution d’un ensemble de qualifications. Ce jour-là, nous avons également commencé à facturer l’extraction d’images dans le cadre de notre étape de décodage de documents. L’extraction de texte à partir de documents est toujours offerte sans frais supplémentaires.
>
> L’exécution de [compétences cognitives intégrées](cognitive-search-predefined-skills.md) est facturée au [tarif de paiement à l’utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services), qui est le même que si vous aviez exécuté la tâche directement. L’extraction d’image est une opération facturable de Recherche Azure, actuellement proposée au tarif de la préversion. Pour plus d’informations, consultez la [page des tarifs de Recherche Azure](https://go.microsoft.com/fwlink/?linkid=2042400) ou [Comment la facturation fonctionne](search-sku-tier.md#how-billing-works).


## <a name="use-free-resources"></a>Utiliser des ressources gratuites

Vous pouvez utiliser une option de traitement gratuite, limitée aux exercices des guides de démarrage rapide et des tutoriels de la recherche cognitive. 

> [!Important]
> À partir du 1er février 2019, l’option **Gratuit (Enrichissements limités)** sera limitée à 20 documents par jour. 

1. Ouvrez l’**Assistant Importation de données**.

   ![Commande Importer des données](media/search-get-started-portal/import-data-cmd2.png "Commande Importer des données")

1. Choisissez une source de données, puis continuer à **Ajouter la recherche cognitive (facultatif)**. Pour une procédure pas à pas de cet assistant, voir l’article sur [l’importation, l’indexation et l’interrogation à l’aide des outils du portail](search-get-started-portal.md).

1. Développez **Attacher Cognitive Services**, puis sélectionnez **Gratuit (enrichissements limités)**.

   ![Section Option Attacher Cognitive Services développée](./media/cognitive-search-attach-cognitive-services/attach1.png "Option Attacher Cognitive Services développée")

Passez à l’étape suivante, **Ajouter des enrichissements**. Pour une description des compétences disponibles dans le portail, consultez [« Étape 2 : Ajouter une compétence cognitive »](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) dans le guide de démarrage rapide de la recherche cognitive.

## <a name="use-billable-resources"></a>Utiliser des ressources facturables

Pour les charges de travail comptant plus de 20 enrichissements par jour, vous devez attacher une ressource Cognitive Services facturable. 

Seules les compétences qui appellent les API Cognitive Services vous sont facturées. Les compétences qui ne sont pas basées sur des API telles que les [compétences personnalisées](cognitive-search-create-custom-skill-example.md), la compétence de [fusion de texte](cognitive-search-skill-textmerger.md), de [fractionnement de texte](cognitive-search-skill-textsplit.md) et de [modélisation](cognitive-search-skill-shaper.md) ne sont pas facturées.

1. Dans l’Assistant **Importation de données** dans **Attacher Cognitive Services**, sélectionnez une ressource existante ou cliquez sur **Créer une ressource Cognitive Services**.

1. Pour **Créer une ressource Cognitive Services**, un nouvel onglet s’ouvre pour vous permettre de créer la ressource. Donnez un nom unique à la ressource.

1. Choisissez le même emplacement que Recherche Azure. Actuellement, l’indexation des qualifications cognitives est prise en charge dans les régions suivantes :

  * USA Centre-Ouest
  * USA Centre Sud
  * USA Est
  * USA Est 2
  * USA Ouest 2
  * Centre du Canada
  * Europe Ouest
  * Sud du Royaume-Uni
  * Europe Nord
  * Brésil Sud
  * Asie Sud-Est
  * Inde Centre
  * Australie Est

1. Choisissez le niveau tarifaire tout-en-un, **S0**. Ce niveau inclut les fonctionnalités Vision et Langue qui sous-tendent les qualifications prédéfinies dans la recherche cognitive.

    ![Créer une ressource Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Créer une ressource Cognitive Services")

1. Cliquez sur **créer** pour approvisionner la nouvelle ressource Cognitive Services. 

1. Revenez à l’onglet précédent contenant l’Assistant **Importation de données**. Cliquez sur **Actualiser** pour afficher la ressource Cognitive Services, puis sélectionnez-la.

   ![Ressource Cognitive Services sélectionnée](./media/cognitive-search-attach-cognitive-services/attach2.png "Ressource Cognitive Services sélectionnée")

1. Développez la section **Ajouter des enrichissements** pour sélectionner les qualifications cognitives spécifiques à exécuter sur vos données et poursuivre le processus. Pour une description des compétences disponibles dans le portail, consultez [« Étape 2 : Ajouter une compétence cognitive »](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) dans le guide de démarrage rapide de la recherche cognitive.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Attacher un ensemble de qualifications à une ressource Cognitive Services existante

Si vous avez un ensemble de qualifications existant, vous pouvez l’attacher à une ressource Cognitive Services nouvelle ou différente.

1. Dans la page **Vue d’ensemble du service**, cliquez sur **Ensemble de qualifications**.

   (./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Onglet Ensemble de qualifications")(./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Onglet Ensemble de qualifications")

1. Cliquez sur le nom de l’ensemble de qualifications, puis sélectionnez une ressource existante ou créez-en une. Cliquez sur **OK** pour confirmer vos modifications. 

   ![Liste des ressources d’ensemble de qualifications](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Liste des ressources d’ensemble de qualifications")

N’oubliez pas que l’option **Gratuit (enrichissements limités)** est restreinte à 20 documents par jour, et que l’option **Créer une ressource Cognitive Services** est utilisée pour approvisionner une nouvelle ressource facturable. Si vous avez créé une ressource, sélectionnez **Actualiser** pour rafraîchir la liste des ressources Cognitive Services, puis sélectionnez la ressource.

## <a name="attach-cognitive-services-programmatically"></a>Attacher Cognitive Services par programme

Lorsque vous définissez l’ensemble de qualifications par programme, ajoutez une section `cognitiveServices` à l’ensemble de qualifications. Dans la section, incluez la clé de la ressource Cognitive Services que vous souhaitez associer à l’ensemble de qualifications. Rappelez-vous que la ressource doit se trouver dans la même région que Recherche Azure. Incluez également `@odata.type`, que vous définissez sur `#Microsoft.Azure.Search.CognitiveServicesByKey`. 

L’exemple ci-après illustre ce modèle. Notez la section cognitiveServices en bas de la définition

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
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Exemple : Estimer les coûts

Pour estimer les coûts associés à l’indexation de recherche cognitive, commencez par vous représenter à quoi ressemble un document moyen afin de pouvoir donner une approximation. Par exemple, à des fins d’estimation, votre approximation pourrait être la suivante :

+ 1 000 fichiers PDF
+ Six pages par fichier
+ Une image par page (6 000 images)
+ 3 000 caractères par page

Supposons un pipeline consistant en un décodage de documents PDF avec extraction d’images et de texte, reconnaissance optique de caractères (OCR) d’images et reconnaissance d’entités nommées d’organisations. 

Dans cet exercice, nous appliquons le prix plus élevé pour chaque transaction. Les coûts réels pourraient être inférieurs en raison de la tarification progressive. Voir [Tarification Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Pour le décodage de documents avec contenu de texte et d’image, l’extraction de texte est actuellement gratuite. Pour 6 000 images, en supposant un prix de 1 USD pour 1 000 images extraites, le coût de cette étape est évalué à 6 USD.

2. Pour la reconnaissance optique de caractères (OCR) de 6 000 images en anglais, la qualification cognitive OCR utilise le meilleur algorithme (DescribeText). À raison de 2,50 $ par lot de 1 000 images à analyser, cette étape vous coûterait 15 $.

3. Pour l’extraction d’entités, vous auriez un total de 3 enregistrements texte par page. Chaque enregistrement contient 1 000 caractères. Trois enregistrements texte par page X 6 000 pages = 18 000 enregistrements texte. À raison de 2 $ par lot de 1 000 enregistrements texte, cette étape vous coûterait 36 $.

En additionnant tout cela, l’ingestion de 1 000 documents PDF de cette nature avec l’ensemble de qualifications décrit vous reviendrait à environ 57 USD. 

## <a name="next-steps"></a>Étapes suivantes
+ [Page Tarification de Recherche Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Créer un jeu de compétences (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Guide pratique pour mapper des champs enrichis](cognitive-search-output-field-mapping.md)
