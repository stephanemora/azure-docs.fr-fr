---
title: Exporter et supprimer des données
titleSuffix: Azure Cognitive Services
description: Language Understanding Intelligent Service (LUIS) préserve le contenu du client pour utiliser le service, mais l’utilisateur de LUIS contrôle totalement l’affichage, l’exportation et la suppression des ses données. Il peut le faire par le biais du portail web LUIS ou des API de programmation de LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 62d6dce4ccb5d4badbcf9b02cbbcda576ee63212
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869243"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exporter et supprimer vos données client dans Language Understanding (LUIS) dans Cognitive Services

## <a name="summary-of-customer-data-request-features"></a>Résumé des fonctionnalités de demande de données client
Language Understanding Intelligent Service (LUIS) préserve le contenu du client pour utiliser le service, mais l’utilisateur de LUIS contrôle totalement l’affichage, l’exportation et la suppression des ses données. Il peut le faire via le [portail](luis-reference-regions.md) web LUIS ou les [API de programmation de LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Le contenu du client est chiffré dans le stockage Azure régional Microsoft et inclut ce qui suit :

- Contenu du compte utilisateur collecté lors de l’inscription
- Données d’apprentissage requises pour générer les modèles (c’est-à-dire les intention et les entités)
- Requêtes utilisateur journalisées lors de l’exécution afin d’améliorer les modèles de l’utilisateur
  - Les utilisateurs peuvent désactiver la journalisation des requêtes en ajoutant `&log=false` à la demande. Pour plus de détails, voir [ici](luis-resources-faq.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Suppression des données client
Les utilisateurs de LUIS contrôlent totalement la suppression de tout contenu utilisateur, via le portail web de LUIS ou à l’aide des API de programmation de LUIS. Le tableau suivant affiche les liens d’assistance pour les deux :

| | **Compte d’utilisateur** | **Application** | **Énoncé(s)** | **Requêtes utilisateur final** |
| --- | --- | --- | --- | --- |
| **Portal** | [Lien](luis-how-to-account-settings.md) | [Lien](luis-how-to-start-new-app.md#delete-app) | [Lien](luis-how-to-start-new-app.md#delete-app) | [Lien](luis-how-to-start-new-app.md#delete-app) |
| **API** | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportation des données client
Les utilisateurs de LUIS contrôlent totalement l’affichage des données sur le portail. Celles-ci doivent cependant être exportées via les API de programmation de LUIS. Le tableau suivant affiche des liens d’assistance pour l’exportation de données via les API de programmation de LUIS :

| | **Compte d’utilisateur** | **Application** | **Énoncé(s)** | **Requêtes utilisateur final** |
| --- | --- | --- | --- | --- |
| **API** | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence sur les régions LUIS](./luis-reference-regions.md)
