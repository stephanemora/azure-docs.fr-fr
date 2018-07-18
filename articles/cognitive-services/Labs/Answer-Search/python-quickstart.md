---
title: Démarrage rapide Python pour Microsoft Cognitive Services, projet Recherche de réponses | Microsoft Docs
description: Exemple de prise en main du projet Recherche de réponses avec Python, Microsoft Cognitive Services sur Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9cb5406c616ed8e96d73c00c788a0d20f66dcabd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369541"
---
# <a name="project-answer-search-python-quickstart"></a>Démarrage rapide Python du projet Recherche de réponses

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
- [Démarrage rapide de Java](java-quickstart.md)
- [Démarrage rapide Node](node-quickstart.md)