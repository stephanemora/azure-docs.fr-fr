---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c737447c3a3bd2d76d3ed620b7c61aaa81250130
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70393806"
---
## <a name="set-up"></a>Configurer

### <a name="create-a-translator-text-resource"></a>Créer une ressource pour la traduction de texte Translator Text

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour la traduction de texte Translator Text en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre machine locale. Vous pouvez également :

* Obtenir une [clé d’évaluation](https://azure.microsoft.com/try/cognitive-services) valide pendant 7 jours gratuitement. Une fois l’inscription terminée, elle est disponible sur le site web Azure.
* Accédez à une ressource existante dans le [portail Azure](https://portal.azure.com/).

Après avoir obtenu une clé à partir de votre ressource ou abonnement d’essai, créez deux [variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) :

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` : clé d’abonnement pour votre ressource de traduction de texte Translator Text.
* `TRANSLATOR_TEXT_ENDPOINT` : Point de terminaison global pour Traduction de texte Translator Text. Utilisez `https://api.cognitive.microsofttranslator.com/`.
