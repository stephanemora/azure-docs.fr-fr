---
title: Équipes et sous-équipes dans l’API Content Moderator | Microsoft Docs
description: Découvrez comment utiliser les équipes et les sous-équipes dans l’API Content Moderator pour Cognitive Services.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.openlocfilehash: 161c7cd8bac07d5ffc138297d98a40317a8d88fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367948"
---
# <a name="team-and-subteams"></a>Équipe et sous-équipes #

Vous devez créer une équipe avant d’utiliser Content Moderator. Lorsque que vous vous connectez et que vous attribuez un nom à une équipe, cette dernière devient votre équipe par défaut. Vous ne pouvez disposer que d’une équipe dans l’outil de révision. Toutefois, vous pouvez créer plusieurs sous-équipes. Les sous-équipes permettent de créer des équipes d’escalade ou des équipes dédiées à la révision de catégories de contenu spécifiques. Par exemple, vous pouvez choisir d’envoyer le contenu réservé aux adultes à une équipe différente qui procèdera à une révision plus approfondie.

Cette rubrique détaille les étapes permettant de créer des sous-équipes et de leur affecter des révisions rapidement. Toutefois, vous pouvez utiliser les [flux de travail](workflows.md) pour affecter les révisions en fonction de critères spécifiques.

## <a name="go-to-the-teams-setting"></a>Accéder aux paramètres des équipes ##

Pour démarrer la création d’une sous-équipe, cliquez sur Paramètres, puis sur l’option **Équipes**.

![Team Settings](images/0-teams-1.png)

## <a name="create-subteams"></a>Créer des sous-équipes ##

L’équipe par défaut contient tous les réviseurs potentiels. En effet, les sous-équipes sont des groupes appartenant à l’équipe par défaut. Vous ne pouvez pas intégrer un utilisateur à une sous-équipe s’il ne fait pas partie de l’équipe par défaut. Par conséquent, vous devez commencer par ajouter tous les réviseurs à l’équipe par défaut. Sur la page de l’équipe, cliquez sur le bouton Inviter.

![Inviter des utilisateurs](images/invite-users.png)

### <a name="1-create-a-subteam"></a>1. Créez une sous-équipe.
Faites défiler la page de l’équipe jusqu’à la section Sous-équipe. Cliquez sur le bouton Ajouter une sous-équipe. 

![Ajouter une sous-équipe](images/1-teams-1.png)

### <a name="2-name-your-subteam"></a>2. Attribuez un nom à votre sous-équipe.
Saisissez le nom de votre sous-équipe dans la boîte de dialogue, puis cliquez sur Enregistrer.

![Nom de la sous-équipe](images/1-Teams-2.PNG)

### <a name="3-assign-members-from-your-default-team"></a>3. Affectez des membres à partir de votre équipe par défaut.
Cliquez sur le bouton Ajouter un membre pour assigner des membres issus de votre équipe par défaut à une ou plusieurs sous-équipes. Vous pouvez uniquement ajouter des utilisateurs existants à une sous-équipe. Pour ajouter des utilisateurs qui ne font pas encore partie de l’outil de révision, vous devez les inviter à l’aide du bouton Inviter sur la page Paramètres de l’équipe.

![Assigner des membres à la sous-équipe](images/1-Teams-3.PNG)

## <a name="assign-reviews-to-your-subteams"></a>Assigner des révisions à vos sous-équipes ##

Une fois que vous avez créé vos sous-équipes et que vous y avez assigné des membres, vous pouvez commencer à leur envoyer des révisions d’images et de texte. Pour ce faire, accédez à la fenêtre Révision.
Si vous souhaitez assigner une image individuelle à une sous-équipe, cliquez sur les points de suspension situés dans l’angle supérieur droit d’une image, sélectionnez Déplacer vers et choisissez une sous-équipe.

![Assigner la révision d’une image à une sous-équipe](images/3-review-image-subteam-1.png)

## <a name="switch-between-subteams-to-review-assigned-content"></a>Basculer entre les sous-équipes pour réviser le contenu assigné ##

Si vous êtes membre de plusieurs sous-équipes, vous pouvez basculer de l’une à l’autre à partir du tableau de bord des outils de révision. Pour afficher tous les révisions en attente pour une sous-équipe, sélectionnez Choisir une sous-équipe dans l’onglet Image.

![Basculer entre les sous-équipes](images/3-review-image-subteam-2.png)
