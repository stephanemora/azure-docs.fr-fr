---
title: Comprendre l’utilisation des applications mobiles et le comportement des utilisateurs avec Visual Studio App Center et les services Azure
description: Découvrez les services tels qu’App Center qui vous aident à prendre des décisions professionnelles avisées en comprenant comment les utilisateurs utilisent votre application mobile.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: d60e9437a2f33788eb183cfcad0f3a10d71fb79d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795177"
---
# <a name="analyze-and-understand-usage-of-mobile-applications"></a>Analyser et comprendre l’utilisation des applications mobiles
Savez-vous réellement comment vos utilisateurs utilisent vos applications ? Combien d’utilisateurs actifs votre application a-t-elle, et comment l’utilisation évolue-t-elle au fil du temps ? Quelles fonctionnalités utilisent-ils, et quelles sont celles qui sont le plus utilisées ? Où ces utilisateurs sont-ils basés ? Combien d’utilisateurs utilisent la version la plus récente de l’application ? Il est important de comprendre toutes ces questions afin de pouvoir faire de votre application un succès. Pour répondre à ce genre de questions sur l’analyse de l’utilisation, vous devez recueillir des données d’utilisation à partir de vos applications.

Plus vous explorerez les données, plus vous serez en mesure de trouver des moyens d’améliorer votre application et de satisfaire vos utilisateurs. Il est important d’utiliser les données pour trouver des insights actionnables et conserver un haut degré de satisfaction des utilisateurs.

## <a name="importance-of-analytics"></a>Importance de l’analyse
- **Comprenez** vos utilisateurs, la manière dont ils interagissent avec votre application et ce qui les attire, de manière à pouvoir affiner votre application et fournir de superbes expériences pour développer votre activité.
- **Suivez** vos métriques d’utilisation pour prendre des décisions avisées sur la façon de commercialiser votre application et de mieux servir votre clientèle.
- **Mesurez** les performances de votre application.
- **Découvrez** quelles parties de votre application génèrent de la valeur et accroissent ses performances.
- Obtenez des **insights axés sur les données** concernant des aspects liés à la rétention et à l’attrition clients.

Utilisez les services suivants pour activer l’analyse des applications mobiles.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Analytics](/appcenter/analytics/) vous permet d’élargir votre public en vous concentrant sur le plus important, avec des rapports et des insights approfondis sur les sessions d’utilisateur, les principaux appareils, les versions de système d’exploitation et l’analyse comportementale. Créez facilement des événements personnalisés pour effectuer un suivi de tout ce qui vous intéresse avec les fonctionnalités d’analyse d’application extensive.

   **Fonctionnalités clés**
   - **Suivi** gratuit des modèles d’utilisation, de l’adoption par les utilisateurs et d’autres métriques d’engagement.
   - **Identification** des tendances, du comportement des utilisateurs et de l’engagement par le biais d’événements personnalisés.
   - **Métriques prêtes à l’emploi** et **insights détaillés** concernant l’utilisation des applications (quotidienne, hebdomadaire, mensuelle), les sessions, les propriétés des appareils et les données démographiques des utilisateurs dans un **tableau de bord unique**.
   - **Exportation en continu de toutes vos données d’analyse dans Azure** pour une conservation illimitée. Prise en charge de l’exportation vers Stockage Blob Azure et Azure Application Insights.
   - **Intégration à Azure Application Insights** pour des analyses encore plus approfondies, telles que la conservation, l’analyse en entonnoir et les cohortes.
   - **Intégration de SDK en une ligne** pour bien démarrer en quelques minutes.
   - **Prise en charge de plateformes** : iOS, Android, macOS, tvOS, Xamarin, React Native, Unity, Cordova.

   **Informations de référence**
   - [S’inscrire auprès d’App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Bien démarrer avec l’analyse App Center](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor inclut [Application Insights](/azure/azure-monitor/app/app-insights-overview), qui fournit des outils permettant de recueillir et d’analyser des données de télémétrie afin d’optimiser les performances et de superviser votre application mobile. Vous pouvez tirer parti d’Application Insights en utilisant App Center Analytics et en configurant l’exportation vers Application Insights. Application Insights peut interroger, segmenter, filtrer et analyser les données de télémétrie des événements personnalisés à partir de vos applications, au-delà des outils d’analyse fournis par App Center.

**Fonctionnalités clés**
   - **Interrogez** les données de télémétrie de vos événements personnalisés.
   - **Filtrez** les données de télémétrie d’événements avec de puissantes fonctionnalités de segmentation.
   - **Analysez** les modèles de conversion, de rétention et de navigation dans votre application.
     - **Entonnoirs**, pour analyser et surveiller le taux de conversion.
     - **Rétention**, pour analyser dans quelle mesure votre application conserve les utilisateurs au fil du temps.
     - **Classeurs**, pour combiner des visualisations et du texte dans un rapport partageable.
     - **Cohortes**, pour nommer et enregistrer des groupes d’utilisateurs ou d’événements spécifiques afin qu’ils puissent être facilement référencés à partir d’autres outils d’analytique.

**Informations de référence**
- [Portail Azure](https://portal.azure.com/)
- [Analyser votre application mobile avec App Center et Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Analyse de l’utilisation avec Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/) offre une plateforme back-end complète avec des services de jeu, des analyses en temps réel et les LiveOps dont vous avez besoin pour créer des jeux de classe mondiale connectés au cloud. Ces services réduisent les obstacles au lancement pour les développeurs de jeux, et offrent aux studios de toute taille des solutions de développement économiques qui évoluent avec leurs jeux et les aident à attirer, conserver et monétiser des joueurs. PlayFab permet aux développeurs d’utiliser le cloud intelligent pour créer et exploiter des jeux, analyser des données de jeu et améliorer l’expérience de jeu globale.

**Fonctionnalités clés**
   - **Supervision** avec des tableaux de bord en temps réel.
   - **Évaluation** des performances de votre jeu grâce aux métriques les plus importantes.
   - **Rapports** permettant d’obtenir des synthèses des performances quotidiennes et mensuelles de votre jeu. Il s’agit de rapports générés automatiquement qui peuvent être affichés dans Game Manager et téléchargés ou remis quotidiennement à votre boîte de réception.
   - **Tests A/B** pour exécuter des expérimentations et déterminer le paramètre optimal pour une variable particulière.
   - La **segmentation** pour les joueurs vous permet de définir des regroupements automatisés de joueurs.
    
**Informations de référence**
- [Portail PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Analyse](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Démarrages rapides](/gaming/playfab/#pivot=documentation&panel=quickstarts)    
