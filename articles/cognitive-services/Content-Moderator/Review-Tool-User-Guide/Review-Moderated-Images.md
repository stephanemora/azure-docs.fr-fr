---
title: Utiliser les révisions de contenu via l’outil de vérification - Content Moderator
titlesuffix: Azure Cognitive Services
description: Découvrez comment l'outil de révision permet aux modérateurs humains d'examiner des images sur un portail web.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: a482ecf4a0d321525ab7e392695d2c4c0eebeadc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628997"
---
# <a name="create-human-reviews"></a>Créer des révisions humaines

Dans ce guide, vous allez apprendre à configurer [passe en revue](../review-api.md#reviews) sur le site Web outil de révision. Révisions stocker et affichent le contenu pour les modérateurs humains à évaluer. Les modérateurs peuvent modifier les balises appliquées et s’appliquent leurs propres balises personnalisées comme il convient. Lorsqu’un utilisateur termine une revue, les résultats sont envoyés à un point de terminaison de rappel spécifiée et le contenu est supprimé à partir du site.

## <a name="prerequisites"></a>Conditions préalables

- Connectez-vous ou créez un compte sur le modérateur de contenu [outil de révision](https://contentmoderator.cognitive.microsoft.com/) site.

## <a name="image-reviews"></a>Révisions d’images

1. Accédez à la [outil de révision](https://contentmoderator.cognitive.microsoft.com/), sélectionnez le **essayez** onglet et charger des images pour passer en revue.
1. Une fois les images chargées ont terminé le traitement, accédez à la **révision** onglet et sélectionnez **Image**.

    ![Navigateur Chrome montrant l’outil de révision avec l’option Réviser - Image en surbrillance](images/review-images-1.png)

    Les images s’affichent avec des étiquettes qui ont été affectées par le processus de modération automatique. Les images que vous avez envoyé via l’outil de vérification ne sont pas visibles aux autres réviseurs.

1. Éventuellement, déplacez le **avis à afficher** curseur (1) pour ajuster le nombre d’images qui sont affichés sur l’écran. Cliquez sur le **avec balises** ou **non marqué** boutons (2) pour trier les images en conséquence. Cliquez sur un panneau des balises (3) pour l’activer ou désactiver.

    ![Navigateur Chrome montrant l’outil de révision avec des images marquées pour révision](images/review-images-2.png)

1. Pour afficher plus d’informations sur une image, cliquez sur le bouton de sélection dans la miniature, puis sélectionnez **afficher les détails**. Vous pouvez affecter une image à un sous-groupe avec le **déplacer vers** option (voir la [équipes](./configure.md#manage-team-and-subteams) section pour en savoir plus sur les sous-équipes).

    ![Une image avec l’option Afficher les détails en surbrillance](images/review-images-3.png)

1. Parcourez les informations de modération de l’image sur la page de détails.

    ![Une image avec les détails de modération listés dans un volet distinct](images/review-images-4.png)

1. Une fois que vous avez révisé et mis à jour les balises attribuées en fonction de la situation, cliquez sur **Suivant** pour envoyer vos révisions. Une fois que vous avez envoyé vos commentaires, vous disposez d’un délai de cinq secondes pour cliquer sur le bouton **Prec** pour revenir à l’écran précédent et examiner à nouveau les images. Passé ce délai, les images ne sont plus dans la file d’attente d’envoi et le bouton **Prec** n’est plus disponible.

## <a name="text-reviews"></a>Révisions de texte

Texte passe en revue la fonction de la même façon aux révisions de l’image. Au lieu de charger le contenu, vous simplement écrirez ou de collez le texte (jusqu'à 1 024 caractères). Ensuite, Content Moderator analyse le texte et applique des balises (en plus des autres informations de modération, telles que les obscénités et données personnelles). Dans les révisions de texte, vous pouvez activer/désactiver les balises appliquées ou appliquer des balises personnalisées avant de soumettre la révision.

![Capture d’écran de l’outil de vérification montrant du texte marqué d’indicateurs dans une fenêtre du navigateur Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris à configurer et utiliser les révisions du modérateur de contenu [outil de vérification](https://contentmoderator.cognitive.microsoft.com). Ensuite, consultez le [guide de l’API REST](../try-review-api-review.md) ou [guide du kit SDK .NET](../moderation-reviews-quickstart-dotnet.md) pour apprendre à créer par programmation des révisions.