---
title: FAQ sur l’API Émotion | Microsoft Docs
description: Obtenez des réponses aux questions fréquentes concernant l’API Émotion dans Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/26/2017
ms.author: anroth
ms.openlocfilehash: 8532d7c00fd8d7b01d84b5e55cb9bbc60241789c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367972"
---
# <a name="emotion-api-frequently-asked-questions"></a>Questions fréquentes (FAQ) sur l’API Émotion
 
> [!IMPORTANT]
> La préversion de l’API Vidéo a pris fin le 30 octobre 2017. Essayez la nouvelle [préversion de l’API Video Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) pour extraire facilement des insights des vidéos et améliorer les expériences de découverte de contenu, telles que les résultats de recherche, en détectant le texte parlé, les visages, les personnes et les émotions. [Plus d’informations](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Si vous ne trouvez pas les réponses à vos questions dans ce Forum aux questions, essayez d’interroger la Communauté de l’API Émotion sur [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou de contacter le support technique sur [UserVoice](https://cognitive.uservoice.com/).  

-----

**Question** : *quels types d’images obtiennent les meilleurs résultats avec l’API Émotion ?*

**Réponse** : utilisez des images avec des visages de face, dégagés et entiers, pour de meilleurs résultats. La fiabilité diminue avec des visages de face partiels et l’API Émotion peut ne pas reconnaître les émotions dans les images où le visage est tourné de 45 degrés ou plus.

-----

**Question** : *combien d’émotions l’API Émotion peut-elle identifier ?*

**Réponse** : l’API Émotion reconnaît huit émotions différentes universellement acceptées : 
* Joie
* Tristesse
* Surprise
* Colère
* Peur
* Mépris
* Dégoût 
* Neutre 

-----

**Question** : *existe-t-il un moyen de transmettre un flux vidéo en direct à l’API et d’obtenir le résultat simultanément ?*

**Réponse** : utilisez l’API Émotion basée sur les images et appelez-la sur chaque image ou passez des images pour améliorer les performances.  Des exemples d’analyse image par image des vidéos sont disponibles.

-----

**Question** : *je transmets les données d’images binaires mais cela me met : « image de visage non valide.**

**Réponse** : cela signifie que l’algorithme a rencontré des problèmes lors de l’analyse de l’image.  
* Les formats d’image d’entrée pris en charge incluent JPEG, PNG, GIF (la première image), BMP. 
* La taille du fichier image ne doit pas dépasser 4 Mo
* Les visages sont détectables selon une plage de tailles allant de 36 x 36 à 4 096 x 4 096 pixels. Les visages en dehors de cette plage ne sont pas détectés
* Certains visages peuvent ne pas être détectés en raison de défis techniques, par exemple, les prises très rapprochées (portrait) et les occlusions importantes. Les prises frontales et quasi-frontales produisent les meilleurs résultats

-----
