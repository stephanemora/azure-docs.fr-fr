---
title: Vue d’ensemble de LUIS (Language Understanding)
description: 'Language Understanding (LUIS) : service d’API basé sur le cloud qui utilise le machine learning sur du langage naturel pour prédire une signification et extraire des informations.'
keywords: Azure, intelligence artificielle, ia, traitement en langage naturel, tln, compréhension du langage naturel, cln, LUIS, ia conversationnelle, chatbot ia, chatbot tln, ia tln, luis azure
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 05/17/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: d48d80170e3f1b9db83e12b34610a8b6736f42ca
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110095821"
---
# <a name="what-is-language-understanding-luis"></a>Qu’est-ce que le service Language Understanding (LUIS) ?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) est un service d’IA conversationnelle qui applique une intelligence de machine learning personnalisée au texte en langage naturel des conversations d’un utilisateur afin d’en prédire le sens général et d’en extraire des informations détaillées pertinentes. LUIS fournit un accès par le biais de son [portail personnalisé](https://www.luis.ai), des [API][endpoint-apis] et des [bibliothèques de client du SDK](client-libraries-rest-api.md).

Les utilisateurs qui se connectent pour la première fois doivent effectuer les étapes de [connexion au portail LUIS](sign-in-luis-portal.md "se connecter au portail LUIS"). Pour démarrer, vous pouvez essayer une [application de domaine prédéfini](luis-get-started-create-app.md) LUIS.

Cette documentation contient les types d’articles suivants :  

* Les [**Démarrages rapides**](luis-get-started-create-app.md) sont des instructions de prise en main qui vous guident dans la formulation de vos requêtes au service.  
* Les [**Guides pratiques**](luis-how-to-start-new-app.md) contiennent des instructions sur l’utilisation du service de manière plus spécifique ou personnalisée.  
* Les [**Concepts**](artificial-intelligence.md) fournissent des explications approfondies sur les fonctions et fonctionnalités du service.  
* Les [**Tutoriels**](tutorial-intents-only.md) sont des guides plus longs qui montrent comment utiliser le service en tant que composant dans des solutions métier élargies.  

## <a name="what-does-luis-offer"></a>Les avantages de LUIS 

* **Simplicité** : Avec LUIS, aucune obligation d’avoir un savoir-faire en IA en interne ou une connaissance préalable du machine learning. En quelques clics seulement, vous pouvez créer votre propre application d’IA conversationnelle. Vous pouvez créer votre application personnalisée en suivant l’un de nos [guides de démarrage rapide](luis-get-started-create-app.md), ou utiliser l’une de nos applications de [domaine prédéfini](luis-get-started-create-app.md).
* **Sécurité, confidentialité et conformité** : Reposant sur l’infrastructure Azure, LUIS offre une sécurité, une confidentialité et une conformité de classe entreprise. Vos données restent votre propriété ; vous pouvez les supprimer à tout moment. Vos données sont chiffrées pendant qu’elles sont stockées. En savoir plus sur ce point [ici](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy).
* **Intégration** : intégrez facilement votre application LUIS à d’autres services Microsoft tels que [Microsoft Bot Framework](/composer/tutorial/tutorial-luis), [QnA Maker](../QnAMaker/choose-natural-language-processing-service.md) et le [service Speech](../speech-service/get-started-intent-recognition.md).


## <a name="luis-scenarios"></a>Scénarios LUIS
* [Générer un bot conversationnel de classe entreprise](/azure/architecture/reference-architectures/ai/conversational-bot) : Cette architecture de référence décrit comment générer un bot conversationnel (chatbot) de classe entreprise à l’aide d’Azure Bot Framework.
* [Chatbot commercial](/azure/architecture/solution-ideas/articles/commerce-chatbot) : Ensemble, Azure Bot Service et le service Language Understanding permettent aux développeurs de créer des interfaces conversationnelles pour différents scénarios, tels que les services bancaires, les voyages et les loisirs.
* [Contrôle des appareils IoT avec un Assistant vocal](/azure/architecture/solution-ideas/articles/iot-controlling-devices-with-voice-assistant) : Créez des interfaces conversationnelles fluides avec tous vos appareils accessibles sur Internet, de votre téléviseur ou réfrigérateur connecté aux appareils d’une usine connectée.


## <a name="application-development-life-cycle"></a>Cycle de vie de développement d’application

![Cycle de vie de développement d’application LUIS](./media/luis-overview/luis-dev-lifecycle.png "Cycle de vie de développement d’application LUIS")

-   **Planifier** : Identifiez les scénarios pour lesquels les utilisateurs sont susceptibles d’utiliser votre application. Définissez les actions et les informations pertinentes qui doivent être reconnues.
-   **Générer** : Utilisez votre ressource de création pour développer votre application. Commencez par définir des [intentions](luis-concept-intent.md) et des [entités](luis-concept-entity-types.md). Ajoutez ensuite des [énoncés](luis-concept-utterance.md) d’entraînement pour chaque intention. 
-   **Tester et améliorer** : Commencez à tester votre modèle avec d’autres énoncés pour avoir une idée de la façon dont l’application se comporte, et décidez ensuite si une amélioration est nécessaire. Vous pouvez améliorer votre application en appliquant ces [bonnes pratiques](luis-concept-best-practices.md). 
-   **Publier** : Déployez votre application pour la prédiction et interrogez le point de terminaison à l’aide de votre ressource de prédiction. Apprenez-en davantage sur les ressources de création et de prédiction [ici](luis-how-to-azure-subscription.md). 
-   **Se connecter** : Connectez-vous à d’autres services tels que [Microsoft Bot Framework](/composer/tutorial/tutorial-luis), [QnA Maker](../QnAMaker/choose-natural-language-processing-service.md) et le [service Speech](../speech-service/get-started-intent-recognition.md). 
-   **Affiner** : [Passez en revue les énoncés de point de terminaison](luis-concept-review-endpoint-utterances.md) pour améliorer votre application avec des exemples concrets.

Apprenez-en davantage sur la planification et la génération de votre application [ici](luis-how-plan-your-app.md).

## <a name="next-steps"></a>Étapes suivantes

* [Nouveautés](whats-new.md "Nouveautés") du service et documentation
* [Créer une application LUIS](tutorial-intents-only.md)
* [Informations de référence sur les API][endpoint-apis]
* [Bonnes pratiques](luis-concept-best-practices.md)
* [Ressources pour les développeurs](developer-reference-resource.md "Ressources pour les développeurs") pour LUIS.
* [Planifier votre application](luis-how-plan-your-app.md "Planifier votre application") avec des [intentions](luis-concept-intent.md "intentions") et des [entités](luis-concept-entity-types.md "entities").

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/