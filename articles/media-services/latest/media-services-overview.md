---
title: Vue d’ensemble d’Azure Media Services v3 | Microsoft Docs
description: Cet article fournit une vue d’ensemble générale de Media Services, ainsi que des liens vers des articles contenant plus d’informations.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, flux, diffusion, en direct, hors connexion
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 05/07/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 4fb42014e2c5389cf2a849ddf5114ab1f59c9e28
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65414027"
---
# <a name="azure-media-services-v3-overview"></a>Vue d’ensemble d’Azure Media Services v3

Azure Media Services est une plateforme sur le cloud vous permettant de créer des solutions qui assurent une diffusion de vidéo en continu de qualité, améliorent l’accessibilité et la distribution, analysent le contenu et bien plus encore. Que vous soyez un développeur d’applications, un centre d’appels, une agence gouvernementale, une société de divertissement, Media Services vous aide à créer des applications proposant des expériences multimédias de qualité exceptionnelle à un large public sur les navigateurs et les appareils les plus populaires actuellement. 

> [!NOTE]
> Actuellement, vous ne pouvez pas utiliser le portail Azure pour gérer des ressources v3. Utilisez l’[API REST](https://aka.ms/ams-v3-rest-ref), l’[interface CLI](https://aka.ms/ams-v3-cli-ref) ou l’un des [SDK](developers-guide.md) pris en charge.

## <a name="what-can-i-do-with-media-services"></a>Que puis-je faire avec Media Services ?

Media Services vous permet de créer une variété de workflows multimédias dans le cloud. Vous trouverez ci-dessous quelques exemples de ce qui peut être accompli avec Media Services.  

* Fournir des vidéos dans différents formats afin qu’elles puissent être lues sur un large éventail de navigateurs et d’appareils. Pour le streaming en direct et à la demande sur différents clients (appareils mobiles, télévisions, PC, etc.), le contenu vidéo et audio doit être codé et empaqueté de façon appropriée. Pour savoir comment livrer et diffuser ce type de contenu, consultez [Démarrage rapide : encoder des fichiers et les diffuser en continu](stream-files-dotnet-quickstart.md).
* Diffuser des événements sportifs en direct à un grand public en ligne, comme le football, le base-ball, le sport universitaire et bien plus encore. 
* Diffuser des réunions et des événements publics, comme des réunions de conseil municipal et de corps législatifs.
* Analyser les vidéos ou le contenu audio enregistrés. Par exemple, pour atteindre une satisfaction client optimale, les organisations peuvent extraire une reconnaissance vocale, et générer des index de recherche et des tableaux de bord. Elles peuvent ensuite extraire des informations sur les réclamations, les causes des réclamations et autres données pertinentes.
* Créer un abonnement au service vidéo et diffuser du contenu DRM protégé lorsqu’un client (par exemple, un studio de cinéma) doit limiter l’accès et l’utilisation de l’œuvre protégée par copyright propriétaire.
* Proposer du contenu hors connexion dans des avions, des trains et des voitures. Un client peut avoir besoin de télécharger du contenu sur son téléphone ou sa tablette pour pouvoir le lire lorsqu’il sera déconnecté du réseau.
* Implémenter une plateforme vidéo de formation en ligne éducative avec Azure Media Services et les [API Azure Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) pour le sous-titrage de la reconnaissance vocale, la traduction en plusieurs langues, etc. 
* Utilisez Azure Media Services avec les [API Azure Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) pour ajouter des sous-titres et des légendes aux vidéos afin de répondre aux besoins d’une audience plus large (par exemple, les personnes ayant des problèmes d’audition ou souhaitant lire simultanément dans une autre langue).
* Activez Azure CDN pour effectuer un scaling à grande échelle afin d’améliorer la gestion instantanée des charges importantes (par exemple le début d’un événement de lancement d’un produit). 

## <a name="how-can-i-get-started-with-v3"></a>Comment bien démarrer avec v3 ? 

Découvrez comment encoder et empaqueter du contenu, diffuser des vidéos en streaming à la demande, diffuser en direct et analyser vos vidéos avec Media Services v3. Des didacticiels, références d’API et autres documents vous montrent comment diffuser en toute sécurité du contenu vidéo ou audio à la demande et en direct pour des millions d’utilisateurs.

> [!TIP]
> Avant de commencer à développer, passez en revue ce qui suit :<br/>* [Concepts fondamentaux](concepts-overview.md) (concepts importants : empaquetage, encodage, protection, etc.)<br/>* [Développement avec les API Media Services v3](media-services-apis-overview.md) (informations sur l’accès aux API, les conventions de nommage, etc.)

### <a name="quickstarts"></a>Démarrages rapides  

Les guides de démarrage rapide montrent les instructions fondamentales du 1er jour pour les nouveaux clients qui testent rapidement Media Services.

* [Diffuser des fichiers vidéo en streaming - .NET](stream-files-dotnet-quickstart.md)
* [Diffuser des fichiers vidéo en streaming - CLI](stream-files-cli-quickstart.md)
* [Diffuser des fichiers vidéo en streaming - Node.js](stream-files-nodejs-quickstart.md)
    
### <a name="tutorials"></a>Tutoriels 

Les tutoriels montrent les procédures de scénarios pour certaines des tâches Media Services principales.

* [Encoder le fichier distant et diffuser la vidéo – REST](stream-files-tutorial-with-rest.md)
* [Encoder le fichier chargé et diffuser la vidéo – REST](stream-files-tutorial-with-api.md)
* [Diffuser en direct - .NET](stream-live-tutorial-with-api.md)
* [Analyser votre vidéo - .NET](analyze-videos-tutorial-with-api.md)
* [Chiffrement dynamique AES-128 - .NET](protect-with-aes128.md)
    
### <a name="how-to-guides"></a>Guides pratiques

Les articles contiennent des exemples de code qui montrent comment effectuer une tâche. Dans cette section, vous trouverez de nombreux exemples, dont ceux-ci :

* [Créer un compte - CLI](create-account-cli-how-to.md)
* [Accéder aux API - CLI](access-api-cli-how-to.md)
* [Commencer à développer avec des kits SDK](developers-guide.md)
* [Encoder avec HTTPS en tant qu’entrée de travail - .NET](job-input-from-http-how-to.md)  
* [Superviser les événements - Portail](monitor-events-portal-how-to.md)
* [Chiffrer dynamiquement avec multi-DRM - .NET](protect-with-drm.md) 
* [Comment encoder avec une transformation personnalisée - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur les concepts fondamentaux](concepts-overview.md)

