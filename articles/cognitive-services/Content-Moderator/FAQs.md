---
title: Questions fréquentes (FAQ) - Content Moderator
titlesuffix: Azure Cognitive Services
description: Obtenez des réponses aux questions fréquentes concernant Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: sajagtap
ms.openlocfilehash: df8d957fc2de620d63567a9cc1b14b24b73052bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61268902"
---
# <a name="frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ)

### <a name="what-does-my-content-moderator-subscription-include"></a>Quels sont les éléments inclus dans mon abonnement Content Moderator ?

Votre abonnement de Content Moderator inclut l’accès à l’outil de vérification et les API. Selon les besoins de votre entreprise, vous pouvez choisir d’utiliser ces deux accès ou l’un ou l’autre.

### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>Quelles sont les limites/restrictions relatives au contenu qui peut être modéré à l’aide de l’API ?

Lorsque vous utilisez l’API, les images doivent avoir une taille minimale de 128 pixels, et la taille du fichier doit être inférieure à 4 Mo. Le texte peut compter jusqu’à 1 024 caractères. Il n’existe aucune limite quant à la durée des vidéos.

### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>Que se passe-t-il si le contenu transmis à l’API de texte ou à l’API d’image dépasse la limite de taille autorisée ?

L’API de texte retourne un code d’erreur qui indique que la longueur du texte dépasse la limite autorisée. L’API d’image retourne également un code d’erreur qui indique que l’image ne respecte pas les exigences de taille.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>Conservez-vous les images, les textes ou les vidéos qui sont soumis à la modération ?

Votre contenu vous appartient et ne peut pas être conservé par Microsoft sans votre consentement. Microsoft utilise des mesures de sécurité de pointe pour vous aider à protéger votre contenu.

### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Puis-je utiliser Content Moderator pour détecter des images illégales d’exploitation d’enfants ?

Non. Toutefois, les organisations qualifiées peuvent utiliser le [service cloud PhotoDNA](https://www.microsoft.com/photodna "service cloud PhotoDNA de Microsoft") pour détecter ce type de contenu.

### <a name="how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>Combien passez en revue les équipes peuvent une jointure de l’utilisateur ? L’utilisateur peut-il changer d’équipe ?

Un utilisateur peut uniquement se joindre à une équipe à la fois.

### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>Quelles sont les rôles des membres sont pris en charge par l’outil de vérification ? En quoi sont-ils différents ?

Les outils de révision permet actuellement d’attribution de rôles d’administrateur et réviseur. Les administrateurs peuvent inviter d’autres utilisateurs et ont accès aux paramètres de configuration alors que les réviseurs peuvent uniquement vérifier les résultats de la modération et baliser un contenu ou en supprimer les balises.

### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>Qu’est-ce qu’une balise ? L’outil de révision prend-elle en charge les balises personnalisées ?

Une balise est un code court d’une ou deux-lettres qui dénote un indicateur de modération, tels que « a » pour adulte, « r » pour osé et ainsi de suite. Les administrateurs peuvent définir de nouvelles balises pour leur entreprise en fonction de leurs besoins.

### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>Combien de membres puis-je avoir dans mon équipe de révision ?

Vous pouvez avoir un maximum de cinq membres de l’équipe, y compris l’administrateur, dans une équipe.
