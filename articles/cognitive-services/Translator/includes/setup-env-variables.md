---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 156486f4f4f0df3d4bb4ab76492709bbecfb8eb5
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906519"
---
## <a name="set-up"></a>Installation

### <a name="create-a-translator-text-resource"></a>Créer une ressource pour la traduction de texte Translator Text

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour la traduction de texte Translator Text en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre machine locale. Vous pouvez également :

* Obtenir une [clé d’évaluation](https://azure.microsoft.com/try/cognitive-services) valide pendant 7 jours gratuitement. Une fois l’inscription terminée, elle est disponible sur le site web Azure.
* Accédez à une ressource existante dans le [portail Azure](https://portal.azure.com/).

Après avoir obtenu une clé à partir de votre ressource ou abonnement d’essai, créez deux [variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) :

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` : clé d’abonnement pour votre ressource de traduction de texte Translator Text.
* `TRANSLATOR_TEXT_ENDPOINT` : nom du point de terminaison régional ou du sous-domaine personnalisé pour votre ressource.
