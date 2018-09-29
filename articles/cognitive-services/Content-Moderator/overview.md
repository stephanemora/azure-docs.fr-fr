---
title: Qu’est-ce que Content Moderator ?
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser Content Moderator pour suivre, marquer, évaluer et filtrer le contenu inapproprié dans le contenu généré par l’utilisateur.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: overview
ms.date: 06/15/2017
ms.author: sajagtap
ms.openlocfilehash: e109376f47d921fb18d7bb9a6252e80315419ec0
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226039"
---
# <a name="what-is-content-moderator"></a>Qu’est-ce que Content Moderator ?

La modération du contenu est le processus de surveillance d’un contenu potentiellement offensant, indésirable ou présentant des risques. Le contenu modéré peut être des images, du texte et des vidéos.

## <a name="where-it-is-used"></a>Utilisation

La liste suivante présente quelques exemples d’utilisation de Content Moderator :

- Modération des catalogues de produits et du contenu généré par l’utilisateur sur les marketplaces
- Modération des artefacts créés par les utilisateurs et des messageries instantanées par les sociétés de jeu
- Modération des images, du texte et des vidéos ajoutés par les utilisateurs sur les plateformes de messagerie sociale
- Mise en place par les sociétés de contenu d’entreprise d’un système de modération centralisée du contenu
- Filtrage du contenu inadéquat et offensant pour les étudiants et les enseignants par les fournisseurs de solutions éducatives K-12

## <a name="what-it-includes"></a>Contenu

Content Moderator se compose de plusieurs API de services web et d’un outil intégré de révision par un opérateur humain qui aide à modérer les images, le texte et les vidéos.

![Diagramme de blocs Content Moderator](images/content-moderator-block-diagram.png)

## <a name="apis"></a>API

Content Moderator inclut les API suivantes :
  - [**API Modération du texte**](text-moderation-api.md) : cette API permet d’analyser le texte à la recherche d’injures, de contenu explicite, suggestif, offensant, et d’informations d’identification personnelle (PII).
  - [**API Liste de termes personnalisée**](try-terms-list-api.md) : utilisez cette API pour effectuer une mise en correspondance avec des listes de termes personnalisées, en plus des termes intégrés. Utilisez ces listes pour bloquer ou autoriser du contenu conformément aux stratégies de votre contenu.  
  - [**API Modération de l’image**](image-moderation-api.md) : cette API permet d’analyser des images à la recherche de contenu pour adultes et osé, de détecter le texte dans les images grâce à la reconnaissance optique des caractères (OCR) et de détecter les visages.
  - [**API Liste d’images personnalisée**](try-image-list-api.md) : utilisez cette API pour effectuer une mise en correspondance avec des listes d’images personnalisées, du contenu préidentifié que vous n’avez pas besoin de classer à nouveau.
  - [**API de modération des vidéos**](video-moderation-api.md) : cette API permet d’analyser les vidéos à la recherche de contenu pour adultes et osé.
  - [**API de révision**](try-review-api-job.md): utilisez les opérations [Travaux](try-review-api-job.md), [Révisions](try-review-api-review.md), et [Flux de travail](try-review-api-workflow.md) pour créer et automatiser les flux de travail impliquant une intervention humaine dans l’outil de révision.

## <a name="human-review-tool"></a>Outil de révision par un opérateur humain

Votre abonnement Content Moderator inclut l’accès à l’[outil intégré de révision par un opérateur humain](Review-Tool-User-Guide/human-in-the-loop.md). Utilisez l’API de révision mentionnée précédemment pour créer des analyses de texte, d’images et de vidéos pour permettre à vos modérateurs humains de prendre des décisions.

![Outil de révision des vidéos Content Moderator](images/video-review-default-view.png)

## <a name="next-steps"></a>Étapes suivantes

Utilisez le [Démarrage rapide](quick-start.md) pour découvrir Content Moderator.
