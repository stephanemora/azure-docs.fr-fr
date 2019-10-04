---
title: Découvrir les concepts de l’outil de révision - Content Moderator
titleSuffix: Azure Cognitive Services
description: Découvrez l’outil de révision de Content Moderator, un site Web qui coordonne les efforts de modération d’une IA et la vérification humaine.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: 7f20b9c824045ac2f8c13df3ed8f776195de611a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564508"
---
# <a name="content-moderator-review-tool"></a>Outil de révision de Content Moderator

Azure Content Moderator fournit des services pour combiner la modération de contenu avec apprentissage automatique et les révisions d’humaines, et le site Web de [l’Outil de révision](https://contentmoderator.cognitive.microsoft.com) est un serveur frontal convivial qui donne un accès détaillé à ces services.

![Le tableau de bord de l’outil de révision dans un navigateur](./images/0-dashboard.png)

## <a name="what-it-does"></a>Résultat

Quand il est utilisé conjointement avec les API de modération assistée par ordinateur, [l’outil de révision](https://contentmoderator.cognitive.microsoft.com) vous permet d’accomplir les tâches suivantes en relation avec le cycle de vie de modération du contenu :

- Utilisez un ensemble d’outils pour modérer le contenu dans plusieurs formats (texte, image et vidéo).
- Automatisez la création de [révisions](../review-api.md#reviews) humaines lorsque les résultats de l’API de modération deviennent disponibles.
- Affectez ou remontez les révisions de contenu à plusieurs équipes de révision organisées par catégorie de contenu ou niveau d’expérience.
- Utilisez des filtres de logique ([flux de travail](../review-api.md#workflows)) personnalisés ou ceux par défaut pour trier et suivre le contenu sans écrire de code.
- Utilisez des [connecteurs](./configure.md#connectors) pour traiter le contenu avec Microsoft PhotoDNA, Texte Analytics et l’API Face en plus de l’API Content Moderator.
- Créez votre propre connecteur pour créer des flux de travail pour toute API ou tout processus métier.
- Récupérez les principales métriques de performances de vos processus de modération du contenu.

## <a name="review-tool-dashboard"></a>Tableau de bord de l’outil de révision

Dans l’onglet **Tableau de bord**, vous pouvez voir les métriques essentielles pour les révisions de contenu effectuées au sein de l’outil. Découvrez le nombre de révisions terminées, en attente et leur total pour le contenu vidéo, image et texte. Vous pouvez également voir la répartition des utilisateurs et des équipes qui ont effectué des révisions, ainsi que les balises de modération qui ont été appliquées.

![Affichage du tableau de bord](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Informations d'identification de l’outil de révision

Lorsque vous vous inscrivez pour [l’outil de révision](https://contentmoderator.cognitive.microsoft.com), vous êtes invité à sélectionner une région Azure pour votre compte. En effet, [l’outil de révision](https://contentmoderator.cognitive.microsoft.com) génère une clé de version d’évaluation gratuite pour les services Azure Content Moderator ; vous aurez besoin de cette clé pour accéder à tous les services par appel REST ou SDK. Vous pouvez voir votre URL de point de terminaison d’API et votre clé en sélectionnant **Paramètres** > **Informations d’identification**.

![Informations d’identification Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez [Configurer l’outil de révision](./configure.md) pour savoir comment accéder aux ressources de l’outil de révision et modifier ses paramètres.