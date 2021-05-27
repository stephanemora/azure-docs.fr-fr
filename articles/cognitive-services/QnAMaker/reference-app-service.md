---
title: Configuration du service – QnA Maker
description: Comprendre comment et où configurer les ressources.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: 7a24045d38b594e09b931a885c60851109a108e3
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110372685"
---
# <a name="service-configuration"></a>Configuration de service

Chaque version de QnA Maker utilise un ensemble différent de ressources (services) Azure. Cet article décrit les personnalisations prises en charge pour ces services. 

## <a name="app-service"></a>App Service

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/v1)

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

# <a name="custom-question-answering-preview-release"></a>[Réponses aux questions personnalisées (préversion)](#tab/v2)

Les personnalisations de App Service ne s’appliquent pas à la fonctionnalité de réponse à une question personnalisée (préversion).

---

## <a name="qna-maker-service"></a>Service d’établissement QnA

Le service QnA Maker fournit une configuration permettant aux utilisateurs suivants de collaborer sur un seul service QnA Maker, ainsi que toutes ses bases de connaissances.

Découvrez [comment ajouter des collaborateurs](./reference-role-based-access-control.md) à votre service.

## <a name="change-azure-cognitive-search"></a>Changer le service Recherche cognitive Azure

Découvrez [comment changer le service Recherche cognitive](./how-to/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) lié à votre service QnA Maker.

## <a name="change-default-answer"></a>Changer la réponse par défaut

Découvrez [comment changer le texte de vos réponses par défaut](How-To/change-default-answer.md). 

## <a name="telemetry"></a>Télémétrie

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/v1)

Application Insights est utilisé pour superviser la télémétrie avec QnA Maker GA. Il n’existe aucun paramètre de configuration propre à QnA Maker.

# <a name="custom-question-answering-preview-release"></a>[Réponses aux questions personnalisées (préversion)](#tab/v2)

Découvrez [comment ajouter des données de télémétrie à votre service Réponses aux questions personnalisées (préversion)](How-To/get-analytics-knowledge-base.md). 

---

## <a name="app-service-plan"></a>Plan App Service

# <a name="qnamaker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/v1)

Plan App Service n’a aucun paramètre de configuration spécifique à QnA Maker.

# <a name="custom-question-answering-preview-release"></a>[Réponses aux questions personnalisées (préversion)](#tab/v2)

Le plan App Service n’est pas utilisé avec les réponses aux questions personnalisées (préversion).

---

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [formats](reference-document-format-guidelines.md) pour les documents et les URL que vous souhaitez importer dans une base de connaissances.
