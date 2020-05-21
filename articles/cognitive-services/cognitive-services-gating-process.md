---
title: Processus de régulation de Cognitive Services
titleSuffix: Azure Cognitive Services
description: Découvrez comment demander un accès anticipé aux nouveaux conteneurs et API Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: a161cc0675eedee50608b8d6c288c57147fd6d23
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599345"
---
# <a name="gating-process-for-azure-cognitive-services"></a>Processus de régulation pour Azure Cognitive Services

> [!NOTE]
> Lorsque la préversion régulée d’une offre de service est terminée, elle devient une préversion publique « non régulée » accessible sans demande spécifique. À la fin du processus de préversion, elle est publiée en disponibilité générale (GA).

À mesure qu’elles sont proposées, les nouvelles offres Azure Cognitive Services passent par une préversion régulée pour laquelle les clients doivent effectuer une demande d’accès. Ce processus de régulation permet d’identifier les opportunités d’amélioration des offres de service avant leur disponibilité générale. 

Cet article vous guide à travers le processus de demande d’accès aux offres Cognitive Services régulées.

## <a name="eligibility-and-approval-process"></a>Éligibilité et processus d’approbation

Le processus de régulation est mis en place pour tenter d’évaluer l’intérêt et mieux comprendre les besoins des clients. L’équipe Microsoft accepte les [demandes](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u) de clients commerciaux de Microsoft disposant d’un abonnement Azure et d’un scénario commercial valides. Les clients peuvent voir leur demande refusée dans les cas suivants :

 - Le client n’est associé à aucune organisation
 - Le client ne possède pas d’abonnement Azure valide
 - La demande a été envoyée via une adresse e-mail personnelle (@hotmail.com, @gmail.com, @yahoo.com)
 - Le client n’a pas fourni de justification ou de scénario commercial approprié

En raison de la demande provenant de différentes catégories de clients, nous essayons d’accélérer le processus d’approbation. Toutefois, nous ne pouvons pas nous engager à respecter un calendrier précis. Une fois sa décision prise, l’équipe Microsoft vous contacte vous et votre équipe de gestion des comptes pour les étapes suivantes. Nous vous remercions par avance pour votre compréhension et votre patience.

Si la demande est approuvée, l’équipe Microsoft vous envoie les informations, la documentation et les instructions par e-mail. Le détail des prix de Cognitive Services est disponible [ici](https://azure.microsoft.com/pricing/details/cognitive-services/).


Actuellement, les services ci-dessous sont proposés par le biais du processus de régulation :

## <a name="gated-web-apis"></a>API web régulées

|Service  |
|---------|
|Détecteur d’anomalies v2     | 

## <a name="gated-online-containers"></a>Conteneurs en ligne régulés

| Service                             | Conteneur(s)                                                                  |
|-------------------------------------|-------------------------------------------------------------------------------|
| [Vision par ordinateur][cv-containers]    | Lire                                                                          |
| [Visage][fa-containers]               | Face                                                                          |
| [Form Recognizer][fr-containers]    | Form Recognizer                                                               |
| [API Speech Service][sp-containers] | Reconnaissance vocale (Personnalisé et Standard) et conversion de texte par synthèse vocale (Personnalisé et Standard) |
| [Translator Text][tt-containers]    | Translator Text                                                               |

> [!IMPORTANT]
> Si un service ou une offre en conteneur ne figure pas dans la liste, cela signifie qu’il ou elle est soit non régulé(e), soit indisponible.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [S’inscrire pour les services régulés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

[ad-containers]: ./anomaly-detector/anomaly-detector-container-howto.md
[cv-containers]: ./computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/face-how-to-install-containers.md
[fr-containers]: ./form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ./luis/luis-container-howto.md
[sp-containers]: ./speech-service/speech-container-howto.md
[ta-containers]: ./text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: ./translator/how-to-install-containers.md
