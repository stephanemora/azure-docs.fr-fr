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
ms.openlocfilehash: 4c33952ed56399b94a7db1088cb60919a4f88137
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108922"
---
Complétez et envoyez le [formulaire de demande de conteneurs Cognitive Services](https://aka.ms/cognitivegate) pour demander l’accès au conteneur.

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

Utilisez la commande docker login avec les informations d’identification fournies dans votre e-mail d’accueil afin de vous connecter à notre registre de conteneurs privé pour les conteneurs Cognitive Services.

```bash
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger cette image conteneur à partir de notre registre de conteneurs privé.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
