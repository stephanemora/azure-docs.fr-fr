---
title: Configuration du service – QnA Maker
description: Comprendre comment et où configurer les ressources.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f504e1de8c751cb7d97679c8d56c591b0508c808
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071113"
---
# <a name="service-configuration"></a>Configuration de service

Chaque version de QnA Maker utilise un ensemble différent de ressources (services) Azure. Cet article décrit les personnalisations prises en charge pour ces services. 

## <a name="app-service"></a>App Service

QnA Maker utilise App Service pour fournir le runtime de requête utilisé par l’[API generateAnswer](/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer).

Ces paramètres sont disponibles dans le Portail Azure, pour App Service. Les paramètres sont disponibles en sélectionnant **Paramètres**, puis **Configuration**.

Vous pouvez définir un paramètre individuel via la liste des paramètres de l’application ou modifier plusieurs paramètres en sélectionnant **Modification avancée**.

|Ressource|Paramètre|
|--|--|
|AzureSearchAdminKey|Recherche cognitive : utilisée pour le stockage des paires de questions et réponses et le classement n°1|
|AzureSearchName|Recherche cognitive : utilisée pour le stockage des paires de questions et réponses et le classement n°1|
|DefaultAnswer|Texte de réponse quand aucune correspondance n’est trouvée|
|UserAppInsightsAppId|Journal et télémétrie de conversation|
|UserAppInsightsKey|Journal et télémétrie de conversation|
|UserAppInsightsName|Journal et télémétrie de conversation|
|QNAMAKER_EXTENSION_VERSION|Choisissez toujours la valeur _le plus récent_. Ce paramètre initialisera l’extension de site QnAMaker dans App Service.|

Vous devez **redémarrer** le service à partir de la page **Vue d’ensemble** du Portail Azure, une fois que vous avez apporté des modifications.

## <a name="qna-maker-service"></a>Service d’établissement QnA

Le service QnA Maker fournit une configuration permettant aux utilisateurs suivants de collaborer sur un seul service QnA Maker, ainsi que toutes ses bases de connaissances.

Découvrez [comment ajouter des collaborateurs](./index.yml) à votre service.

## <a name="change-azure-cognitive-search"></a>Changer le service Recherche cognitive Azure

Découvrez [comment changer le service Recherche cognitive](./how-to/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) lié à votre service QnA Maker.

## <a name="change-default-answer"></a>Changer la réponse par défaut

Découvrez [comment changer le texte de vos réponses par défaut](How-To/change-default-answer.md). 

## <a name="telemetry"></a>Télémétrie

Application Insights est utilisé pour superviser la télémétrie avec QnA Maker GA. Il n’existe aucun paramètre de configuration propre à QnA Maker.

## <a name="app-service-plan"></a>Plan App Service

Plan App Service n’a aucun paramètre de configuration spécifique à QnA Maker.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [formats](reference-document-format-guidelines.md) pour les documents et les URL que vous souhaitez importer dans une base de connaissances.
