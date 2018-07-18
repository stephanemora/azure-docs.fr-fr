---
title: Configuration des paramètres de l’outil de révision Content Moderator | Microsoft Docs
description: Découvrez comment configurer ou gérer votre équipe, vos balises, vos connecteurs, vos flux de travail et vos informations d’identification.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: a3432a1d8f424fbe78570f47b774c6e7942e16b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367925"
---
# <a name="about-review-tool-settings"></a>À propos des paramètres de l’outil de révision #

Vous pouvez facilement définir et modifier de nombreux composants grâce à l’onglet Paramètres du tableau de bord de l’outil de révision.

![Paramètres de la révision Content Moderator](images/settings-1.png)

## <a name="team-and-subteams"></a>Équipe et sous-équipes ## 

Gérez votre équipe et vos sous-équipes depuis cet onglet. Vous ne pouvez disposer que d’une équipe, mais vous pouvez [créer plusieurs sous-équipes](subteams.md) et envoyer des invitations aux futurs membres. Une fois que vous avez envoyé vos invitations, vous pouvez les surveiller, modifier les autorisations des membres de l’équipe et inviter davantage d’utilisateurs. Une fois que les membres de l’équipe ont accepté votre invitation, vous pouvez affecter ces membres à différentes sous-équipes. Vous pouvez attribuer deux rôles différents aux membres de l’équipe : administrateur ou réviseur. L’administrateur peut inviter d’autres utilisateurs, contrairement aux réviseurs.

![Paramètres de l’équipe Content Moderator](images/settings-2-team.png)

## <a name="tags"></a>Balises ##

Cette section vous permet de [définir des balises personnalisées](tags.md) en saisissant un extrait de code, un nom et une description de vos balises. Une fois créée, la balise est disponible lors des révisions. Vous pouvez utiliser différentes balises en fonction des révisions, en activant ou désactivant leur visibilité.

![Paramètres des balises Content Moderator](images/settings-3-tags.png)

## <a name="connectors"></a>Connecteurs ##

Les flux de travail offrent des fonctionnalités supplémentaires grâce à des connecteurs permettant de communiquer avec l’outil de révision. L’outil de révision appelle les API Content Moderator avec le flux de travail par défaut pour modérer le contenu. Lors de votre inscription à l’outil de révision, les informations d’identification de l’API Content Moderator sont configurées au préalable. L’outil prend également en charge l’intégration avec d’autres API de connecteurs, dans la mesure où un connecteur est disponible. Plusieurs connecteurs sont disponibles par défaut.

Vous pouvez gérer les connecteurs dans l’onglet [Connecteurs](connectors.md). Vous pouvez ajouter ou supprimer des connecteurs, et récupérer la clé d’abonnement pour un connecteur spécifique. Cliquez sur Connecter pour les ajouter à vos flux de travail personnalisés. 

![Paramètres des connecteurs Content Moderator](images/settings-4-connectors.png)

## <a name="workflows"></a>Flux de travail ##

Vous pouvez gérer les flux de travail depuis l’onglet Flux de travail. Vous pouvez tester les flux de travail en chargeant un fichier d’exemple. De plus, vous pouvez [définir des flux de travail personnalisés](workflows.md) appliqués à des images et du texte à l’aide des connecteurs d’API disponibles (disponibles dans l’onglet Connecteurs). 

![Paramètres du flux de travail Content Moderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Informations d'identification ##

Cet onglet fournit un accès rapide à votre clé d’abonnement Content Moderator. Vous en aurez besoin pour utiliser les API incluses avec Content Moderator (API de modération d’images, de modération de texte, de gestion de listes, de flux de travail et de révision).
 
![Informations d’identification Content Moderator](images/settings-6-credentials.png)
