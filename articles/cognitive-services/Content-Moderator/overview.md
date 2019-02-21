---
title: Qu’est-ce qu’Azure Content Moderator ?
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser Content Moderator pour suivre, marquer, évaluer et filtrer le matériau inapproprié dans le contenu généré par l’utilisateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: 7a6424921b869428d1dbeffadd68e173a32d8821
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455244"
---
# <a name="what-is-azure-content-moderator"></a>Qu’est-ce qu’Azure Content Moderator ?

L’API Azure Content Moderator est un service cognitif qui vérifie le texte, les images et le contenu vidéo à la recherche d’éléments potentiellement dangereux, offensants ou indésirables. Lorsque des éléments de ce type sont détectés, le service applique les étiquettes appropriées (indicateurs) au contenu. L’application peut ensuite gérer le contenu marqué afin de se conformer aux réglementations ou pour maintenir l’environnement souhaité pour les utilisateurs. Consultez la section sur l’[API Content Moderator](#content-moderator-apis) pour en savoir plus sur les différents indicateurs de contenu.

## <a name="where-it-is-used"></a>Utilisation

Voici quelques scénarios dans lesquels un développeur de logiciels ou une équipe utilise Content Moderator :

- Modération des catalogues de produits et du contenu généré par l’utilisateur sur les marketplaces en ligne
- Modération des artefacts créés par les utilisateurs et des messageries instantanées par les sociétés de jeu
- Modération des images, du texte et des vidéos ajoutés par les utilisateurs sur les plateformes de messagerie sociale
- Mise en place par les sociétés de contenu d’entreprise d’un système de modération centralisée du contenu
- Filtrage du contenu inapproprié pour les étudiants et les enseignants par les fournisseurs de solutions éducatives (maternelle-terminale)

## <a name="what-it-includes"></a>Contenu

Le service Content Moderator se compose de plusieurs API de service web disponibles via les appels REST et un SDK .NET. Il inclut également l’outil de vérification humaine, qui permet aux réviseurs humains d’aider le service et d’améliorer ou d’ajuster sa fonction de modération.

![Diagramme de blocs pour Content Moderator montrant les API de modération, les API de vérification et l’outil de vérification humaine](images/content-moderator-block-diagram.png)

### <a name="content-moderator-apis"></a>API Content Moderator

Le service Content Moderator inclut des API pour les scénarios suivants.

| Action | Description |
| ------ | ----------- |
|[**Modération de texte**](text-moderation-api.md)| Analyse du texte à la recherche de contenu offensant, de contenu sexuellement explicite ou suggestif, de blasphèmes et d’informations d’identification personnelle (PII).|
|[**Listes de termes personnalisées**](try-terms-list-api.md)| Analyse du texte par rapport à une liste personnalisée de termes, en plus des termes prédéfinis. Utilisez les listes personnalisées pour bloquer ou autoriser du contenu en fonction de vos propres stratégies de contenu.|  
|[**Modération d’images**](image-moderation-api.md)| Analyse des images à la recherche de contenu pour adultes et osé, détection de texte dans les images grâce à la reconnaissance optique des caractères (OCR) et détection des visages.|
|[**Listes d’images personnalisées**](try-image-list-api.md)| Analyse des images par rapport à une liste d’images personnalisée. Utilisez les listes d’images personnalisées pour filtrer les instances de contenu récurrent que vous ne souhaitez pas classifier à nouveau.|
|[**Modération de vidéos**](video-moderation-api.md)| Analyse des vidéos à la recherche de contenu pour adultes ou osé, en renvoyant des marqueurs de temps pour ce contenu.|
|[**Révision**](try-review-api-job.md)| Utilisez les opérations [Travaux](try-review-api-job.md), [Révisions](try-review-api-review.md) et [Flux de travail](try-review-api-workflow.md) pour créer et automatiser les workflows impliquant une intervention humaine dans l’outil de vérification humaine. L’API de workflow n’est pas encore disponible par le biais du SDK .NET.|

### <a name="human-review-tool"></a>Outil de révision par un opérateur humain

Le service Content Moderator inclut également un [outil web de vérification humaine](Review-Tool-User-Guide/human-in-the-loop.md). 

![Page d'accueil de l’outil de vérification humaine de Content Moderator](images/homepage.PNG)

Vous pouvez utiliser les API de révision pour configurer des révisions d’équipe de contenu vidéo, de texte et d’images en fonction des filtres que vous spécifiez. Ensuite, les modérateurs humains peuvent prendre les décisions de modération finales. Le service n’apprend pas de l’entrée humaine, mais le travail combiné du service et des équipes de vérification humaine permet aux développeurs de trouver le bon équilibre entre efficacité et précision.

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données
Comme avec tous les services Cognitive Services, les développeurs utilisant le service Content Moderator doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) dans le Centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Commencez à utiliser le service Content Moderator en suivant les instructions fournies dans [Essayer Content Moderator sur le web](quick-start.md).
