---
title: Exporter et supprimer des données
titleSuffix: Azure Cognitive Services
description: Supprimer les données de client pour garantir la confidentialité et conformité.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: a82452f4b41aee9c4ea6f269d92fbc91a5697d16
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916953"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exporter et supprimer vos données client dans Language Understanding (LUIS) dans Cognitive Services

Supprimer les données de client pour garantir la confidentialité et conformité. 

## <a name="summary-of-customer-data-request-features"></a>Résumé des fonctionnalités de demande de données client
Language Understanding Intelligent Service (LUIS) préserve le contenu du client pour utiliser le service, mais l’utilisateur de LUIS contrôle totalement l’affichage, l’exportation et la suppression des ses données. Cela est possible via le web LUIS [portal](luis-reference-regions.md) ou [LUIS création des API (également appelé par programmation)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Le contenu du client est chiffré dans le stockage Azure régional Microsoft et inclut ce qui suit :

- Contenu du compte utilisateur collecté lors de l’inscription
- Données d’apprentissage nécessaires pour générer les modèles
- Connecté les requêtes utilisateur utilisées par [apprentissage actif](luis-concept-review-endpoint-utterances.md) pour aider à améliorer le modèle
  - Les utilisateurs peuvent désactiver la journalisation des requêtes en ajoutant `&log=false` à la demande. Pour plus de détails, voir [ici](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Suppression des données client
Les utilisateurs de LUIS ont un contrôle total à supprimer n’importe quel utilisateur contenu, par le biais du portail web de LUIS ou les API de création de LUIS (également appelé par programmation). Le tableau suivant affiche les liens d’assistance pour les deux :

| | **Compte d’utilisateur** | **Application** | **Exemple Utterance(s)** | **Requêtes utilisateur final** |
| --- | --- | --- | --- | --- |
| **Portal** | [Lien](luis-concept-data-storage.md#delete-an-account) | [Lien](luis-how-to-start-new-app.md#delete-app) | [Lien](luis-concept-data-storage.md#utterances-in-an-intent) | [Énoncés d’apprentissage actif](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Énoncés connectés](luis-concept-data-storage.md#disable-logging-utterances) |
| **API** | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Exportation des données client
LUIS les utilisateurs ont accès pour afficher les données sur le portail, mais il doit être exporté via les API (également appelé par programmation) Création LUIS. Le tableau suivant affiche des liens de l’assistance des exportations de données via les API (également appelé par programmation) LUIS création :

| | **Compte d’utilisateur** | **Application** | **Énoncé(s)** | **Requêtes utilisateur final** |
| --- | --- | --- | --- | --- |
| **API** | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Lien](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Emplacement de l’apprentissage actif

Pour activer [apprentissage actif](luis-how-to-review-endpoint-utterances.md#enable-active-learning), énoncés connecté d’utilisateurs, reçues au niveau des points de terminaison LUIS publiés, sont stockés dans des zones géographiques Azure suivantes :

* [Europe](#europe)
* [Australie](#australia)
* [États-Unis](#united-states)

À l’exception des données d’apprentissage actif (décrits ci-dessous), LUIS suit le [pratiques de stockage de données pour les services régionaux](https://azuredatacentermap.azurewebsites.net/). 

### <a name="europe"></a>Europe

Le [eu.luis.ai](https://eu.luis.ai) Europe création (également appelé API par programme) et portail sont hébergés dans geography d’Europe d’Azure. Le portail de eu.luis.ai et la création de l’Europe (également appelé API par programme) prennent en charge le déploiement de points de terminaison pour les zones géographiques Azure suivantes :

* Europe
* France
* Royaume-Uni

Lors du déploiement de ces zones géographiques Azure, énoncés reçus par le point de terminaison à partir des utilisateurs finaux de votre application seront stockées dans geography d’Europe d’Azure pour un apprentissage actif. Vous pouvez désactiver l’apprentissage actif, consultez [désactiver apprentissage actif](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Pour gérer les énoncés stockées, consultez [supprimer énoncé](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Australie

Le [au.luis.ai](https://au.luis.ai) portail et la création de l’Australie (également appelé API par programme) sont hébergés dans geography de l’Australie d’Azure. Le portail de au.luis.ai et la création de l’Australie (également appelé API par programme) prennent en charge le déploiement de points de terminaison pour les zones géographiques Azure suivantes :

* Australie

Lors du déploiement de ces zones géographiques Azure, énoncés reçus par le point de terminaison à partir des utilisateurs finaux de votre application seront stockées dans geography de l’Australie d’Azure pour un apprentissage actif. Vous pouvez désactiver l’apprentissage actif, consultez [désactiver apprentissage actif](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Pour gérer les énoncés stockées, consultez [supprimer énoncé](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>États-Unis

Le [luis.ai](https://www.luis.ai) United States création (également appelé API par programme) et portail sont hébergés dans geography des États-Unis d’Azure. Luis.ai portail les États-Unis création (également appelé API par programme) prennent en charge le déploiement de points de terminaison pour les zones géographiques Azure suivantes :

* Zones géographiques Azure ne pas pris en charge par l’Europe ou les régions de la création de l’Australie

Lors du déploiement de ces zones géographiques Azure, énoncés reçus par le point de terminaison à partir des utilisateurs finaux de votre application seront stockées dans la zone géographique des États-Unis d’Azure pour un apprentissage actif. Vous pouvez désactiver l’apprentissage actif, consultez [désactiver apprentissage actif](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Pour gérer les énoncés stockées, consultez [supprimer énoncé](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence sur les régions LUIS](./luis-reference-regions.md)
