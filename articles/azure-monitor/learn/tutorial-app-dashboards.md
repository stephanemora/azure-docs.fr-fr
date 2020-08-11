---
title: Créer des tableaux de bord personnalisés dans Azure Application Insights | Microsoft Docs
description: Didacticiel concernant la création de tableaux de bord d’indicateur de performance clé à l’aide d’Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.custom: mvc
ms.openlocfilehash: 706ae5a6c93468da9a65293c1bb4eefb136b938d
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553272"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Créer des tableaux de bord d’indicateur de performance clé à l’aide d’Azure Application Insights

Vous pouvez créer dans le portail Azure plusieurs tableaux de bord incluant des vignettes présentant des données de plusieurs ressources Azure dans différents groupes de ressources et abonnements.  Vous pouvez épingler différents graphiques et affichages d’Azure Application Insights pour créer des tableaux de bord personnalisés qui donnent une image complète de l’intégrité et des performances de votre application. Ce didacticiel vous guide dans la création d’un tableau de bord personnalisé incluant plusieurs types de données et visualisations d’Azure Application Insights.

 Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un tableau de bord personnalisé dans Azure
> * Ajouter une vignette à partir de la Galerie de vignettes
> * Ajouter des métriques standard au tableau de bord dans Application Insights
> * Ajouter un graphique de métrique personnalisé Application Insights au tableau de bord
> * Ajouter les résultats d’une requête Logs (Analytics) au tableau de bord

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce tutoriel :

- Déployez une application .NET pour Azure et [activez le Kit SDK Application Insights](../app/asp-net.md).

> [!NOTE]
> Les autorisations requises pour l’utilisation des tableaux de bord sont décrites dans l’article [Présentation du contrôle d’accès relatif aux tableaux de bord](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboard-share-access#understanding-access-control-for-dashboards).

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Créer un tableau de bord
Un tableau de bord unique peut contenir des ressources de plusieurs applications, groupes de ressources et abonnements.  Démarrez le didacticiel en créant un tableau de bord pour votre application.  

1. Dans le volet du tableau de bord, sélectionnez **Nouveau tableau de bord**.

   ![Nouveau tableau de bord](media/tutorial-app-dashboards/1newdashboard.png)

1. Tapez un nom pour le tableau de bord.
1. Examinez la **Galerie de vignettes** pour voir l’éventail des vignettes que vous pouvez ajouter à votre tableau de bord.  Outre l’ajout de vignettes de la galerie, vous pouvez épingler directement des graphiques et d’autres affichages d’Application Insights au tableau de bord.
1. Recherchez la vignette **Markdown**, puis faites-la glisser sur votre tableau de bord.  Cette vignette permet d’ajouter du texte mis en forme dans markdown, ce qui est idéal pour l’ajout d’un texte descriptif à votre tableau de bord.
1. Ajoutez du texte au propriétés de la vignette, puis redimensionnez celle-ci sur le canevas du tableau de bord.
    
    ![Modifier une vignette markdown](media/tutorial-app-dashboards/2dashboard-text.png)

1. Cliquez sur **Personnalisation terminée** en haut de l’écran pour quitter le mode de personnalisation des vignettes.

## <a name="add-health-overview"></a>Ajouter une vue d'ensemble de l'intégrité
Un tableau de bord contenant uniquement du texte statique n’est pas très attrayant. Ajoutez donc une vignette d’Application Insights pour présenter les informations relatives à votre application.  Vous pouvez ajouter des vignettes d’Application Insights à partir de la Galerie de vignettes, ou les épingler directement à partir des écrans d’Application Insights.  Cela vous permet de configurer des graphiques et affichages qui vous sont déjà familiers avant de les épingler à votre tableau de bord.  Commencez par ajouter la vue d'ensemble standard de l'intégrité pour votre application.  Cela ne nécessite aucune configuration et permet une personnalisation minimale du tableau de bord.


1. Sélectionnez votre ressource **Application Insights** dans l’écran d’accueil.
2. Dans le volet **Vue d’ensemble**, cliquez sur ![l’icône de punaise](media/tutorial-app-dashboards/pushpin.png) pour ajouter la vignette au dernier tableau de bord que vous consultiez.  
 
3. En haut à droite, une notification s’affiche indiquant que votre vignette a été épinglée à votre tableau de bord. Cliquez sur **Épinglé au tableau de bord** dans la notification pour revenir à votre tableau de bord ou en utiliser le volet.
4. Cette vignette est désormais ajoutée à votre tableau de bord. Sélectionnez **Modifier** pour modifier le positionnement de la vignette. Cliquez sur la vignette et faites-la glisser à sa position, puis cliquez sur **Personnalisation terminée**. Votre tableau de bord affiche à présent une mosaïque avec des informations utiles.

    ![Tableau de bord avec Chronologie d'ensemble](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Ajouter un graphique de métrique personnalisée
Le panneau **Métriques** vous permet de tracer le graphique d’une métrique collectée par Application Insights au fil du temps avec des filtres et un regroupement facultatifs.  Comme tout autre élément dans Application Insights, vous pouvez ajouter ce graphique au tableau de bord.  Cela nécessite au préalable un peu de personnalisation.

1. Sélectionnez votre ressource **Application Insights** dans l’écran d’accueil.
1. Sélectionnez **Métriques**.  
2. Un graphique vide a déjà été créé, et vous êtes invité à ajouter une métrique.  Ajoutez une métrique au graphique, et éventuellement un filtre et un regroupement.  L’exemple ci-dessous présente le nombre de demandes de serveur regroupées par réussite.  Cela produit une vue dynamique des requêtes ayant réussi ou échoué.

    ![Ajouter une métrique](media/tutorial-app-dashboards/metrics.png)

4. Sélectionnez **Épingler au tableau de bord** sur la droite. Cela a pour effet d’ajouter l’affichage au dernier tableau de bord que vous avez utilisé.

3.  En haut à droite, une notification s’affiche indiquant que votre vignette a été épinglée à votre tableau de bord. Cliquez sur **Épinglé au tableau de bord** dans la notification pour revenir à votre tableau de bord ou en utiliser le panneau.

4. Cette vignette est désormais ajoutée à votre tableau de bord. Sélectionnez **Modifier** pour modifier le positionnement de la vignette. Cliquez sur la vignette et faites-la glisser à sa position, puis cliquez sur **Personnalisation terminée**.

## <a name="add-logs-analytics-query"></a>Ajouter une requête Logs (Analytics)
Azure Application Insights Logs (Analytics) utilise un langage de requête riche permettant d’analyser toutes les données collectées par Application Insights. Tout comme aux graphiques et autres affichages, vous pouvez ajouter à votre tableau de bord la sortie d’une requête Logs.

Azure Application Insights Logs (Analytics) étant un service distinct, vous devez partager votre tableau de bord de façon à ce qu’il inclue une requête Logs. Lorsque vous partagez un tableau de bord Azure, vous publiez celui-ci en tant que ressource Azure, ce qui permet de le mettre à la disposition d’autres utilisateurs et ressources.  

1. En haut de l’écran du tableau de bord, cliquez sur **Partager**.

    ![Publier un tableau de bord](media/tutorial-app-dashboards/8dashboard-share.png)

2. Conservez le **Nom du tableau de bord**, puis sélectionnez le **Nom d'abonnement** pour partager le tableau de bord.  Cliquez sur **Publier**.  Le tableau de bord est désormais à la disposition d’autres services et abonnements.  Vous pouvez éventuellement définir des utilisateurs spécifiques pouvant accéder au tableau de bord.
1. Sélectionnez votre ressource **Application Insights** dans l’écran d’accueil.
2. Cliquez sur **Logs (Analytics)** sur la gauche pour ouvrir le portail Logs (Analytics).
3. Tapez la requête suivante qui renvoie les 10 pages les plus demandées et le nombre de demandes :

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Cliquez sur **Exécuter** pour valider les résultats de la requête.
5. Cliquez sur l’épingle ![Icône d’épingle](media/tutorial-app-dashboards/pushpin.png) et sélectionnez le nom de votre tableau de bord. La raison pour laquelle vous devez sélectionner un tableau de bord contrairement aux étapes précédentes où le dernier tableau de bord était utilisé, est que la console Logs (Analytics) est un service distinct qui doit opérer une sélection parmi tous les tableaux de bord partagés disponibles.

5. Avant de revenir au tableau de bord, ajoutez une autre requête, mais cette fois sous la forme d’un graphique, afin de voir les différentes façons de visualiser une requête Logs sur un tableau de bord. Commencez avec la requête suivante qui résume les 10 opérations avec le plus grand nombre d’exceptions.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Sélectionnez **Graphique**, puis choisissez l’option **Anneau** pour visualiser la sortie.

    ![Graphique Logs (Analytics)](media/tutorial-app-dashboards/11querychart.png)

6. Cliquez sur l’épingle ![Icône d’épingle](media/tutorial-app-dashboards/pushpin.png) en haut à droite pour épingler le graphique à votre tableau de bord, en sélectionnant cette fois le lien pour revenir à votre tableau de bord.
4. Les résultats des requêtes sont à présent ajoutés à votre tableau de bord au format que vous avez sélectionné.  Cliquez sur chaque élément et faites-le glisser en position, puis cliquez sur **Personnalisation terminée**.
5. Sélectionnez l’icône de crayon ![Icône de crayon](media/tutorial-app-dashboards/pencil.png) sur chaque titre afin de leur donner un titre descriptif.

5. Sélectionnez **Partager** pour republier les modifications apportées à votre tableau de bord qui comprend désormais une série de graphiques et de visualisations d’Application Insights.


## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez appris à créer des tableaux de bord personnalisés, jetez un coup d’œil au reste de la documentation d’Application Insights, notamment à une étude de cas.

> [!div class="nextstepaction"]
> [Diagnostics profonds](../app/devops.md)

