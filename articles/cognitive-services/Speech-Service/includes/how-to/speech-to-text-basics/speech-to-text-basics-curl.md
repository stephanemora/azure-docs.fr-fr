---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 36d12b29054f736b65af5ac411adbc26d870b982
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014141"
---
Dans ce guide de démarrage rapide, vous allez apprendre à convertir de la parole en texte à l’aide du service Speech et de cURL.

Pour obtenir une vue d’ensemble des concepts de reconnaissance vocale, consultez l’article de [présentation](../../../speech-to-text.md).

## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous disposez d’un compte Azure et d’un abonnement au service Speech. Si vous n’avez pas de compte et d’abonnement, [essayez le service Speech gratuitement](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-speech-to-text"></a>Convertir la parole en texte

Depuis une invite de commandes, exécutez la commande suivante. Vous devrez insérer les valeurs suivantes dans la commande.
- Votre clé d’abonnement du service Speech.
- Votre région du service Speech.
- Le chemin du fichier audio d’entrée. Vous pouvez générer des fichiers audio en utilisant la [synthèse vocale](../../../get-started-text-to-speech.md).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

Vous devez recevoir une réponse semblable à la suivante.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

Pour plus d’informations, consultez les [informations de référence sur l’API REST de reconnaissance vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text).
