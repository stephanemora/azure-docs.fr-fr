---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: a936afc611462b6ec3c3de9021d517ccf66f666b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586502"
---
## <a name="set-up"></a>Configurer

### <a name="create-a-translator-resource"></a>Créer une ressource pour Translator

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Translator avec le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre machine locale. Vous pouvez également :

* Obtenir une [clé d’évaluation](https://azure.microsoft.com/try/cognitive-services) valide pendant 7 jours gratuitement. Une fois l’inscription terminée, elle est disponible sur le site web Azure.
* Accédez à une ressource existante dans le [portail Azure](https://portal.azure.com/).

Après avoir obtenu une clé à partir de votre ressource ou abonnement d’essai, créez deux [variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) :

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` : clé d’abonnement pour votre ressource Translator.
* `TRANSLATOR_TEXT_ENDPOINT` : point de terminaison global pour Translator. Utilisez `https://api.cognitive.microsofttranslator.com/`.
