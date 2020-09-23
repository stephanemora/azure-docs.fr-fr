---
title: Commande docker pull pour le conteneur de santé
titleSuffix: Azure Cognitive Services
description: Commande docker pull pour le conteneur d’Analyse de texte pour la santé
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: b9452baf0301233a1cd6e05621a89d013d32c9e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906066"
---
Complétez et envoyez le [formulaire de demande de conteneurs Cognitive Services](https://aka.ms/cognitivegate) pour demander l’accès au conteneur.
Le formulaire demande des informations sur vous, votre entreprise et le scénario d’utilisateur pour lequel vous allez utiliser le conteneur. Une fois que vous avez envoyé le formulaire, l’équipe Azure Cognitive Services vérifie que vous remplissez bien les critères d’accès au registre de conteneurs privé.

> [!IMPORTANT]
> * Dans le formulaire, vous devez utiliser une adresse e-mail associée à un ID d’abonnement Azure.
> * La ressource Azure que vous utilisez pour exécuter le conteneur doit avoir été créée avec l’ID d’abonnement Azure approuvé. 
> * Vérifiez votre adresse e-mail (boîtes de réception et dossiers de courrier indésirable) pour obtenir des mises à jour sur l’état de votre application auprès de Microsoft.

Utilisez la commande docker login avec les informations d’identification fournies dans votre e-mail d’accueil afin de vous connecter à notre registre de conteneurs privé pour les conteneurs Cognitive Services.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger cette image conteneur à partir de notre registre de conteneurs privé.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
