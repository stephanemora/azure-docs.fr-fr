---
title: Exportation et suppression de données client - LUIS - Azure Cognitive Services || Microsoft Docs
description: Documentation de référence pour l’exportation et la suppression de données client dans Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/23/2018
ms.author: v-geberr;
ms.openlocfilehash: af3a96c978a3b22fcbc6296642e4749c863dff9e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928571"
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
| **Portail** | [Lien](luis-how-to-account-settings.md) | [Lien](luis-how-to-start-new-app.md#delete-app) | [Lien](luis-how-to-start-new-app.md#delete-app) | [Lien](luis-how-to-start-new-app.md#delete-app) |
| **API** | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportation des données client
Les utilisateurs de LUIS contrôlent totalement l’affichage des données sur le portail. Celles-ci doivent cependant être exportées via les API de programmation de LUIS. Le tableau suivant affiche des liens d’assistance pour l’exportation de données via les API de programmation de LUIS :

| | **Compte d’utilisateur** | **Application** | **Énoncé(s)** | **Requêtes utilisateur final** |
| --- | --- | --- | --- | --- |
| **API** | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence sur les régions LUIS](./luis-reference-regions.md)
