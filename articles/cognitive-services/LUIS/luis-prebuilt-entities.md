---
title: Entités prédéfinies pour Language Understanding (LUIS).
titleSuffix: Azure Cognitive Services
description: LUIS comprend un ensemble d’entités prédéfinies pour reconnaître des types courants d’informations telles que des dates, heures, nombres, mesures et devises. La prise en charge de l’entité prédéfinie varie selon la culture de votre application LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: e3bd203c9ab1d6daaae04866cf195b3ca28c3078
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041555"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Entités prédéfinies pour reconnaître les types de données courants

LUIS comprend un ensemble d’entités prédéfinies pour reconnaître des types courants d’informations telles que des dates, heures, nombres, mesures et devises. La prise en charge de l’entité prédéfinie varie selon la culture de votre application LUIS. Pour obtenir la liste complète des entités prédéfinies que LUIS prend en charge, notamment en lien avec la culture, voir la [documentation de référence sur les entités prédéfinies](./luis-reference-prebuilt-entities.md).

> [!NOTE]
> L’entité **builtin.datetime** est désapprouvée. Elle est remplacée par l’entité [**builtin.datetimev2**](luis-reference-prebuilt-datetimev2.md), qui fournit la reconnaissance des plages de dates et d’heures, ainsi qu’une reconnaissance améliorée des dates et heures ambiguës.

## <a name="add-a-prebuilt-entity"></a>Ajouter une entité prédéfinie

1. Ouvrez votre application en cliquant sur son nom dans la page **Mes applications**, puis cliquez sur **Entités** du côté gauche. 
2. Dans la page **Entités**, cliquez sur **Manage prebuilt entities** (Gérer les entités prédéfinies).

3. Dans la boîte de dialogue **Add prebuilt entities** (Ajouter des entités prédéfinies), cliquez sur l’entité prédéfinie que vous souhaitez ajouter (par exemple, « datetimev2 »). Cliquez ensuite sur **Enregistrer**.

    ![Boîte de dialogue Add prebuilt entities (Ajouter des entités prédéfinies)](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## <a name="use-a-prebuilt-number-entity"></a>Utiliser une entité number prédéfinie
Quand une entité prédéfinie est incluse dans votre application, ses prédictions sont incluses dans votre application publiée. Le comportement des entités prédéfinies est préformé et **ne peut pas** être modifié. Procédez comme suit pour voir comment fonctionne une entité prédéfinie :

1. Ajoutez une entité **number** à votre application, puis [formez](luis-interactive-test.md) et [publiez](luis-how-to-publish-app.md) l’application.
2. Cliquez sur l’URL du point de terminaison dans la page **Publier l’application** pour ouvrir le point de terminaison de LUIS dans un navigateur web. 
3. Ajoutez un énoncé à l’URL contenant une expression numérique. Par exemple, vous pouvez taper dans `buy two plane ticktets` et voir que LUIS identifie `two` en tant qu’entité `builtin.number`, et `2` en tant que valeur de celle-ci dans le champ `resolution`. Le champ `resolution` vous permet de résoudre les nombres et les dates dans une forme canonique plus facile à utiliser pour votre application cliente. 

    ![Énoncé dans le navigateur contenant une entité number](./media/luis-use-prebuilt-entity/browser-query.png)

LUIS peut reconnaître intelligemment les nombres dont la forme n’est pas standard. Essayez différentes expressions numériques dans vos énoncés, et voyez ce que LUIS renvoie.

L’exemple suivant montre une réponse JSON de LUIS, qui inclut la résolution de la valeur 24 pour l’énoncé "two dozen" (deux douzaines).

```json
{
  "query": "order two dozen tickets for group travel",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.905443209
  },
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "value": "24"
      }
    }
  ]
}
```
## <a name="use-a-prebuilt-datetimev2-entity"></a>Utiliser une entité datetimev2 prédéfinie
L’entité prédéfinie **datetimev2** reconnaît les dates, les heures, les plages de dates et les durées. Pour voir comment fonctionne l’entité prédéfinie `datetimeV2`, procédez comme suit :

1. Ajoutez une entité **datetimeV2** à votre application, puis [formez](luis-interactive-test.md) et [publiez](luis-how-to-publish-app.md) l’application.
2. Cliquez sur l’URL du point de terminaison dans la page **Publier l’application** pour ouvrir le point de terminaison de LUIS dans un navigateur web. 
3. Ajoutez un énoncé à l’URL contenant une plage de dates. Par exemple, vous pouvez taper dans `book a flight tomorrow` et voir que LUIS identifie `tomorrow` en tant qu’entité `builtin.datetimeV2.date`, et la date de demain en tant que valeur de celle-ci dans le champ `resolution`. 

L’exemple suivant montre l’apparence possible de la réponse JSON de LUIS si la date du jour est le 31 octobre 2017.

```json
{
  "query": "book a flight tomorrow",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9063408
  },
  "entities": [
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 14,
      "endIndex": 21,
      "resolution": {
        "values": [
          {
            "timex": "2017-11-01",
            "type": "date",
            "value": "2017-11-01"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Documentation de référence sur les entités prédéfinies](./luis-reference-prebuilt-entities.md)
