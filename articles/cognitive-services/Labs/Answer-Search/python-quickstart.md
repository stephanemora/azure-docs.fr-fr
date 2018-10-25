---
title: 'Démarrage rapide : Project Answer Search, Python'
titlesuffix: Azure Cognitive Services
description: Exemple en Python pour commencer à vous servir de Project Answer Search.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 69c5edfef65af2ed1b27f9a512ad13c21468df7a
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465506"
---
# <a name="quickstart-project-answer-search-with-python"></a>Démarrage rapide : Project Answer Search avec Python

L’exemple Python suivant crée et envoie une demande d’information sur « Rock de Gibraltar ».

## <a name="prerequisites"></a>Prérequis

Obtenir une clé d’accès pour l’essai gratuit [Cognitive Services Labs](https://aka.ms/answersearchsubscription)

Cet exemple utilise Python 3.6.4.

## <a name="code-scenario"></a>Scénario de code 

Le code suivant crée un aperçu d’URL.
Il est implémenté lors des étapes suivantes :
1. Déclarez les variables pour spécifier le point de terminaison par hôte et par chemin d’accès.
2. Spécifiez l’URL de requête pour afficher un aperçu et ajoutez le paramètre de requête.  
3. Définissez le paramètre de requête.
4. Définissez la fonction de recherche qui crée la requête et ajoute l’en-tête *Ocp-Apim-Subscription-Key*.
5. Définissez l’en-tête *Ocp-Apim-Subscription-Key*. 
6. Établissez la connexion et envoyez la requête.
7. Imprimez les résultats JSON.

Voici le code complet pour cette démonstration :

````
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'https://api.labs.cognitive.microsoft.com'
path = '/answerSearch/v7.0/search '

query = 'Rock of Gibraltar'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

````
## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide C#](c-sharp-quickstart.md)
- [Démarrage rapide Java](java-quickstart.md)
- [Démarrage rapide de Node](node-quickstart.md)