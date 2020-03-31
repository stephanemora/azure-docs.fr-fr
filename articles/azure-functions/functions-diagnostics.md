---
title: Vue d’ensemble des diagnostics Azure Functions
description: Découvrez comment résoudre les problèmes de votre application de fonction avec les diagnostics Azure Functions.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834046"
---
# <a name="azure-functions-diagnostics-overview"></a>Vue d’ensemble des diagnostics Azure Functions

Lorsque vous exécutez une application de fonction, vous voulez vous parer aux problèmes qui peuvent survenir, des erreurs 4xx aux échecs de déclenchement. Avec les diagnostics Azure Functions, vous pouvez résoudre les problèmes de votre application de fonction de manière intelligente et interactive sans frais supplémentaires. Si vous rencontrez des problèmes avec votre application de fonction, les diagnostics Azure Functions vous en indiquent la nature afin que vous disposiez des informations appropriées pour les résoudre plus facilement et plus rapidement. Cet article explique les principes fondamentaux de l’utilisation des diagnostics Azure Functions pour diagnostiquer et résoudre plus rapidement les problèmes courants liés aux applications de fonction.

## <a name="start-azure-functions-diagnostics"></a>Démarrer les diagnostics d’Azure Functions

Pour accéder aux diagnostics d’Azure Functions :

1. Accédez à votre application de fonction sur le [Portail Azure](https://portal.azure.com).
2. Sélectionnez l’onglet **Fonctionnalités de la plateforme**.
3. Sélectionnez **Diagnostiquer et résoudre les problèmes** sous **Gestion des ressources**, ce qui ouvre les diagnostics Azure Functions.
4. Choisissez la catégorie qui décrit le mieux le problème de votre application de fonction en utilisant les mots clés de la vignette de la page d’accueil. Vous pouvez également saisir le mot clé qui décrit le mieux votre problème dans la barre de recherche. Par exemple, vous pouvez saisir `execution` pour afficher la liste des rapports de diagnostic liés à l’exécution de votre application de fonction et les ouvrir directement à partir de la page d’accueil.

![Page d’accueil](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>Utiliser l’interface interactive

Lorsque vous sélectionnez la catégorie de la page d’accueil correspondant le mieux au problème rencontré avec votre application de fonction, l’interface interactive des diagnostics Azure Functions, Génie, peut vous aider à diagnostiquer et résoudre le problème. Vous pouvez utiliser les raccourcis de vignette fournis par Génie pour afficher le rapport de diagnostic complet de la catégorie de problème qui vous intéresse. Les raccourcis de vignette vous offrent un moyen d’accès direct aux métriques de diagnostic.

![Génie](./media/functions-diagnostics/genie.png)

Après avoir sélectionné une vignette, vous pouvez voir une liste des rubriques relatives au problème décrit dans la vignette. Ces rubriques fournissent des extraits du rapport complet, offrant des informations pertinentes. Vous pouvez sélectionner les rubriques de votre choix pour examiner les problèmes de façon plus approfondie. En outre, vous pouvez sélectionner **Afficher le rapport complet** pour explorer toutes les rubriques sur une seule page.

![Aperçu du rapport de diagnostic](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Afficher un rapport de diagnostic

Une fois que vous avez choisi une rubrique, vous pouvez afficher un rapport de diagnostic spécifique à votre application de fonction. Les rapports de diagnostic utilisent des icônes d’état pour indiquer la présence de problèmes spécifiques dans votre application. Vous y trouverez une description détaillée du problème, des actions recommandées, des mesures associées et des documents utiles. Les rapports de diagnostic personnalisés sont générés à partir d’une série de vérifications exécutées sur votre application de fonction. Les rapports de diagnostic peuvent être un outil utile pour identifier les problèmes dans votre application de fonction et vous guider vers la résolution du problème.

## <a name="find-the-problem-code"></a>Trouver le code problématique

Pour les fonctions basées sur des scripts, vous pouvez utiliser **Exécution de la fonction et Erreurs** sous **Function App cesse de fonctionner ou signale des erreurs** pour circonscrire la ligne de code provoquant des exceptions ou des erreurs. Cette fonctionnalité peut être utile pour déterminer la cause racine et résoudre les problèmes à partir d’une ligne de code spécifique. Cette option n’est pas disponible pour les fonctions précompilées C# et Java.

![Rapport de diagnostic sur les erreurs d’exécution de fonction](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Exception de fonction](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez poser des questions ou envoyer vos retours sur les diagnostics Azure Functions sur [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Veuillez inclure `[Diag]` dans le titre de vos commentaires.

> [!div class="nextstepaction"]
> [Surveiller vos applications de fonction](functions-monitoring.md)
