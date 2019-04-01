---
title: Découvrez les concepts de l’outil révision - Content Moderator
titlesuffix: Azure Cognitive Services
description: En savoir plus sur l’outil de modérateur de contenu consulter un site Web qui coordonne une AI combiné et efforts de modération de vérification humaine.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: b7ec997fd3e9bfe294050893d80fd57a96a47aae
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755884"
---
# <a name="content-moderator-review-tool"></a>Outil de vérification de modérateur de contenu

Azure Content Moderator fournit des services pour combiner la modération du contenu machine learning avec les révisions d’humaines et le [outil de révision](https://contentmoderator.cognitive.microsoft.com) site Web est un serveur frontal convivial qui donne accès détaillées à ces services.

![Le tableau de bord d’outil de révision dans un navigateur](./images/0-dashboard.png)

## <a name="what-it-does"></a>Résultat

Le [outil de révision](https://contentmoderator.cognitive.microsoft.com), lorsqu’il est utilisé conjointement avec l’API, de modération assistée par ordinateur vous permet d’accomplir les tâches suivantes dans le processus de modération du contenu :

- Utiliser un ensemble d’outils pour modérer le contenu dans plusieurs formats (texte, image et vidéo).
- Automatiser la création de humaines [passe en revue](../review-api.md#reviews) lorsque les résultats de modération de l’API arrivé dans.
- Affecter ou réaffecter des révisions de contenu à plusieurs équipes de révision, organisées par niveau d’expérience ou de la catégorie de contenu.
- Utiliser des filtres de logique personnalisée ou par défaut ([workflows](../review-api.md#workflows)) permet de trier et le suivi du contenu, sans écrire de code.
- Utilisez [connecteurs](./configure.md#connectors) pour traiter le contenu avec Microsoft PhotoDNA, Analytique de texte et API visage outre les API de modérateur de contenu.
- Créer votre propre connecteur pour créer des flux de travail pour n’importe quelle API ou des processus d’entreprise.
- Récupérez les principales métriques de performances de vos processus de modération du contenu.

## <a name="review-tool-dashboard"></a>Tableau de bord outil de révision

Sur le **tableau de bord** onglet, vous pouvez voir des mesures clés pour les évaluations de contenu effectuées au sein de l’outil. Voir le nombre total, complète et en attente de révisions pour le contenu vidéo, image et texte. Vous pouvez également voir la répartition des utilisateurs et des équipes qui se sont achevées révisions, ainsi que les balises de modération qui ont été appliqués.

![Affichage du tableau de bord](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Passez en revue les informations d’identification de l’outil

Lorsque vous vous inscrivez avec le [outil de révision](https://contentmoderator.cognitive.microsoft.com), vous êtes invité à sélectionner une région Azure pour vous de compte. Il s’agit, car le [outil de révision](https://contentmoderator.cognitive.microsoft.com) génère une clé de version d’évaluation gratuite pour les services d’Azure Content Moderator ; vous aurez besoin cette clé d’accès à toutes les services à partir d’un appel REST ou le logiciel (SDK). Vous pouvez afficher votre URL de point de terminaison d’API et de clé en sélectionnant **paramètres** > **informations d’identification**.

![Informations d’identification Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez [configurer l’outil de vérification](./configure.md) pour savoir comment accéder aux ressources d’outil de révision et de modifier les paramètres.