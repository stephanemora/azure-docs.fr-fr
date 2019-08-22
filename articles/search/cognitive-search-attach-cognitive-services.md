---
title: Attacher une ressource Cognitive Services à un ensemble de qualifications – Recherche Azure
description: Instructions à suivre pour attacher un abonnement Cognitive Services tout-en-un à un pipeline d’enrichissement cognitif dans Recherche Azure.
manager: nitinme
author: LuisCabrer
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.openlocfilehash: dcdef8df25bdf6a7a60b1221d1463e6be5486875
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639446"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Attacher une ressource Cognitive Services à un ensemble de qualifications dans Recherche Azure 

Les algorithmes d’intelligence artificielle pilotent les [pipelines d’indexation cognitive](cognitive-search-concept-intro.md) utilisés pour l’enrichissement de document dans Recherche Azure. Ces algorithmes sont basés sur des ressources Cognitive Services, dont [Vision par ordinateur](https://azure.microsoft.com/services/cognitive-services/computer-vision/) pour l’analyse d’image et la reconnaissance optique de caractères (OCR), et [Analyse de texte](https://azure.microsoft.com/services/cognitive-services/text-analytics/) pour la reconnaissance des entités, l’extraction d’expressions clés et d’autres enrichissements. Tels qu’utilisés par Recherche Azure à des fins d’enrichissement de documents, les algorithmes sont encapsulés dans une *qualification*, placés dans un *ensemble de qualifications* et référencés par un *indexeur* lors de l’indexation.

Vous pouvez enrichir un nombre limité de documents gratuitement. Vous pouvez également associer une ressource Cognitive Services facturable à un *ensemble de qualifications* pour des charges de travail plus volumineuses et plus fréquentes. Dans cet article, vous allez apprendre à associer une ressource Cognitive Services facturable pour enrichir des documents au cours d’une [indexation](search-what-is-an-index.md) de Recherche Azure.

> [!NOTE]
> Les événements facturables incluent les appels d’API Cognitive Services et l’extraction d’images durant la phase de craquage de document dans Recherche Azure. L’extraction de texte à partir de documents ou les qualifications n’appelant pas Cognitive Services ne sont pas facturés.
>
> L’exécution de qualifications facturables est facturée au [tarif de paiement à l’utilisation de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Pour connaître les prix appliqués à l’extraction d’images, voir la [page de tarification du service Recherche Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="same-region-requirement"></a>Exigence de même région

Recherche Azure et Azure Cognitive Services doivent obligatoirement exister au sein de la même région. Autrement, vous obtenez ce message lors de l’exécution : `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Il n’existe aucun moyen de changer un service de région. Si vous obtenez cette erreur, vous devez créer une ressource Cognitive Services située dans la même région que le service Recherche Azure.

> [!NOTE]
> Certaines compétences intégrées sont basées sur des services cognitifs non régionaux (par exemple la [compétence de traduction de texte](cognitive-search-skill-text-translation.md)). Sachez que si vous ajoutez l’une d’elles à vos compétences, il n’est pas garanti que vos données restent dans la même région que votre ressource Recherche Azure ou Cognitive Services. Pour plus d’informations, consultez la [page État du service](https://aka.ms/allinoneregioninfo).

## <a name="use-free-resources"></a>Utiliser des ressources gratuites

Vous pouvez utiliser une option de traitement gratuite, limitée aux exercices des guides de démarrage rapide et des tutoriels de la recherche cognitive.

Les ressources de l’option Gratuit (enrichissements limités) sont limitées à 20 documents par jour, par abonnement.

1. Ouvrez l’Assistant Importation de données :

   ![Ouvrir l’Assistant Importation de données](media/search-get-started-portal/import-data-cmd.png "Ouvrir l’Assistant Importation de données")

1. Choisissez une source de données, puis passez à **Ajouter la recherche cognitive (facultatif)** . Pour une procédure pas à pas de cet assistant, voir [Importer, indexer et interroger à l’aide des outils du portail](search-get-started-portal.md).

1. Développez **Attacher Cognitive Services**, puis sélectionnez **Gratuit (enrichissements limités)** .

   ![Section Attacher Cognitive Services développée](./media/cognitive-search-attach-cognitive-services/attach1.png "Section Attacher Cognitive Services développée")

1. Passez à l’étape suivante, **Ajouter des enrichissements**. Pour une description des qualifications disponibles dans le portail, voir [Étape 2 : Ajouter des qualifications cognitives](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) dans le guide de démarrage rapide de la recherche cognitive.

## <a name="use-billable-resources"></a>Utiliser des ressources facturables

Pour les charges de travail créant plus de 20 enrichissements par jour, veillez à attacher une ressource Cognitive Services facturable. Nous vous recommandons de toujours attacher une ressource Cognitive Services facturable, même si vous n’avez aucune intention d’appeler les API Cognitive Services. L’attachement d’une ressource modifie la limite quotidienne.

Vous n’êtes facturé que pour les qualifications qui appellent les API Cognitive Services. Vous n’êtes pas facturé pour les [qualifications personnalisées](cognitive-search-create-custom-skill-example.md) ou des qualifications telles que [Fusion de texte](cognitive-search-skill-textmerger.md), [Séparateur de texte ](cognitive-search-skill-textsplit.md) et [Modélisateur](cognitive-search-skill-shaper.md) qui ne sont pas basées sur des API.

1. Ouvrez l’Assistant Importation de données, choisissez une source de données, puis passez à **Ajouter la recherche cognitive (facultatif)** .

1. Développez **Attacher Cognitive Services**, puis sélectionnez **Créer une ressource Cognitive Services**. Un nouvel onglet s’ouvre pour vous permettre de créer la ressource :

   ![Créer une ressource Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Créer une ressource Cognitive Services")

1. Dans la liste **Emplacement**, sélectionnez la région où se trouve votre service Recherche Azure. Veillez à utiliser cette région à des fins de performances. L’utilisation de cette région évite également les frais de bande passante sortante entre les différentes régions.

1. Dans la liste **Niveau tarifaire**, sélectionnez **S0** pour obtenir la collection complète de fonctionnalités Cognitive Services, y compris les fonctionnalités Vision et Langue qui sous-tendent les qualifications prédéfinies utilisées par Recherche Azure.

   Pour le niveau S0, vous pouvez trouver les tarifs des charges de travail spécifiques dans la [page de tarification de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Dans la liste **Sélectionner une offre**, assurez-vous que **Cognitive Services** est sélectionné.
   + Sous les fonctionnalités **Langue**, les tarifs d’**Analyse de texte** standard s’appliquent à l’indexation basée sur l’intelligence artificielle.
   + Sous les fonctionnalités **Vision**, les tarifs de **Vision par ordinateur S1** s’appliquent.

1. Sélectionnez **Créer** pour approvisionner la nouvelle ressource Cognitive Services.

1. Revenez à l’onglet précédent contenant l’Assistant Importation de données. Sélectionnez **Actualiser** pour afficher la ressource Cognitive Services, puis sélectionnez-la :

   ![Sélectionner la ressource Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "Sélectionner la ressource Cognitive Services")

1. Développez la section **Ajouter des enrichissements** pour sélectionner les qualifications cognitives spécifiques à exécuter sur vos données. Suivez les recommandations restantes de l’Assistant. Pour une description des qualifications disponibles dans le portail, voir [Étape 2 : Ajouter des qualifications cognitives](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) dans le guide de démarrage rapide de la recherche cognitive.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Attacher un ensemble de qualifications à une ressource Cognitive Services existante

Si vous avez un ensemble de qualifications existant, vous pouvez l’attacher à une ressource Cognitive Services nouvelle ou différente.

1. Dans la page **Vue d’ensemble du service**, sélectionnez l’onglet **Ensembles de qualifications** :

   ![Onglet Ensemble de qualifications](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Onglet Ensemble de qualifications")

1. Sélectionnez le nom de l’ensemble de qualifications, puis choisissez une ressource existante ou créez-en une. Cliquez sur **OK** pour confirmer vos modifications.

   ![Liste des ressources d’ensemble de qualifications](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Liste des ressources d’ensemble de qualifications")

   N’oubliez pas que l’option **Gratuit (enrichissements limités)** vous limite à 20 documents par jour et que vous pouvez utiliser l’option **Créer une ressource Cognitive Services** pour approvisionner une nouvelle ressource facturable. Si vous avez créé une ressource, sélectionnez **Actualiser** pour rafraîchir la liste des ressources Cognitive Services, puis sélectionnez la ressource.

## <a name="attach-cognitive-services-programmatically"></a>Attacher Cognitive Services par programme

Lorsque vous définissez l’ensemble de qualifications par programme, ajoutez une section `cognitiveServices` à l’ensemble de qualifications. Dans cette section, incluez la clé de la ressource Cognitive Services que vous souhaitez associer à l’ensemble de qualifications. Rappelez-vous que la ressource doit se trouver dans la même région que votre ressource Recherche Azure. Incluez également `@odata.type`, que vous définissez sur `#Microsoft.Azure.Search.CognitiveServicesByKey`.

L’exemple ci-après illustre ce modèle. Notez la section `cognitiveServices` à la fin de la définition.

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

Pour estimer les coûts associés à l’indexation de recherche cognitive, commencez par vous représenter à quoi ressemble un document moyen afin de pouvoir donner une approximation. Par exemple, vous pourriez faire une approximation :

+ 1 000 fichiers PDF.
+ Six pages par fichier.
+ Une image par page (6 000 images).
+ 3 000 caractères par page.

Supposons un pipeline consistant en un craquage de document PDF, une extraction d’image et de texte, une reconnaissance optique de caractères (OCR) d’images, et une reconnaissance d’entités d’organisations.

Les prix indiqués dans cet article sont hypothétiques. Ils sont utilisés pour illustrer le processus d’estimation. Vos coûts peuvent être inférieurs. Pour obtenir les prix réels des transactions, voir [Tarification Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. Pour le décodage de documents avec contenu de texte et d’image, l’extraction de texte est actuellement gratuite. Pour 6 000 images, supposez un prix de 1 USD pour chaque tranche de 1 000 images extraites. Cela revient à un coût de 6,00 USD pour cette étape.

2. Pour la reconnaissance optique de caractères (OCR) de 6 000 images en anglais, la qualification cognitive OCR utilise le meilleur algorithme (DescribeText). À raison de 2,50 $ par lot de 1 000 images à analyser, cette étape vous coûterait 15 $.

3. Pour l’extraction d’entité, vous auriez un total de trois enregistrements texte par page. Chaque enregistrement contient 1 000 caractères. Trois enregistrements texte par page multipliés par 6 000 pages équivalent à 18 000 enregistrements texte. À raison de 2 $ par lot de 1 000 enregistrements texte, cette étape vous coûterait 36 $.

En additionnant tout cela, l’ingestion de 1 000 documents PDF de ce type avec l’ensemble de qualifications décrit vous reviendrait à environ 57 USD.

## <a name="next-steps"></a>Étapes suivantes
+ [Page Tarification de Recherche Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Créer un jeu de compétences (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Guide pratique pour mapper des champs enrichis](cognitive-search-output-field-mapping.md)
