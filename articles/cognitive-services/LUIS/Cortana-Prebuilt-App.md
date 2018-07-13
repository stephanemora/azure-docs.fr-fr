---
title: Utiliser l’application prédéfinie Cortana de LUIS | Microsoft Docs
description: Utilisez l’assistant personnel Cortana, une application prédéfinie de LUIS (Language Understanding Intelligent Services).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: b792d090d037ef180258a1634d4bd063c0a71b9a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369133"
---
# <a name="cortana-prebuilt-app"></a>Application prédéfinie Cortana

> [!IMPORTANT]
> Nous vous recommandons d’utiliser les [domaines prédéfinis](./luis-how-to-use-prebuilt-domains.md) plutôt que l’application prédéfinie Cortana. Par exemple, au lieu de **builtin.intent.calendar.create_calendar_entry**, utilisez **Calendar.Add** du domaine prédéfini **Calendar**.
> Les domaines prédéfinis offrent les avantages suivants : 
> * Ils fournissent des packages d’intentions et d’entités prédéfinies et pré-entraînées qui ont été conçues pour fonctionner correctement les unes avec les autres. Vous pouvez intégrer un domaine prédéfini directement dans votre application. Par exemple, si vous générez une application de suivi de remise en forme, vous pouvez ajouter le domaine **Fitness** et avoir un jeu complet d’intentions et d’entités pour le suivi des activités de remise en forme, notamment des intentions pour le suivi du poids et la planification des repas, le temps ou la distance restante, et l’enregistrement de notes d’activité de remise en forme.
> * Les intentions de domaine prédéfinies sont personnalisables. Par exemple, si vous souhaitez fournir des évaluations d’hôtels, vous pouvez entraîner et personnaliser l’intention **Places.GetReviews** du domaine **Places** de façon à reconnaître les demandes d’évaluations d’hôtels.
> * Les domaines prédéfinis sont extensibles. Par exemple, si vous souhaitez utiliser le domaine prédéfini **Places** dans un bot qui recherche des restaurants et que vous avez besoin d’une intention pour obtenir le type de cuisine, vous pouvez générer et entraîner une intention **Places.GetCuisine**.

LUIS vous permet non seulement de créer vos propres applications, mais il fournit également des entités et des intentions à partir de l’assistant personnel Microsoft Cortana en tant qu’application prédéfinie. Le comportement de cette application LUIS accessible à tous ne peut pas être changé. Les intentions et les entités dans cette application ne peuvent pas être modifiées ou intégrées dans d’autres applications LUIS. Si vous souhaitez que votre application cliente ait accès à la fois à cette application prédéfinie et à votre propre application LUIS, votre application cliente doit référencer les deux applications LUIS.

L’application d’assistant personnel prédéfinie est disponible dans les cultures (paramètres régionaux) suivantes : anglais, français, italien, espagnol et chinois.

## <a name="get-the-endpoint-for-the-cortana-prebuilt-app"></a>Obtenir le point de terminaison pour l’application prédéfinie Cortana

Vous pouvez accéder à l’application prédéfinie Cortana à l’aide des points de terminaison suivants : 

| Langue | Point de terminaison|
|--------| ------------------|
| Français| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821|
|    Chinois| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c27c4af7-d44a-436f-a081-841bb834fa29|
|    Français| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/0355ead1-2d08-4955-ab95-e263766e8392|
|    Espagnol| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/cb2675e5-fbea-4f8b-8951-f071e9fc7b38|
|    Italien| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/30a0fddc-36f4-4488-b022-03de084c1633|


> [!NOTE]
> Les URL de point de terminaison sont également disponibles à partir de l’API [Applications - Obtenir des applications d’assistant personnel](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c32).

## <a name="try-out-the-personal-assistant-app"></a>Tester l’application d’assistant personnel
Pour appeler le point de terminaison, vous pouvez ajouter votre argument de clé abonnement et votre chaîne de requête au point de terminaison. 

Par exemple, si l’énoncé que vous souhaitez interpréter est « créer un rendez-vous pour la réunion d’équipe », vous pouvez ajouter cet énoncé à l’URL de point de terminaison. 

```
https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821?subscription-key={YOUR-SUBSCRIPTION-KEY}&q=create an appointment for team meeting
```

Vous pouvez coller l’URL dans un navigateur web et remplacez le champ `{YOUR-SUBSCRIPTION-KEY}` par votre clé d’abonnement.

Dans le navigateur, vous pouvez constater que l’application prédéfinie Cortana identifie `builtin.intent.calendar.create_calendar_entry` comme intention, et `builtin.calendar.title` comme type d’entité, pour l’énoncé `create an appointment for team meeting`.

![Utiliser l’application prédéfinie Cortana](./media/luis-how-to-prebuilt-cortana/luis-prebuilt-cortana-browser.png)

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Informations de référence sur l’application prédéfinie Cortana](./luis-reference-cortana-prebuilt.md)

