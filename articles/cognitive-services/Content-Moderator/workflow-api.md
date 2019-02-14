---
title: Flux de travail avec modération - Content Moderator
titlesuffix: Azure Cognitive Services
description: Utilisez des flux de travail avec les opérations Job de l'API de révision afin d'automatiser les révisions pour une intervention humaine basée sur vos stratégies de contenu et vos seuils.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 56b3bffc7aca1ace4dfae78cdcd4394daae9360e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872792"
---
# <a name="automate-moderation-reviews-with-workflows"></a>Automatiser les révisions de modération avec des flux de travail

Content Moderator inclut des outils et des API pour gérer les flux de travail. Vous utilisez des flux de travail avec les [opérations Job de l’API de révision](review-api.md) pour automatiser la création de révisions pour une intervention humaine basée sur vos stratégies de contenu et vos seuils.

L’API de révision propose les méthodes suivantes pour inclure une supervision humaine dans votre processus de modération du contenu :

1. Les opérations **Job** pour le démarrage d’une modération assistée par ordinateur et la création de révisions par un opérateur humain en une seule étape.
1. Les opérations **Review** pour la création de révisions par un opérateur humain, en dehors de l’étape de modération.
1. Les opérations **Workflow** pour la gestion des flux de travail qui automatisent l’analyse avec des seuils pour la création de révisions.

Cet article aborde les opérations **Workflow**. Pour en savoir plus sur les tâches de modération et les révisions de contenu, lisez la vue d’ensemble [Tâches et révisions](review-api.md).

L’extraction du flux de travail **par défaut** est la meilleure façon de se familiariser avec les workflows dans Content Moderator.

## <a name="your-first-workflow"></a>Votre premier flux de travail

Votre premier flux de travail est fourni avec votre [équipe d’outils de révision](https://contentmoderator.cognitive.microsoft.com/). Inscrivez-vous, si ce n’est pas encore fait.

Accédez à l’écran [Flux de travail de l’outil de révision](Review-Tool-User-Guide/Workflows.md) sous l’onglet Paramètres. Un flux de travail **par défaut** s’affiche, comme indiqué dans l’image suivante :

![Flux de travail Content Moderator](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>Ouvrir le flux de travail par défaut

Utilisez l'option **Modifier** pour ouvrir la page de modification des flux de travail, comme indiqué dans l'image suivante : ![Flux de travail par défaut Content Moderator](images/default-workflow-listed.PNG)

### <a name="the-designer-view"></a>La vue Concepteur

L’onglet **Concepteur** du flux de travail est affiché. La vue Concepteur présente les étapes suivantes :

1. La **condition** du flux de travail à évaluer. Dans le cas présent, le flux de travail appelle l’API d’image de Content Moderator et vérifie si la sortie de `isAdult` est `true`.
1. L’**action** à exécuter si la condition est remplie. Dans le cas présent, le flux de travail crée une révision dans l’outil de révision si la sortie `isAdult` est `true`.

![Flux par défaut Content Moderator - Concepteur](images/default-workflow-designer.png)

### <a name="the-json-view"></a>La vue JSON

Sélectionnez l’onglet **JSON** pour voir la définition JSON du flux de travail.

    {
        "Type": "Logic",
        If": {
            "ConnectorName": "moderator",
            "OutputName": "isAdult",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": []
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="key-learning"></a>Apprentissage clé

Dans Content Moderator, les flux de travail sont flexibles et faciles à configurer. Si le concepteur intégré ne répond pas à vos besoins, écrivez la définition de flux de travail au format **JSON**. Utilisez ensuite la définition JSON avec l’[API de flux de travail](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) pour créer et gérer le flux de travail à partir de votre application.

## <a name="define-a-custom-workflow"></a>Définir un flux de travail personnalisé

Les fonctionnalités de flux de travail de Content Moderator permettent de définir et d’utiliser des flux de travail personnalisés. Utilisez l’article [Guide pratique pour les flux de travail de l’outil de révision](Review-Tool-User-Guide/Workflows.md) pour définir un flux de travail personnalisé. Ce flux de travail utilise la fonctionnalité OCR (reconnaissance optique des caractères) de Content Moderator pour extraire du texte d’un exemple d’image. Il crée ensuite une révision dans l’outil de révision.

### <a name="the-sample-image"></a>L’exemple d’image

Enregistrez l’[exemple d’image](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) sur votre disque local. Vous avez besoin de cette image dans votre exercice.

### <a name="the-designer-view"></a>La vue Concepteur

Sélectionnez l’onglet **Concepteur** et le [tutoriel de création de flux de travail](Review-Tool-User-Guide/Workflows.md) pour définir un flux de travail personnalisé. L’image suivante montre la vue **Condition** du Concepteur. Reportez-vous au tutoriel pour voir le reste des étapes.

![Content Moderator - Condition de flux de travail](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>La vue JSON

Sélectionnez l’onglet **JSON** pour voir la définition JSON suivante de votre flux de travail personnalisé. Notez comment les instructions **If-Then** dans la définition JSON correspondent aux étapes que vous avez définies à l’aide de la vue Concepteur.

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="workflow-result"></a>Résultat du flux de travail

Une fois que vous avez testé le flux de travail à partir de l’écran des flux de travail, la révision suivante est créée. Accédez à l’onglet **Image** sous **Révision** pour voir votre révision.
Le flux de travail a créé la révision car le test de la condition principale a révélé la présence de texte. La révision a également mis en surbrillance la balise **`a`** dans la révision de l’image.

![Content Moderator - Sortie de flux de travail simple](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>Flux de travail avancé avec combinaison

### <a name="the-sample-image"></a>L’exemple d’image

Utilisez le même [exemple d’image](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) que celui utilisé dans la section précédente.

Toutefois, remplacez cette fois-ci votre condition principale par une combinaison de deux vérifications. En plus de la vérification du texte, vérifiez si le texte contient des propos injurieux. Le flux de travail crée une révision s’il trouve du texte **et** qu’il y détecte des propos injurieux.

### <a name="the-designer-view"></a>La vue Concepteur

Pour remplacer la **Condition** par une **Combinaison**, modifiez le flux de travail. L’image suivante montre la nouvelle vue qui s’affiche dans le concepteur.

![Content Moderator - Condition de flux de travail modifiée](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>La vue JSON

Sélectionnez l’onglet **JSON** pour voir la définition JSON suivante de votre flux de travail personnalisé modifié. Notez comment les instructions **If-Then** dans la définition JSON correspondent aux nouvelles étapes que vous avez ajoutées au flux de travail.

    {
        "Type": "Logic",
        "If": {
        "Left": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Right": {
            "ConnectorName": "moderator",
            "OutputName": "text.HasProfanity",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition",
            "AlternateInput": "moderator.ocrText"
            },
        "Combine": "AND",
        "Type": "Combine"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

    
### <a name="workflow-result"></a>Résultat du flux de travail

Après avoir retesté le flux de travail, vous découvrez qu’aucune révision n’est créée. Pour confirmer l’absence de toute révision, accédez à l’onglet **Image** sous **Révision**.
Le flux de travail n’a pas créé la révision, car il n’a détecté aucun propos injurieux dans le texte extrait.

![Content Moderator - Sortie de flux de travail modifiée](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>L’API de flux de travail

Les [opérations de flux de travail](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) fournissent l’interface de programmation aux fonctionnalités de flux de travail. Créez des flux de travail, obtenez les détails des flux de travail et mettez à jour les définitions de flux de travail à l’aide de l’API de flux de travail.

### <a name="get-all-workflow-details"></a>Obtenir [tous] les détails des flux de travail

L’opération **Workflow-Get** accepte les entrées suivantes :

- **team** : ID d'équipe que vous avez créé lors de la configuration du [compte de l'outil de révision](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname** : nom de votre flux de travail. Utilisez `default` pour commencer.
- **Ocp-Apim-Subscription-Key** : valeur située dans l'onglet **Paramètres**. Pour plus d’informations, consultez la [vue d’ensemble ](overview.md) de Content Moderator.

Si l’opération réussit, l’**État de la réponse** est `200 OK` et la zone **Contenu de la réponse** affiche la définition de flux de travail au format JSON.
Pour plus d’informations, lisez le [guide de démarrage rapide de la console d’API de flux de travail](try-review-api-job.md).

### <a name="create-or-update-workflow"></a>Créer ou mettre à jour un flux de travail

L’opération de création et de mise à jour permet de créer un flux de travail à partir de l’API.

L’opération **Workflow-Create or Update** accepte les entrées suivantes :

- **team** : ID d'équipe que vous avez créé lors de la configuration du [compte de l'outil de révision](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname** : nom de votre flux de travail. Utilisez `default` pour commencer.
- **Ocp-Apim-Subscription-Key** : valeur située dans l'onglet **Paramètres**. Pour plus d’informations, consultez la [vue d’ensemble ](overview.md) de Content Moderator.

Si l’opération réussit, l’**État de la réponse** est `200 OK` et la zone **Contenu de la réponse** affiche `true`. Pour en savoir plus, [testez l’opération `Create`](try-review-api-job.md).

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment créer des flux de travail personnalisés, consultez le [tutoriel de flux de travail de l’outil de révision](Review-Tool-User-Guide/Workflows.md). 

Testez la [console d’API de flux de travail](try-review-api-job.md) et utilisez les exemples de code API REST. 

Enfin, utilisez vos flux de travail personnalisés avec les opérations **Job**, comme indiqué dans [Console d’API de tâche](try-review-api-job.md) et [Démarrage rapide .NET des tâches](moderation-jobs-quickstart-dotnet.md).
