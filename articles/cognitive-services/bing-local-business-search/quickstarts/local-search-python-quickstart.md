---
title: 'Démarrage rapide : Envoyer une requête à l’API en Python avec Recherche d’entreprises locales Bing'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour commencer à utiliser l’API Recherche d’entreprises locales Bing en Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 30e66260f0603139bf45f9a0f0b5f19539ae468e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87843553"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Démarrage rapide : Envoyer une requête à l’API Recherche d’entreprises locales Bing en Python

Utilisez ce guide de démarrage rapide pour découvrir comment envoyer des demandes à l’API Recherche d’entreprises locales Bing, un service Azure Cognitive Services. Bien que cette application simple soit écrite en Python, l’API est un service web RESTful compatible avec tous les langages de programmation capables de formuler des requêtes HTTP et d’analyser du JSON.

Cet exemple d’application récupère des données de réponse locales auprès de l’API pour une requête de recherche.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/) 2.x ou 3.x.
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Créer une ressource Recherche Bing"  target="_blank">créez une ressource Recherche Bing<span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.

## <a name="run-the-complete-application"></a>Exécuter l’application complète

L’exemple suivant obtient les résultats localisés, qui sont implémentés dans les étapes suivantes :
1. Déclarez les variables pour spécifier le point de terminaison par hôte et par chemin d’accès.
2. Spécifiez le paramètre de requête. 
3. Définissez la fonction de recherche qui crée la requête et ajoute l’en-tête `Ocp-Apim-Subscription-Key`.
4. Définissez l’en-tête `Ocp-Apim-Subscription-Key`. 
5. Établissez la connexion et envoyez la requête.
6. Imprimez les résultats JSON.

Voici le code complet pour cette démonstration :

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide Java de la Recherche d’entreprises locales](local-search-java-quickstart.md)
- [Démarrage rapide C# de la Recherche d’entreprises locales](local-quickstart.md)
- [Démarrage rapide Node.js de la Recherche d’entreprises locales](local-search-node-quickstart.md)
