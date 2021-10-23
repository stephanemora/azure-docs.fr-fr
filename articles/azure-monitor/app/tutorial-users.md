---
title: Comprendre vos clients dans Application Insights | Microsoft Docs
description: Didacticiel sur l’utilisation d’Application Insights pour comprendre comment les clients utilisent votre application.
ms.topic: tutorial
ms.date: 07/30/2021
ms.custom: mvc
ms.openlocfilehash: f06522ffe257bc7e20fe587b7c0a4479f6677240
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129621"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Utiliser Azure Application Insights pour comprendre comment les clients utilisent votre application

 Application Insights collecte des informations d’utilisation pour vous aider à comprendre comment vos utilisateurs interagissent avec votre application.  Ce didacticiel vous présente les différentes ressources disponibles pour analyser ces informations.  Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Analyser les détails sur les utilisateurs qui accèdent à votre application
> * Utiliser les Informations de session pour analyser la façon dont les clients utilisent votre application
> * Définir des entonnoirs qui vous permettent de comparer l’activité idéale d’un utilisateur et son activité réelle 
> * Créer un classeur pour consolider les visualisations et les requêtes dans un même document
> * Regrouper les utilisateurs similaires pour les analyser ensemble
> * Identifier les utilisateurs qui retournent dans votre application
> * Examiner la façon dont les utilisateurs naviguent dans votre application


## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce tutoriel :

- Installez [Visual Studio 2019](https://www.visualstudio.com/downloads/) avec les charges de travail suivantes :
    - Développement web et ASP.NET
    - Développement Azure
- Téléchargez et installez le [débogueur d'instantané de Visual Studio](https://aka.ms/snapshotdebugger).
- Déployez une application .NET pour Azure et [activez le Kit SDK Application Insights](../app/asp-net.md). 
- [Envoyez des données de télémétrie à partir de votre application](../app/usage-overview.md#send-telemetry-from-your-app) pour ajouter des affichages d’événement/de page personnalisés.
- Envoyez un [contexte utilisateur](./usage-overview.md) pour suivre les actions d’un utilisateur au fil du temps et exploiter pleinement les fonctionnalités d’utilisation.

## <a name="log-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com).

## <a name="get-information-about-your-users"></a>Obtenir des informations sur vos utilisateurs
Le panneau **Utilisateurs** vous permet de comprendre les détails importants sur vos utilisateurs de plusieurs façons. Vous pouvez utiliser ce panneau pour comprendre ces informations, par exemple l’emplacement à partir duquel vos utilisateurs se connectent, les détails de leurs clients, et les zones de votre application auxquelles ils accédent. 

1. Dans votre ressource Application Insights, sous *Utilisation*, sélectionnez **Utilisateurs** dans le menu.
2. L’affichage par défaut indique le nombre d’utilisateurs uniques qui se sont connectés à votre application au cours des dernières 24 heures.  Vous pouvez modifier la fenêtre de temps et définir divers autres critères pour filtrer ces informations.

3. Cliquez sur la liste déroulante **Pendant** et choisissez une fenêtre de temps de 7 jours.  Cette option augmente les données incluses dans les différents graphiques du panneau.

4. Cliquez sur la liste déroulante **Fractionner par** pour ajouter une répartition par propriété d’utilisateur au graphique.  Sélectionnez un **pays ou une région**.  Le graphique inclut les mêmes données mais vous permet d’afficher une répartition du nombre d’utilisateurs pour chaque pays/région.

      :::image type="content" source="./media/tutorial-users/user-1.png" alt-text="Capture d’écran du générateur de requêtes de l’onglet Utilisateur." lightbox="./media/tutorial-users/user-1.png":::

5. Placez le curseur sur les différentes barres du graphique et notez que le nombre pour chaque pays/région correspond uniquement à la fenêtre de temps représentée par cette barre.
6. Sélectionnez **Afficher plus d’insights** pour plus d’informations. 

      :::image type="content" source="./media/tutorial-users/user-2.png" alt-text="Capture d’écran de l’onglet Utilisateur de Voir plus d’insights." lightbox="./media/tutorial-users/user-2.png":::


## <a name="analyze-user-sessions"></a>Analyser les sessions utilisateur
Le panneau **Sessions** est similaire au panneau **Utilisateurs**.  **Utilisateurs** vous permet d’obtenir des détails sur les utilisateurs qui accèdent à votre application, tandis que **Sessions** vous aide à comprendre comment ces utilisateurs se servent de votre application.  

1. *Utilisation* de l’utilisateur, sélectionnez **Sessions**.
2. Examinez le graphique et notez que vous disposez des mêmes options pour filtrer et décomposer les données comme dans le panneau **Utilisateurs**.

     :::image type="content" source="./media/tutorial-users/sessions.png" alt-text="Capture d’écran de l’onglet Sessions avec un graphique à barres affiché." lightbox="./media/tutorial-users/sessions.png":::

4. Pour afficher la chronologie de la session, sélectionnez **Afficher plus d’insights**, puis sous Sessions actives, **Afficher la chronologie de session** sur l’une des chronologies. La chronologie de la session montre toutes les actions dans les sessions. Cette chronologie peut vous aider à identifier des informations telles que les sessions contenant un grand nombre d’exceptions.

     :::image type="content" source="./media/tutorial-users/timeline.png" alt-text="Capture d’écran de l’onglet Sessions avec une chronologie affichée." lightbox="./media/tutorial-users/timeline.png":::

## <a name="group-together-similar-users"></a>Regrouper les utilisateurs similaires
Une **cohorte** est un ensemble de groupes d’utilisateurs qui partagent des caractéristiques similaires.  Vous pouvez utiliser des cohortes pour filtrer les données dans d’autres panneaux, ce qui vous permet d’analyser des groupes d’utilisateurs particuliers.  Par exemple, vous pouvez analyser uniquement les utilisateurs ayant effectué un achat.

1.  Sélectionnez **Créer une cohorte** en haut de l’un des onglets d’utilisation (utilisateurs, sessions, événements, etc.).

1.  Sélectionnez un modèle dans la galerie.

    :::image type="content" source="./media/tutorial-users/cohort.png" alt-text="Capture d’écran de la Galerie de modèles pour les cohortes." lightbox="./media/tutorial-users/cohort.png":::
1.  Modifiez votre cohorte puis sélectionnez **Enregistrer**.
1.  Pour voir votre cohorte, sélectionnez-la dans le menu déroulant **Affichage**. 

    :::image type="content" source="./media/tutorial-users/cohort-2.png" alt-text="Capture d’écran de la liste déroulante Afficher, affichant une cohorte." lightbox="./media/tutorial-users/cohort-2.png":::


## <a name="compare-desired-activity-to-reality"></a>Comparer l’activité souhaitée à la réalité
Alors que les panneaux précédents se concentrent sur ce que les utilisateurs ont fait avec votre application, les **entonnoirs** se focalisent sur ce que vous souhaitez que les utilisateurs fassent.  Un entonnoir représente un ensemble d’étapes dans votre application, avec le pourcentage d’utilisateurs qui passent d’une étape à l’autre.  Par exemple, vous pouvez créer un entonnoir qui calcule le pourcentage d’utilisateurs qui se connectent à votre application pour rechercher un produit.  Vous pouvez ainsi voir le pourcentage d’utilisateurs qui ajoutent ce produit à leur panier, puis le pourcentage de ceux qui ont effectué un achat.

1. Sélectionnez **Entonnoirs** dans le menu, puis sélectionnez **Modifier**. 

3. Créez un entonnoir avec au moins deux étapes en sélectionnant une action pour chaque étape.  La liste d’actions est générée à partir des données d’utilisation collectées par Application Insights.

    :::image type="content" source="./media/tutorial-users/funnel.png" alt-text="Capture d’écran de l’onglet Entonnoir et sélection des étapes dans l’onglet de modification." lightbox="./media/tutorial-users/funnel.png":::

4. Sélectionnez l’onglet **Affichage** pour voir les résultats. La fenêtre à droite montre les événements les plus courants avant la première activité et après la dernière activité pour vous aider à comprendre les tendances des utilisateurs qui caractérisent la séquence particulière.

     :::image type="content" source="./media/tutorial-users/funnel-2.png" alt-text="Capture d’écran de l’onglet Entonnoir sur la vue." lightbox="./media/tutorial-users/funnel-2.png":::

4. Sélectionnez **Enregistrer** pour enregistrer l’entonnoir. 

## <a name="learn-which-customers-return"></a>Identifier les clients récurrents

L’option **Rétention** vous aide à identifier les utilisateurs qui retournent dans votre application.  

1. Sélectionnez **Rétention** dans le menu, puis *Classeur d’analyse de rétention.
2. Par défaut, les informations analysées incluent les utilisateurs qui ont effectué une action, puis qui sont retournés dans l’application pour effectuer à nouveau une action.  Vous pouvez modifier ce filtre, par exemple pour inclure uniquement les utilisateurs qui y sont retournés après avoir effectué un achat.

      :::image type="content" source="./media/tutorial-users/retention.png" alt-text="Capture d’écran montrant un graphe pour les utilisateurs correspondant aux critères définis pour un filtre de rétention." lightbox="./media/tutorial-users/retention.png":::

3. Les utilisateurs récurrents qui répondent aux critères apparaissent sous forme d’un graphique et d’un tableau pour différentes durées. Le modèle par défaut montre une baisse progressive des utilisateurs récurrents au fil du temps.  Une baisse soudaine d’une période à une autre peut indiquer un problème. 

      :::image type="content" source="./media/tutorial-users/retention-2.png" alt-text="Capture d’écran du classeur de rétention, montrant le retour de l’utilisateur après le graphique # de semaines." lightbox="./media/tutorial-users/retention-2.png":::

## <a name="analyze-user-navigation"></a>Analyser la navigation de l’utilisateur
Un **flux utilisateur** permet de visualiser la façon dont vos utilisateurs naviguent entre les pages et les fonctionnalités de votre application.  Cela vous permet de répondre à certaines questions, par exemple à quel moment les utilisateurs quittent une page particulière, comment ils ferment votre application, et si des actions sont répétées régulièrement.

1.  Sélectionnez **Flux d’utilisateurs** dans le menu.
2.  Cliquez sur **Nouveau** pour créer un flux utilisateur, puis sélectionnez **Modifier** pour modifier ses détails.
3.  Augmentez l’**intervalle de temps** à 7 jours, puis sélectionnez un événement initial.  Le flux effectuera le suivi des sessions utilisateur qui commencent par cet événement.

     :::image type="content" source="./media/tutorial-users/flowsedit.png" alt-text="Capture d’écran montrant comment créer un flux d’utilisateur." lightbox="./media/tutorial-users/flowsedit.png":::
    
4.  Le flux d’utilisateur s’affiche et montre les différents chemins d’accès des utilisateurs et leur nombre de sessions.  Les lignes bleues indiquent une action effectuée par l’utilisateur après l’action en cours.  Une ligne rouge indique la fin de la session de l’utilisateur.

   :::image type="content" source="./media/tutorial-users/flows.png" alt-text="Capture d’écran montrant l’affichage de chemins d’accès d’utilisateur et de nombres de sessions pour un flux d’utilisateur." lightbox="./media/tutorial-users/flows.png":::

5.  Pour supprimer un événement du flux, sélectionnez le signe **x** dans l’angle de l’action, puis **Créer un graphique**.  Le graphique est redessiné et toutes les instances de cet événement sont supprimées. Sélectionnez **Modifier** pour constater que l’événement est désormais ajouté à la liste des **événements exclus**.

    :::image type="content" source="./media/tutorial-users/flowsexclude.png" alt-text="Capture d’écran montrant la liste d’événements exclus pour un flux d’utilisateur." lightbox="./media/tutorial-users/flowsexclude.png":::

## <a name="consolidate-usage-data"></a>Consolider les données d’utilisation
Les **classeurs** associent des visualisations de données, des requêtes Analytics et du texte dans des documents interactifs.  Vous pouvez utiliser des classeurs pour regrouper les informations d’utilisation communes, consolider les informations d’un incident spécifique, ou fournir à votre équipe des détails sur l’utilisation de votre application.

1.  Sélectionnez **Classeurs** dans le menu.
2.  Sélectionnez **Nouveau** pour créer un classeur.
3.  Une requête est déjà fournie et inclut toutes les données d’utilisation du dernier jour sous la forme d’un graphique à barres.  Vous pouvez utiliser cette requête, la modifier manuellement, ou sélectionner **Exemples de requêtes** pour sélectionner d’autres requêtes utiles.

    :::image type="content" source="./media/tutorial-users/sample-queries.png" alt-text="Capture d’écran montrant le bouton d’exemple une liste d’exemples de requêtes que vous pouvez utiliser." lightbox="./media/tutorial-users/sample-queries.png":::

4.  Sélectionnez **Modification terminée**.
5.  Sélectionnez **Modifier** dans le volet supérieur pour modifier le texte en haut du classeur.  Le texte est mis en forme à l’aide de markdown.

6.  Sélectionnez **Ajouter des utilisateurs** pour ajouter un graphique avec des informations sur les utilisateurs.  Modifiez les détails du graphique si vous le souhaitez, puis sélectionnez **Fin de l’édition** pour enregistrer le graphique.

Pour en savoir plus sur les classeurs, consultez [la vue d’ensemble des classeurs](../visualize/workbooks-overview.md).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à analyser vos utilisateurs, passez à l’étape suivante du didacticiel pour apprendre à créer des tableaux de bord personnalisés qui combinent ces informations avec d’autres données utiles concernant votre application.

> [!div class="nextstepaction"]
> [Créer des tableaux de bord personnalisés](./tutorial-app-dashboards.md)