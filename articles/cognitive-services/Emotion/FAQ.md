---
title: Forum aux questions - API Émotion
titlesuffix: Azure Cognitive Services
description: Obtenez des réponses aux questions les plus fréquemment posées sur l’API Émotion.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3654d315867cfe60c277806d4246469adf0a791f
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238264"
---
# <a name="emotion-api-frequently-asked-questions"></a>Questions fréquentes (FAQ) sur l’API Émotion

> [!IMPORTANT]
> L’API Émotion sera déconseillée à partir du 15 février 2019. La fonction de reconnaissance des émotions est maintenant accessible de façon générale dans le cadre de l’[API Visage](https://docs.microsoft.com/azure/cognitive-services/face/).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Si vous ne trouvez pas les réponses à vos questions dans ce Forum aux questions, essayez d’interroger la Communauté de l’API Émotion sur [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou de contacter le support technique sur [UserVoice](https://cognitive.uservoice.com/).  

-----

**Question** : *quels types d'images obtiennent les meilleurs résultats avec l'API Émotion ?*

**Réponse** : pour de meilleurs résultats, utilisez des images avec des visages de face, dégagés et entiers. La fiabilité diminue avec des visages de face partiels et l’API Émotion peut ne pas reconnaître des émotions dans des images où le visage est tourné de plus de 45 degrés.

-----

**Question** : *combien d'émotions l'API Émotion peut-elle identifier ?*

**Réponse** : l'API Émotion reconnaît huit émotions différentes universellement acceptées :
* Joie
* Tristesse
* Surprise
* Colère
* Peur
* Mépris
* Dégoût
* Neutre

-----

**Question** : *existe-t-il un moyen de transmettre un flux vidéo en direct à l'API et d'obtenir le résultat simultanément ?*

**Réponse** : utilisez l'API Émotion basée sur les images et appelez-la sur chaque image ou passez des images pour améliorer les performances.  Des exemples d’analyse image par image des vidéos sont disponibles.

-----

**Question** : *je transmets les données d'images binaires mais je reçois le message : « Image de visage non valide.**

**Réponse** : ce message indique que l'algorithme a rencontré des problèmes lors de l'analyse de l'image.  
* Les formats d’image d’entrée pris en charge incluent JPEG, PNG, GIF (la première image) et BMP.
* La taille du fichier image ne doit pas dépasser 4 Mo.
* La plage des tailles de visages détectables s’étend de 36 x 36 à 4096 x 4096 pixels. Les visages en dehors de cette plage ne sont pas détectés.
* Certains visages peuvent ne pas être détectés en raison de problèmes techniques, par exemple, des prises à grand angle (position de la tête) et des occlusions importantes. Les prises frontales et quasi-frontales produisent les meilleurs résultats.

-----
