---
title: 'Démarrage rapide : Analyser le contenu d’un texte (Python) - Content Moderator'
titleSuffix: Azure Cognitive Services
description: Comment analyser le contenu d’un texte pour trouver des éléments répréhensibles à l’aide du kit SDK Content Moderator pour Python
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: ceaaa9bdb5f6e5c977e0c1a71af080f22d52a5e7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561218"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>Démarrage rapide : Analyser le contenu d’un texte pour trouver des éléments répréhensibles dans Python

Cet article fournit des informations et des exemples de code qui vont vous aider à prendre en main le kit SDK Content Moderator pour Python. Vous allez apprendre à exécuter le filtrage basé sur des termes et la classification d’un contenu textuel dans le but de modérer les matériaux potentiellement répréhensibles.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="prerequisites"></a>Prérequis
- Une clé d’abonnement Content Moderator. Suivez les instructions dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner à Content Moderator et obtenir votre clé.
- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- Outil [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="get-the-content-moderator-sdk"></a>Obtenir le kit SDK Content Moderator

Installez le kit SDK Python Content Moderator en ouvrant une invite de commandes et en exécutant la commande suivante :

```bash
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>Modules d’importation

Créez un script Python nommé _ContentModeratorQS.py_ et ajoutez le code suivant pour importer les composants nécessaires du SDK. Le module pretty-print est inclus afin de faciliter la lecture de la réponse JSON.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=imports)]


## <a name="initialize-variables"></a>Initialiser les variables

Ajoutez ensuite des variables pour l’URL de point de terminaison et la clé de votre abonnement Content Moderator. Ajoutez le nom `CONTENT_MODERATOR_SUBSCRIPTION_KEY` à vos variables d’environnement avec votre clé d’abonnement comme valeur. Pour l’URL de votre point de terminaison de base, ajoutez `CONTENT_MODERATOR_ENDPOINT` à vos variables d’environnement avec votre URL propre à la région comme valeur, par exemple `https://westus.api.cognitive.microsoft.com`. Les clés d’abonnement d’essai gratuit sont générées dans la région **westus**.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=authentication)]

Une chaîne de texte multiligne à partir d’un fichier sera modérée. Incluez le fichier [content_moderator_text_moderation.txt](https://github.com/Azure-Samples/cognitive-services-content-moderator-samples/blob/master/documentation-samples/python/content_moderator_text_moderation.txt) dans votre dossier racine local et ajoutez son nom de fichier à vos variables :

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModerationFile)]

## <a name="query-the-moderator-service"></a>Interroger le service Moderator

Créez une instance **ContentModeratorClient** à l’aide de l’URL de point de terminaison et la clé de votre abonnement. 

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=client)]

Ensuite, utilisez votre client avec son instance membre **TextModerationOperations** pour appeler l’API de modération avec la fonction `screen_text`. Consultez la documentation de référence **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** pour plus d’informations sur la manière de l’appeler.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModeration)]

## <a name="check-the-printed-response"></a>Vérifier la réponse imprimée

Exécutez l’exemple et confirmez la réponse. Une fois l’opération terminée, une instance **Screen** est retournée. Un résultat positif est illustré ci-dessous :

```console
{'auto_corrected_text': '" Is this a garbage email abide@ abed. com, phone: '
                        '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                        'Redmond, WA 98052. Crap is the profanity here. Is '
                        'this information PII? phone 3144444444\\ n"',
 'classification': {'category1': {'score': 0.00025233393535017967},
                    'category2': {'score': 0.18468093872070312},
                    'category3': {'score': 0.9879999756813049},
                    'review_recommended': True},
 'language': 'eng',
 'normalized_text': '" Is this a garbage email abide@ abed. com, phone: '
                    '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                    'Redmond, WA 98052. Crap is the profanity here. Is this '
                    'information PII? phone 3144444444\\ n"',
 'original_text': '"Is this a grabage email abcdef@abcd.com, phone: '
                  '6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, '
                  'WA 98052. Crap is the profanity here. Is this information '
                  'PII? phone 3144444444\\n"',
 'pii': {'address': [{'index': 82,
                      'text': '1 Microsoft Way, Redmond, WA 98052'}],
         'email': [{'detected': 'abcdef@abcd.com',
                    'index': 25,
                    'sub_type': 'Regular',
                    'text': 'abcdef@abcd.com'}],
         'ipa': [{'index': 65, 'sub_type': 'IPV4', 'text': '255.255.255.255'}],
         'phone': [{'country_code': 'US', 'index': 49, 'text': '6657789887'},
                   {'country_code': 'US', 'index': 177, 'text': '3144444444'}]},
 'status': {'code': 3000, 'description': 'OK'},
 'terms': [{'index': 118, 'list_id': 0, 'original_index': 118, 'term': 'crap'}],
 'tracking_id': 'b253515c-e713-4316-a016-8397662a3f1a'}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez développé un script Python simple qui utilise le service Content Moderator pour retourner des informations pertinentes sur un exemple de texte donné. Ensuite, apprenez-en davantage sur la signification des différents indicateurs et classifications, afin que vous puissiez décider de quelles données vous avez besoin et la manière votre application doit les gérer.

> [!div class="nextstepaction"]
> [Guide de modération du texte](text-moderation-api.md)
