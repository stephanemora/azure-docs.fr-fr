---
title: Exemple de conteneur d’exécution de la commande d’exécution du docker
titleSuffix: Azure Cognitive Services
description: Commande docker run pour le conteneur de santé
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 72a877ab8cfda126016376017c0a7eb42b1186d6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108918"
---
Pour exécuter le conteneur, commencez par rechercher son ID d’image :
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Exécutez la commande `docker run` suivante. Remplacez les espaces réservés suivants par vos valeurs :

| Espace réservé | Valeur | Format ou exemple |
|-------------|-------|---|
| **{API_KEY}** | Clé de votre ressource Analyse de texte. Cette information est disponible dans le portail Azure, sur la page **Key and endpoint** (Clé et point de terminaison) de votre ressource. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Point de terminaison pour accéder à l’API Analyse de texte. Cette information est disponible dans le portail Azure, sur la page **Key and endpoint** (Clé et point de terminaison) de votre ressource. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | ID d’image du conteneur. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | Répertoire d’entrée du conteneur. | Windows : `C:\healthcareMount` <br> Linux/macOS : `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Cette commande :

- Suppose que le répertoire d’entrée existe sur l’ordinateur hôte
- Exécute un conteneur Analyse de texte pour la santé à partir de l’image conteneur
- Alloue 6 cœurs de processeur et 12 gigaoctets (Go) de mémoire
- Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
- Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte.
