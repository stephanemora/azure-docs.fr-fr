---
title: Révision d’images dans Azure Content Moderator | Microsoft Docs
description: L’outil de révision permet aux modérateurs humains d’examiner les images.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/06/2017
ms.author: sajagtap
ms.openlocfilehash: 97879747bbfde2247e41847d8d68bf7f19530ecb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367864"
---
# <a name="review-moderated-images"></a>Révision des images modérées

Une fois que vous vous êtes inscrit à la modération de contenu et obtenu une clé d’abonnement, vous pouvez tester les fonctionnalités de révision des images.

1.  Ouvrez l’[outil de révision manuelle](https://contentmoderator.cognitive.microsoft.com/) et connectez-vous. 
2.  Cliquez sur l’onglet Essayer et télécharger des images à examiner.
3.  Cliquez sur l’onglet Révision et sélectionnez l’image.

  ![Options de révision des images](images/review-images-1.png)

  Les images sont affichées avec des étiquettes attribuées par l’outil de révision. Lorsque vous vérifiez les images, les autres réviseurs de votre équipe n’y ont pas accès.

4.  Déplacez le curseur « Révisions à afficher » (1) pour ajuster le nombre d’images affichées à l’écran. Cliquez sur les boutons permettant de trier les images qui sont accompagnées de balises et les autres (2). Cliquez sur une balise (3) pour l’activer ou la désactiver.

  ![Navigation au sein des images à réviser](images/review-images-2.png)
 
5.  Pour obtenir plus d’informations au sujet d’une image, cliquez sur les points de suspension d’une miniature, puis sur l’option **Afficher les détails**. Pour assigner l’image à une sous-équipe, sélectionnez l’option **Déplacer vers**.
 
  ![Options permettant d’afficher les détails et de déplacer l’image vers une sous-équipe](images/review-images-3.png)

6. Parcourez les informations de modération de l’image sur la page de détails.

  ![Affichage des détails de l’image](images/review-images-4.png)
 
7.  Une fois que vous avez révisé et mis à jour les balises attribuées en fonction de la situation, cliquez sur **Suivant** pour envoyer vos révisions.

Une fois que vous avez envoyé vos commentaires, vous disposez d’un délai de cinq secondes pour cliquer sur le bouton **Prec** pour revenir à l’écran précédent et examiner à nouveau les images. Passé ce délai, les images ne sont plus dans la file d’attente d’envoi et le bouton **Prec** n’est plus disponible.
