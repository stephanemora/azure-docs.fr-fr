---
title: Améliorer la qualité des réponses avec des synonymes
description: Dans ce didacticiel, découvrez comment améliorer la réponse avec des synonymes et des mots secondaires
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/29/2021
ms.openlocfilehash: 9830eab526ad1cad4f7eb45d010958545ccec81b
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113235241"
---
# <a name="improve-quality-of-response-with-synonyms"></a>Améliorer la qualité de la réponse avec les synonymes

Ce didacticiel vous montre comment vous pouvez améliorer la qualité de vos réponses à l’aide de synonymes. Supposons que les utilisateurs n’obtiennent pas une réponse exacte à leurs requêtes lorsqu’ils utilisent d’autres formes, synonymes ou acronymes d’un mot. Par conséquent, ils décident d’améliorer la qualité de la réponse à l’aide de l'[API Alterations](/rest/api/cognitiveservices-qnamaker/QnAMaker4.0/Alterations) pour ajouter des synonymes pour les mots clés.

## <a name="add-synonyms-using-alterations-api"></a>Ajouter des synonymes à l’aide de l’API Alterations

Nous allons ajouter les mots suivants et leurs modifications pour améliorer les résultats :

|Word | Alterations|
|--------------|--------------------------------|
| corriger des problèmes | `Troubleshoot`, `trouble-shoot`|
| tableau blanc   | `white-board`, `white board`   |
| bluetooth    | `blue-tooth`, `blue tooth`     |

```json
{
    "wordAlterations": [
        {
            "alterations": [
                "fix problems",
                "trouble shoot",
                "trouble-shoot",
                ]
        },
        {
            "alterations": [
                "whiteboard",
                "white-board",
                "white board"
            ]
        },
        {
            "alterations": [
                "bluetooth",
                "blue-tooth",
                "blue tooth"
            ]
        }
    ]
}

```

## <a name="response-after-adding-synonyms"></a>Réponse après l’ajout de synonymes

Pour la paire de questions/réponses « résoudre les problèmes avec le stylet de surface » illustré ci-dessous, nous comparons la réponse pour une requête effectuée à l’aide de son synonyme « résoudre les problèmes ».

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran avec résolution des problèmes avec le stylet de surface mis en surbrillance en rouge]( ../media/adding-synonyms/fix-problems.png) ]( ../media/adding-synonyms/fix-problems.png#lightbox)

## <a name="response-before-addition-of-synonym"></a>Réponse avant l’ajout du synonyme

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran avec score de confiance de 71,82 mis en surbrillance en rouge]( ../media/adding-synonyms/confidence-score.png) ]( ../media/adding-synonyms/confidence-score.png#lightbox)

## <a name="response-after-addition-of-synonym"></a>Réponse après l’ajout d’un synonyme 

> [!div class="mx-imgBorder"]
> [ ![Capture d’écran avec score de confiance de 97 mis en surbrillance en rouge]( ../media/adding-synonyms/increase-score.png) ]( ../media/adding-synonyms/increase-score.png#lightbox)

Comme vous pouvez le voir, lorsque `trouble shoot` n’a pas été ajouté en tant que synonyme, nous obtenons une réponse de confiance faible à la requête « comment résoudre les problèmes de votre surface Pen ». Toutefois, une fois que nous avons ajouté `trouble shoot` comme synonyme de « résoudre les problèmes », nous avons reçu la réponse correcte à la requête avec un score de confiance plus élevé. Une fois ces modifications de mots ajoutées, la pertinence des résultats a été améliorée, ce qui améliore l’expérience utilisateur. 

> [!NOTE]
> Les synonymes sont insensibles à la casse. Les synonymes peuvent également ne pas fonctionner comme prévu si vous ajoutez des mots vides en tant que synonymes. La liste de mots vides est disponible ici : [liste des mots vides](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md).

Par exemple, si vous ajoutez l’abréviation **IT** pour les technologies de l’information, le système risque de ne pas pouvoir reconnaître Technologies de l’information, car **IT** est un mot d’arrêt et filtré quand la requête est traitée.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer des bases de connaissances dans plusieurs langues](multiple-languages.md)