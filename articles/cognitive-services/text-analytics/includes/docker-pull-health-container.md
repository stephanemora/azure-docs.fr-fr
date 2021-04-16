---
title: Commande docker pull pour le conteneur de santé
titleSuffix: Azure Cognitive Services
description: Commande docker pull pour le conteneur d’Analyse de texte pour la santé
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/25/2021
ms.author: aahi
ms.openlocfilehash: 8595af7f46b63f9991d6a02279ccad76afb38311
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089704"
---
Renseignez et envoyez le [formulaire de demande Cognitive Services](https://aka.ms/csgate) pour demander accès à la préversion publique d’Analyse de texte pour la santé.  Cette application s’utilise à la fois avec le conteneur et l’API web hébergée en préversion publique.
Le formulaire demande des informations sur vous, votre entreprise et le scénario d’utilisateur pour lequel vous allez utiliser le conteneur. Une fois que vous avez envoyé le formulaire, l’équipe Azure Cognitive Services vérifie que vous remplissez bien les critères d’accès au registre de conteneurs privé.

> [!IMPORTANT]
> * Dans le formulaire, vous devez utiliser une adresse e-mail associée à un ID d’abonnement Azure.
> * La ressource Analyse de texte (point de terminaison de facturation et apikey) que vous utilisez pour exécuter le conteneur doit avoir été créée avec l’ID d’abonnement Azure approuvé. 
> * Vérifiez votre adresse e-mail (boîtes de réception et dossiers de courrier indésirable) pour obtenir des mises à jour sur l’état de votre application auprès de Microsoft.
> * Cette URL de conteneur a changé ; consultez les exemples ci-dessous. Le conteneur ne sera pas disponible en téléchargement à partir du `containerpreview.azurecr.io` à compter du 26 avril 2021.


Une fois approuvé, utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger cette image conteneur à partir du registre de conteneurs publics Microsoft.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```
