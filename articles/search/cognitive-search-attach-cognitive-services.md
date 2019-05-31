---
title: Attacher une ressource Cognitive Services à un ensemble de qualifications – Recherche Azure
description: Instructions pour attacher un abonnement de tout-en-un Cognitive Services à un pipeline d’enrichissement cognitive dans Azure Search.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 44f16b3334b991e071fa85ca4cffbc0837f0a6ec
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244427"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Attacher une ressource Cognitive Services à un ensemble de qualifications dans Recherche Azure 

Lecteur d’algorithmes d’intelligence artificielle la [cognitives pipelines indexation](cognitive-search-concept-intro.md) utilisés pour l’enrichissement de document dans Azure Search. Ces algorithmes sont basés sur les ressources Azure Cognitive Services, y compris [vision par ordinateur](https://azure.microsoft.com/services/cognitive-services/computer-vision/) pour l’analyse de l’image et de reconnaissance optique de caractères (OCR) et [Analytique de texte](https://azure.microsoft.com/services/cognitive-services/text-analytics/) pour la reconnaissance d’entité, extraction de phrases clés et autres enrichissement. Que ceux utilisés par Azure Search pour les fins d’enrichissement de document, les algorithmes sont encapsulées à l’intérieur d’un *compétence*, placée dans un *compétences*et référencés par un *indexeur* pendant l’indexation.

Vous pouvez enrichir un nombre limité de documents gratuitement. Ou, vous pouvez attacher une ressource Cognitive Services facturable à un *compétences* pour les charges de travail plus volumineux et plus fréquents. Dans cet article, vous allez apprendre à attacher une ressource Cognitive Services facturable pour enrichir les documents au cours de la recherche Azure [l’indexation](search-what-is-an-index.md).

> [!NOTE]
> Événements facturables incluent les appels à l’extraction d’API Cognitive Services et l’image dans le cadre de l’étape de décodage de document dans Azure Search. Aucun frais n’est pour l’extraction de texte à partir de documents ou des compétences qui n’appellent pas de Cognitive Services.
>
> Exécution de compétences facturables s’effectue sur le [Cognitive Services paie-sous-vous accédez prix](https://azure.microsoft.com/pricing/details/cognitive-services/). Pour connaître la tarification image d’extraction, consultez le [page de tarification de Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="same-region-requirement"></a>Spécification de la même région

Nous exigeons que recherche Azure et Azure Cognitive Services existent dans la même région. Sinon, vous obtiendrez ce message en cours d’exécution : `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Il n’existe aucun moyen de déplacer un service dans différentes régions. Si vous obtenez cette erreur, vous devez créer une nouvelle ressource Cognitive Services dans la même région que la recherche Azure.

## <a name="use-free-resources"></a>Utiliser des ressources gratuites

Vous pouvez utiliser une option de traitement gratuite, limitée aux exercices des guides de démarrage rapide et des tutoriels de la recherche cognitive.

Ressources de libre (limitée : enrichissements) sont limitées à 20 documents par jour, par abonnement.

1. Ouvrez l’Assistant Importation de données :

   ![Ouvrir l’Assistant Importation de données](media/search-get-started-portal/import-data-cmd2.png "ouvrir l’Assistant Importation de données")

1. Choisir une source de données et continuer à **recherche cognitive ajouter (facultatif)** . Pour une procédure pas à pas de cet Assistant, consultez [importation, index et des requêtes à l’aide des outils du portail](search-get-started-portal.md).

1. Développez **attacher Cognitive Services** , puis sélectionnez **gratuit (enrichissements limitées)** :

   ![Attacher un Cognitive Services section développée](./media/cognitive-search-attach-cognitive-services/attach1.png "section développé attacher Cognitive Services")

1. Passez à l’étape suivante, **enrichissements ajouter**. Pour obtenir une description des compétences disponibles dans le portail, consultez [étape 2 : Ajouter des compétences cognitives](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) dans le Guide de démarrage rapide recherche cognitive.

## <a name="use-billable-resources"></a>Utiliser des ressources facturables

Pour les charges de travail qui créent des enrichissements de plus de 20 par jour, veillez à attacher une ressource Cognitive Services facturable. Nous vous recommandons de toujours attacher une ressource Cognitive Services facturable, même si vous n’envisagez pas d’appeler les API Cognitive Services. Attachement d’une ressource substitue à la limite quotidienne.

Vous êtes facturé uniquement pour les compétences qui appellent les API Cognitive Services. Vous n’êtes pas facturé pour [compétences personnalisées](cognitive-search-create-custom-skill-example.md), ou comme des compétences [fusion de texte](cognitive-search-skill-textmerger.md), [fractionnement de texte](cognitive-search-skill-textsplit.md), et [modélisateur](cognitive-search-skill-shaper.md), qui ne sont pas basées sur API.

1. Ouvrir l’Assistant Importation de données, choisissez une source de données et continuer à **recherche cognitive ajouter (facultatif)** .

1. Développez **attacher Cognitive Services** , puis sélectionnez **Créer nouvelle ressource Cognitive Services**. Un nouvel onglet s’ouvre afin que vous puissiez créer la ressource :

   ![Créer une ressource Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "créer une ressource Cognitive Services")

1. Dans le **emplacement** , sélectionnez la région où se trouve votre service Azure Search. Veillez à utiliser cette région pour des raisons de performances. À l’aide de cette région invalide également les frais de bande passante sortante dans différentes régions.

1. Dans le **niveau tarifaire** liste, sélectionnez **S0** pour obtenir la collection tout-en-un des fonctionnalités de Cognitive Services, notamment les fonctionnalités de Vision et de langage qui soutiennent les compétences prédéfinies utilisées par Azure Search.

   Pour le niveau S0, vous pouvez trouver des tarifs pour les charges de travail spécifiques sur le [page de tarification de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Dans le **sélectionnez offrent** liste, assurez-vous que **Cognitive Services** est sélectionné.
   + Sous **langage** fonctionnalités, les tarifs de **Analytique de texte Standard** s’appliquent à l’indexation de l’intelligence artificielle.
   + Sous **Vision** fonctionnalités, les tarifs de **S1 de vision par ordinateur** s’appliquent.

1. Sélectionnez **créer** pour configurer la nouvelle ressource Cognitive Services.

1. Revenez à l’onglet précédent, qui contient l’Assistant Importation de données. Sélectionnez **Actualiser** pour afficher la ressource Cognitive Services, puis sélectionnez la ressource :

   ![Sélectionnez la ressource Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "sélectionnez la ressource Cognitive Services")

1. Développez le **enrichissements ajouter** section pour sélectionner les compétences cognitives spécifiques que vous souhaitez exécuter sur vos données. Terminez l’Assistant. Pour obtenir une description des compétences disponibles dans le portail, consultez [étape 2 : Ajouter des compétences cognitives](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) dans le Guide de démarrage rapide recherche cognitive.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Attacher un ensemble de qualifications à une ressource Cognitive Services existante

Si vous avez un ensemble de qualifications existant, vous pouvez l’attacher à une ressource Cognitive Services nouvelle ou différente.

1. Sur le **vue d’ensemble du Service** page, sélectionnez **compétences**:

   ![Onglet Ensemble de qualifications](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Onglet Ensemble de qualifications")

1. Sélectionnez le nom de la compétences, puis sélectionnez une ressource existante ou créez-en un. Cliquez sur **OK** pour confirmer vos modifications.

   ![Liste des ressources d’ensemble de qualifications](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Liste des ressources d’ensemble de qualifications")

   N’oubliez pas que le **gratuit (enrichissements limitées)** option vous limite à 20 documents quotidiennement, et que vous pouvez utiliser **Créer nouvelle ressource Cognitive Services** pour approvisionner une nouvelle ressource facturable. Si vous avez créé une ressource, sélectionnez **Actualiser** pour rafraîchir la liste des ressources Cognitive Services, puis sélectionnez la ressource.

## <a name="attach-cognitive-services-programmatically"></a>Attacher Cognitive Services par programme

Lorsque vous définissez l’ensemble de qualifications par programme, ajoutez une section `cognitiveServices` à l’ensemble de qualifications. Dans cette section, incluez la clé de la ressource Cognitive Services que vous souhaitez associer les compétences dont dispose. N’oubliez pas que la ressource doit être dans la même région que votre ressource Azure Search. Incluez également `@odata.type`, que vous définissez sur `#Microsoft.Azure.Search.CognitiveServicesByKey`.

L’exemple ci-après illustre ce modèle. Notez que le `cognitiveServices` section à la fin de la définition.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
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

Pour estimer les coûts associés à l’indexation de recherche cognitive, commencez par une idée de quoi ressemble une moyenne des documents afin de pouvoir exécuter des nombres. Par exemple, vous pouvez estimer :

+ Fichiers PDF de 1 000.
+ Six pages chaque.
+ Une image par page (6 000 images).
+ 3 000 caractères par page.

Supposons un pipeline qui se compose de recherche de document de chaque extraction PDF, image et texte, reconnaissance optique de caractères (OCR) des images et reconnaissance d’entité des organisations.

Les prix indiqués dans cet article sont hypothétiques. Elles sont utilisées pour illustrer le processus d’estimation. Vos coûts peut être inférieur. Pour obtenir les prix réels de transactions, consultez [tarification Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Pour le décodage de documents avec contenu de texte et d’image, l’extraction de texte est actuellement gratuite. Pour les images de 6 000, supposons que $1 pour chaque 1 000 images extraites. C’est un coût de $6.00 pour cette étape.

2. Pour la reconnaissance optique de caractères (OCR) de 6 000 images en anglais, la qualification cognitive OCR utilise le meilleur algorithme (DescribeText). À raison de 2,50 $ par lot de 1 000 images à analyser, cette étape vous coûterait 15 $.

3. Pour l’extraction d’entités, vous devez au total trois enregistrements de texte par page. Chaque enregistrement contient 1 000 caractères. Trois enregistrements de texte par page multiplié par 6 000 pages est égale à 18 000 enregistrements de texte. À raison de 2 $ par lot de 1 000 enregistrements texte, cette étape vous coûterait 36 $.

Vue d’ensemble, vous devrez alors payer environ 57,00 dollars pour recevoir les 1 000 documents PDF de ce type avec les compétences décrites.

## <a name="next-steps"></a>Étapes suivantes
+ [Page Tarification de Recherche Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Créer un jeu de compétences (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Guide pratique pour mapper des champs enrichis](cognitive-search-output-field-mapping.md)
