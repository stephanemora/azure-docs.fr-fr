---
title: Modèles prédéfinis - entité, intention, domaine - LUIS
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 2a978665eff2f8aa3eab3c7d70b39bdb61ecfda4
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651808"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>Modèles de domaine, d’intention et d’entité prédéfinis

Les modèles prédéfinis fournissent des domaines, des intentions, des énoncés et des entités. Vous pouvez démarrer votre application avec un domaine prédéfini ou ajouter un domaine approprié à votre application ultérieurement. 

## <a name="types-of-prebuilt-models"></a>Types de modèles prédéfinis

LUIS fournit 3 types de modèles prédéfinis. Chaque modèle peut être ajouté à votre application à tout moment. 

|Type de modèle|Inclus|
|--|--|
|Domaine|Intentions, énoncés, entités|
|Intentions|Intentions, énoncés|
|Entités|Entités uniquement| 

## <a name="prebuilt-domains"></a>Domaines prédéfinis

Language Understanding (LUIS) fournit des *domaines prédéfinis*, à savoir des ensembles prédéfinis d’[intentions](luis-how-to-add-intents.md) et d’[entités](luis-concept-entity-types.md) qui fonctionnent conjointement pour des domaines ou des catégories communes d’applications clientes. 

Les domaines prédéfinis sont formés et prêts à êtres ajoutés à votre application LUIS. Les intentions et les entités dans un domaine prédéfini sont entièrement personnalisables, une fois que vous les avez ajoutées à votre application. 

Si vous commencez par personnaliser un domaine prédéfini entier, supprimez les intentions et les entités dont votre application n’a pas besoin. Vous pouvez également ajouter des intentions ou des entités à l’ensemble que le domaine prédéfini fournit déjà. Par exemple, si vous utilisez le domaine prédéfini **Événements** pour une application d’événements sportifs, vous pouvez pour ajouter des entités pour les équipes sportives. Lorsque vous commencez à [fournir des énoncés](luis-how-to-add-example-utterances.md) à LUIS, incluez les conditions propres à votre application. LUIS apprend à les reconnaître et adapte les intentions et les entités du domaine prédéfini aux besoins de votre application. 

> [!TIP]
> Les intentions et les entités d’un domaine prédéfini fonctionnent le mieux ensemble. Il est préférable de combiner des intentions et des entités du même domaine lorsque cela est possible.
> Le domaine prédéfini Utilitaires comprend des intentions que vous pouvez personnaliser pour une utilisation dans n’importe quel domaine. Par exemple, vous pouvez ajouter `Utilities.Repeat` à votre application et lui apprendre à reconnaître les actions que les utilisateurs souhaitent répéter dans votre application. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Modification du comportement d’une intention de domaine prédéfini

Vous constaterez peut-être qu’un domaine prédéfini contient une intention semblable à une intention que vous souhaitez avoir dans votre application LUIS, mais avec un comportement différent. Par exemple, le domaine prédéfini **Lieux** fournit une intention `MakeReservation` pour effectuer une réservation au restaurant, mais vous voulez que votre application utilise cette intention pour faire des réservations d’hôtel. Dans ce cas, vous pouvez modifier le comportement de cette intention en fournissant des énoncés à LUIS sur les réservations d’hôtel et en les étiquetant à l’aide de l’intention `MakeReservation`. LUIS peut alors être formé à reconnaître l’intention `MakeReservation` dans une requête de réservation d’hôtel.

Vous trouverez une liste complète des domaines prédéfinis dans la [référence des domaines prédéfinis](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Intentions prédéfinies

LUIS fournit les intentions prédéfinies et leurs énoncés. Les intentions peuvent être ajoutées sans ajouter le domaine entier. Pour ajouter une intention, il faut ajouter une intention et ses énoncés. Le nom de l’intention et la liste d’énoncés peuvent être modifiés.  

## <a name="prebuilt-entities"></a>Entités prédéfinies

LUIS comprend un ensemble d’entités prédéfinies pour reconnaître des types courants d’informations telles que des dates, heures, nombres, mesures et devises. La prise en charge de l’entité prédéfinie varie selon la culture de votre application LUIS. Pour obtenir la liste complète des entités prédéfinies que LUIS prend en charge, notamment en lien avec la culture, voir la [documentation de référence sur les entités prédéfinies](./luis-reference-prebuilt-entities.md).

Quand une entité prédéfinie est incluse dans votre application, ses prédictions sont incluses dans votre application publiée. Le comportement des entités prédéfinies est préformé et **ne peut pas** être modifié. Procédez comme suit pour voir comment fonctionne une entité prédéfinie :

> [!NOTE]
> L’entité **builtin.datetime** est désapprouvée. Elle est remplacée par l’entité [**builtin.datetimev2**](luis-reference-prebuilt-datetimev2.md), qui fournit la reconnaissance des plages de dates et d’heures, ainsi qu’une reconnaissance améliorée des dates et heures ambiguës.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [ajouter des entités prédéfinies](luis-prebuilt-entities.md) à votre application.