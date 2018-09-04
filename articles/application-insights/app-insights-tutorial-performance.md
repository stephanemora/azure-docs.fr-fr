---
title: Diagnostiquer les problèmes de performances à l’aide d’Azure Application Insights | Microsoft Docs
description: Didacticiel vous permettant de rechercher et de diagnostiquer les problèmes de performances dans votre application à l’aide d’Azure Application Insights.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 4ce4c9e2479c8d570766169ce5094dcc2b4bc511
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42812869"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Rechercher et diagnostiquer les problèmes de performances à l’aide d’Azure Application Insights

Azure Application Insights collecte des données de télémétrie à partir de votre application pour analyser son fonctionnement et ses performances.  Vous pouvez utiliser ces informations pour identifier les problèmes qui peuvent se produire ou pour identifier les améliorations apportées à l’application et qui ont le plus d’impact pour les utilisateurs.  Ce didacticiel vous guide dans le processus d’analyse des performances à la fois au niveau des composants serveur de votre application et du point de vue du client.  Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Identifier les performances des opérations côté serveur
> * Analyser les opérations du serveur pour déterminer la cause de la baisse des performances
> * Identifier les opérations les plus lentes côté client
> * Analyser les détails des affichages de page à l’aide du langage de requête


## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel :

- Installez [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec les charges de travail suivantes :
    - Développement web et ASP.NET
    - Développement Azure
- Déployez une application .NET pour Azure et [activez le Kit SDK Application Insights](app-insights-asp-net.md).
- [Activer le profileur Application Insights](app-insights-profiler.md#installation) pour votre application.

## <a name="log-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Identifier les opérations lentes côté serveur
Application Insights collecte des informations sur les performances pour les différentes opérations de votre application. En identifiant ces opérations sur la plus longue période, vous pouvez diagnostiquer les problèmes potentiels ou mieux cibler votre développement actuel afin d’améliorer les performances globales de l’application.

1. Sélectionnez **Application Insights** choisissez votre abonnement.  
1. Pour ouvrir le panneau **Performances**, sélectionnez **Performances** sous le menu **Examiner**, ou cliquez sur le graphique **Temps de réponse du serveur**.

    ![Performances](media/app-insights-tutorial-performance/performance.png)

2. Le panneau **Performances** affiche le nombre et la durée moyenne de chaque opération de l’application.  Vous pouvez utiliser ces informations pour identifier les opérations qui ont le plus d’impact sur les utilisateurs. Dans cet exemple, il peut être intéressant d’examiner les opérations **GET Customers/Details** et **GET Home/Index** en raison de leur durée relativement longue et du nombre d’appels.  D’autres opérations peuvent avoir une durée supérieure, mais elles sont rarement appelées et leur amélioration n’apporterait qu’un résultat négligeable.  

    ![Panneau des performances](media/app-insights-tutorial-performance/performance-blade.png)

3. Le graphique affiche actuellement la durée moyenne des opérations sélectionnées au fil du temps. Vous pouvez basculer vers le 95e centile pour rechercher les problèmes de performances. Ajoutez les opérations qui vous intéressent en les épinglant au graphique.  Cela indique qu’il existe des pics qui méritent un examen approfondi.  Isolez cet élément en réduisant la fenêtre de temps du graphique.

    ![Épingler des opérations](media/app-insights-tutorial-performance/pin-operations.png)

4.  Le panneau de performances sur la droite affiche la distribution des durées pour différentes requêtes de l’opération sélectionnée.  Réduisez la fenêtre pour démarrer autour du 95e centile. La carte insights des « 3 principales dépendances » peut vous indiquer d’un coup d’œil que les dépendances externes contribuent probablement aux transactions lentes.  Cliquez sur le bouton avec le nombre d’exemples pour afficher la liste des exemples. Vous pouvez ensuite sélectionner un exemple quelconque pour afficher les détails de la transaction.

    ![Répartition de la durée](media/app-insights-tutorial-performance/duration-distribution.png)

5.  Vous pouvez voir d’un coup d’œil que l’appel à la Table Azure Fabrikamaccount contribue le plus à la durée totale de la transaction. Vous pouvez également voir qu’une exception a provoqué son échec. Vous pouvez cliquer sur n’importe quel élément dans la liste pour afficher ses détails sur le côté droit. [En savoir plus sur l’expérience de diagnostic des transactions](app-insights-transaction-diagnostics.md)

    ![Détails de l’opération](media/app-insights-tutorial-performance/operation-details.png)
    

6.  Le **Profileur** vous aide à en apprendre davantage sur les diagnostics de niveau de code en affichant le code qui s’exécutait pour l’opération et le temps nécessaire à chaque étape. Certaines opérations peuvent ne pas avoir de suivi car le profileur s’exécute périodiquement.  Au fil du temps, d’autres opérations devraient avoir un suivi.  Pour démarrer le profileur pour l’opération, cliquez sur **Suivis du Profileur**.
5.  Le suivi affiche les événements individuels pour chaque opération pour vous permettre d’identifier la cause de la durée de l’opération globale.  Cliquez sur un des exemples en haut de la liste, dont la durée est la plus longue.
6.  Cliquez sur **Afficher le chemin réactif** pour mettre en surbrillance le chemin d’accès spécifique aux événements qui contribuent le plus à la durée totale de l’opération.  Dans cet exemple, vous pouvez remarquer que l’appel le plus lent provient de la méthode *FabrikamFiberAzureStorage.GetStorageTableData*. La partie qui prend le plus de temps est la méthode *CloudTable.CreateIfNotExist*. Si cette ligne de code est exécutée chaque fois que la fonction est appelée, les appels réseau inutiles et les ressources du processeur sont consommées. La meilleure façon de corriger votre code est de placer cette ligne dans une méthode de démarrage qui s’exécutera une seule fois. 

    ![Détails du profileur](media/app-insights-tutorial-performance/profiler-details.png)

7.  Le **conseil relatif aux performances** affiché en haut de l’écran confirme l’hypothèse que la durée excessive est due à l’attente.  Cliquez sur le lien **en attente** pour plus d’informations sur l’interprétation les différents types d’événements.

    ![Conseil relatif aux performances](media/app-insights-tutorial-performance/performance-tip.png)

8.  Pour une analyse plus approfondie, vous pouvez cliquer sur **Télécharger la trace .etl** pour télécharger la trace dans Visual Studio.

## <a name="use-analytics-data-for-server"></a>Utiliser les données Analytics pour le serveur
Application Insights Analytics fournit un langage de requête enrichi qui vous permet d’analyser toutes les données collectées par Application Insights.  Vous pouvez utiliser ces informations pour effectuer une analyse approfondie des données concernant les requêtes et les performances.

1. Retournez au panneau des détails de l’opération et cliquez sur le bouton Analytics.

    ![Bouton Analytics](media/app-insights-tutorial-performance/server-analytics-button.png)

2. Application Insights Analytics s’ouvre et affiche une requête pour chacune des vues dans le panneau.  Vous pouvez exécuter ces requêtes en tant que telles ou les modifier selon vos besoins.  La première requête indique la durée de cette opération au fil du temps.

    ![Analytics](media/app-insights-tutorial-performance/server-analytics.png)


## <a name="identify-slow-client-operations"></a>Identifier les opérations lentes côté client
En plus d’identifier les processus côté serveur à optimiser, Application Insights peut effectuer une analyse du point de vue des navigateurs clients.  Cela peut vous aider à identifier les améliorations potentielles des composants clients et même identifier les problèmes avec différents navigateurs ou emplacements.

1. Sélectionnez **Navigateur** sous **Examiner** pour ouvrir le résumé du navigateur.  Vous obtenez un résumé visuel des différentes données de télémétrie de votre application du point de vue du navigateur.

    ![Résumé du navigateur](media/app-insights-tutorial-performance/browser-summary.png)

2.  Faites défiler jusqu'à la section **Quelles sont les pages les plus lentes ?**.  Cette liste affiche les pages de votre application que les clients ont mis le plus de temps à charger.  Vous pouvez utiliser ces informations pour classer par priorité les pages qui ont l’impact le plus important sur l’utilisateur.
3.  Cliquez sur une des pages pour ouvrir le panneau **Affichage de la page**.  Dans l’exemple, la page **/FabrikamProd** affiche une durée moyenne excessive.  Le panneau **Affichage de la page** fournit des détails sur cette page, y compris une répartition des différentes plages de durée.

    ![Affichage de la page](media/app-insights-tutorial-performance/page-view.png)

4.  Cliquez sur la durée la plus élevée pour examiner les détails de ces requêtes.  Cliquez ensuite sur la requête individuelle pour afficher les détails du client qui demande la page, y compris le type de navigateur et son emplacement.  Ces informations peuvent vous aider à déterminer certains types de clients rencontrent des problèmes de performances.

    ![Détails de la requête](media/app-insights-tutorial-performance/request-details.png)

## <a name="use-analytics-data-for-client"></a>Utiliser les données Analytics pour le client
Comme avec les données collectées pour les performances du serveur, Application Insights rend toutes les données du client disponibles pour une analyse approfondie à l’aide d’Analytics.

1. Retournez au résumé du navigateur, puis cliquez sur l’icône Analytics.

    ![Icône Analytics](media/app-insights-tutorial-performance/client-analytics-icon.png)

2. Application Insights Analytics s’ouvre et affiche une requête pour chacune des vues dans le panneau. La première requête indique la durée de différents affichages de page au fil du temps.

    ![Analytics](media/app-insights-tutorial-performance/client-analytics.png)

3.  Smart Diagnostics est une fonctionnalité d’Application Insights Analytics qui identifie des modèles uniques dans les données.  Lorsque vous cliquez sur le point Smart Diagnostics dans le graphique en courbes, la même requête est exécutée sans les enregistrements qui ont provoqué l’anomalie.  Les détails de ces enregistrements sont affichés dans la section des commentaires de la requête pour vous permettre d’identifier les propriétés de ces affichages de page qui sont à l’origine de la durée excessive.

    ![Smart Diagnostics](media/app-insights-tutorial-performance/client-smart-diagnostics.png)


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à identifier les exceptions d’exécution, passez à l’étape suivante du didacticiel pour apprendre à créer des alertes en réponse aux défaillances.

> [!div class="nextstepaction"]
> [Alerter sur l’intégrité des applications](app-insights-tutorial-alert.md)
