---
title: Exemple de conteneur d’exécution de la commande d’exécution du docker
titleSuffix: Azure Cognitive Services
description: Commande Docker run du conteneur Analyse des sentiments
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 7ece961054c945ce949886976dc45e1178a616b6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677539"
---
Pour exécuter le conteneur *Analyse des sentiments v3*, exécutez la commande `docker run` suivante. Remplacez les espaces réservés suivants par vos valeurs :

| Espace réservé | Valeur | Format ou exemple |
|-------------|-------|---|
| **{LANGUAGE}** | Langue du conteneur vous voulez exécuter. Veillez à que ceci corresponde à la commande `docker pull` que vous avez utilisée. Notez le trait d’union (`-`) utilisé avant la langue dans l’exemple ci-dessous. | `en` |
| **{API_KEY}** | Clé de votre ressource Analyse de texte. Cette information est disponible dans le portail Azure, sur la page **Key and endpoint** (Clé et point de terminaison) de votre ressource. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Point de terminaison pour accéder à l’API Analyse de texte. Cette information est disponible dans le portail Azure, sur la page **Key and endpoint** (Clé et point de terminaison) de votre ressource. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment-{LANGUAGE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cette commande :

* Exécute un conteneur *Analyse des sentiments* à partir de l’image conteneur
* Alloue un cœur de processeur et 8 gigaoctets (Go) de mémoire
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte.