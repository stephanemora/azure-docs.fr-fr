---
title: 'Tutoriel : Créer un bot de forum aux questions pour plusieurs domaines avec Azure Bot Service'
description: Dans ce tutoriel, vous créez un bot de forum aux questions sans code, pour les cas d’utilisation de production, avec QnA Maker et Azure Bot Service.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.author: diagarw
ms.date: 03/31/2021
ms.openlocfilehash: d79eed22d441949810cfc1738f3af2c0f8703adc
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110116416"
---
# <a name="add-multiple-domains-to-your-faq-bot"></a>Ajouter plusieurs domaines à votre bot de forum aux questions

Lors de la création d’un bot de forum aux questions, vous pouvez être confronté à des cas d’utilisation qui vous demandent d’adresser des requêtes dans plusieurs domaines. Supposons que l’équipe marketing de Microsoft souhaite créer un bot de service clientèle qui réponde aux demandes les plus fréquentes des utilisateurs sur différents produits Surface. Pour rester simples, nous utiliserons une URL de FAQ distincte pour le [stylet Surface](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98) et pour les écouteurs [Surface Earbuds](https://support.microsoft.com/surface/use-surface-earbuds-aea108c3-9344-0f11-e5f5-6fc9f57b21f9) dans la création de la base de connaissances.

Vous pouvez concevoir votre bot de gestion des requêtes sur plusieurs domaines avec QnA Maker, selon les différentes manières suivantes :

* Création d’une base de connaissances unique et étiquetage des paires de questions-réponses dans les domaines distincts avec des métadonnées
* Création d’une base de connaissances distincte pour chaque domaine
* Création d’une ressource QnA Maker distincte pour chaque domaine

## <a name="create-a-single-knowledge-base-and-tag-qna-pairs-into-distinct-domains-with-metadata"></a>Création d’une base de connaissances unique et étiquetage des paires de questions-réponses dans les domaines distincts avec des métadonnées

Les auteurs de contenu peuvent utiliser des documents pour extraire des questions-réponses, ou ajouter des questions-réponses personnalisées à la base de connaissances. Pour regrouper ces questions-réponses dans des domaines ou des catégories spécifiques, vous pouvez ajouter des [métadonnées](../How-To/query-knowledge-base-with-metadata.md) aux paires de questions-réponses.

Pour le bot des produits Surface, vous pouvez effectuer les étapes suivantes en créant un bot qui réponde aux requêtes des deux types de produits :

1. Ajoutez les URL de FAQ suivantes sur les produits Surface à l’ÉTAPE 3 de la page Créer une base de connaissances, puis cliquez sur « Créer votre base de connaissances ». Une nouvelle base de connaissances est créée après l’extraction des paires de questions-réponses à partir de ces sources. 
   
   [FAQ sur le stylet Surface](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98)<br>[FAQ sur Surface Earbuds](https://support.microsoft.com/surface/use-surface-earbuds-aea108c3-9344-0f11-e5f5-6fc9f57b21f9)
 
2. Après avoir créé la base de connaissances, vous pouvez accéder à **Options d’affichage** et cliquer sur **Afficher les métadonnées**. Une colonne de métadonnées pour les questions-réponses s’ouvre.

   >[!div class="mx-imgBorder"]
   >[![Affichage des métadonnées ]( ../media/qnamaker-tutorial-updates/show-metadata.png)]( ../media/qnamaker-tutorial-updates/expand/show-metadata.png#lightbox)


3. Dans cette base de connaissances, nous disposons de questions-réponses sur deux produits, et nous aimerions les différencier pour pouvoir rechercher des réponses parmi les questions-réponses d’un produit donné. Pour ce faire, nous devons mettre à jour le champ de métadonnées des paires de questions-réponses en conséquence. 

   Comme vous pouvez le voir dans l’exemple ci-dessous, nous avons ajouté une métadonnée avec **produit** en tant que clé, et **surface_pen** ou **surface_earbuds** en tant que valeurs, lorsque cela est nécessaire. Vous pouvez étendre cet exemple pour extraire des données sur plusieurs produits, et ajouter une valeur différente pour chaque produit.

   >[!div class="mx-imgBorder"]
   >[![Métadonnées]( ../media/qnamaker-tutorial-updates/metadata-example-2.png)]( ../media/qnamaker-tutorial-updates/expand/metadata-example-2.png#lightbox)

4. À présent, pour limiter la recherche de réponse du système à un produit particulier, vous devez transmettre ce produit en tant que filtre strict dans l’API de génération de réponse.

    Découvrez [comment utiliser l’API GenerateAnswer](../How-To/metadata-generateanswer-usage.md). Le format de l’URL GenerateAnswer est le suivant :
    ```
    https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
    ```

    Dans le corps JSON de l’appel d’API, nous avons passé *surface_pen* comme valeur pour le *produit* en métadonnée. Le système recherche alors uniquement la réponse parmi les paires de questions-réponses ayant la même métadonnée. 

    ```json
    {
        "question": "What is the price?",
        "top": 6,
        "isTest": true,
        "scoreThreshold": 30,
        "rankerType": ""  // values: QuestionOnly
        "strictFilters": [
        {
            "name": "product",
            "value": "surface_pen"
        }],
        "userId": "sd53lsY="
    }
    ```

    Vous pouvez obtenir la valeur de la métadonnée en fonction de l’entrée utilisateur de l’une des manières suivantes : 

    * Acceptez explicitement le domaine comme entrée à partir de l’utilisateur par le biais du client bot. Par exemple, comme illustré ci-dessous, vous pouvez utiliser une catégorie de produit en tant qu’entrée à partir de l’utilisateur lorsque la conversation est lancée.

      ![Utilisation d’une entrée de métadonnée](../media/qnamaker-tutorial-updates/expand/explicit-metadata-input.png)

    * Identifiez implicitement le domaine en fonction du contexte du bot. Par exemple, en cas de question précédente posée sur un produit Surface particulier, celui-ci peut être enregistré en tant que contexte par le client. Si l’utilisateur ne précise pas le produit dans la requête suivante, vous pouvez transmettre le contexte du bot comme métadonnées à l’API de génération de réponse.

      ![Transmission du contexte]( ../media/qnamaker-tutorial-updates/expand/extract-metadata-from-context.png)

    * Extrayez l’entité à partir de la requête utilisateur permettant d’identifier le domaine à utiliser pour le filtre de métadonnées. Vous pouvez utiliser d’autres ressources Cognitive Services, telles que l’Analyse de texte et LUIS pour l’extraction d’entités.

      ![Extraction de métadonnées à partir d’une requête]( ../media/qnamaker-tutorial-updates/expand/extract-metadata-from-query.png)

### <a name="how-large-can-our-knowledge-bases-be"></a>Quelle est la taille maximale de nos bases de connaissances ? 

Vous pouvez ajouter jusqu’à 50 000 paires de questions-réponses à une base de connaissances unique. Si vos données excèdent 50 000 paires de questions-réponses, vous devez envisager de diviser la base de connaissances.

## <a name="create-a-separate-knowledge-base-for-each-domain"></a>Créer une base de connaissances distincte pour chaque domaine

Vous pouvez également créer une base de connaissances distincte pour chaque domaine, et gérer séparément les bases de connaissances. Toutes les API demandent que l’utilisateur transmette l’ID de la base de connaissances, pour effectuer une mise à jour de la base de connaissances ou pour récupérer une réponse à la question de l’utilisateur.  

Lorsque la question de l’utilisateur est reçue par le service, vous devez transmettre l’ID de la base de connaissances dans le point de terminaison de la génération de réponse, illustré ci-dessus, pour extraire une réponse de la base de connaissances appropriée. Vous trouverez l’ID de la base de connaissances dans la section de **Publication**, comme indiqué ci-dessous.

>[!div class="mx-imgBorder"]
>![Récupération de l’ID de la base de connaissances](../media/qnamaker-tutorial-updates/fetch-kb-id.png)

## <a name="create-a-separate-qna-maker-resource-for-each-domain"></a>Création d’une ressource QnA Maker distincte pour chaque domaine

Supposons que l’équipe marketing de Microsoft souhaite créer un bot de service clientèle qui réponde aux demandes des utilisateurs concernant les produits Surface et Xbox. Elle envisage d’affecter des équipes distinctes pour accéder aux bases de connaissances du produit Surface et du produit Xbox. Dans ce cas, il est recommandé de créer deux ressources QnA Maker, une pour la Surface et l’autre pour la Xbox. Par contre, vous pouvez définir des rôles distincts pour les utilisateurs qui accèdent à la même ressource. En savoir plus sur le [Contrôle d’accès en fonction du rôle](../How-To/manage-qna-maker-app.md). 
