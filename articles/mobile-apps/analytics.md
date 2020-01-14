---
title: Comprendre l’utilisation des applications mobiles et le comportement des utilisateurs avec Visual Studio App Center et les services Azure
description: Découvrez les services tels qu’App Center qui vous aident à prendre des décisions professionnelles avisées en comprenant comment les utilisateurs utilisent votre application mobile.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: ff25df55e8489819ca9400205cdea1b8ceb8bf80
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454531"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Analyser et comprendre l'utilisation des applications mobiles
Savez-vous réellement comment vos utilisateurs utilisent vos applications ? Combien d'utilisateurs actifs votre application compte-t-elle, et comment l'utilisation évolue-t-elle au fil du temps ? Quelles fonctionnalités utilisent-ils, et quelles sont les plus utilisées ? Où ces utilisateurs sont-ils basés ? Combien d’utilisateurs utilisent la version la plus récente de l’application ? Il est important de comprendre toutes ces questions afin de pouvoir faire de votre application un succès. Pour répondre à ce genre de questions sur l’analyse de l’utilisation, vous devez recueillir des données d’utilisation à partir de vos applications.

Plus vous explorerez les données, plus vous serez en mesure de trouver des moyens d'améliorer votre application et de satisfaire vos utilisateurs. Il est important d'utiliser les données pour trouver des insights exploitables et maintenir la satisfaction des utilisateurs.

## <a name="importance-of-analytics"></a>Importance de l'analyse
- Étudiez vos utilisateurs, la façon dont ils interagissent avec votre application et ce qui les attire, de manière à pouvoir affiner votre application et fournir de superbes expériences pour développer votre activité.
- Suivez vos métriques d'utilisation pour prendre des décisions avisées sur la façon de commercialiser votre application et de mieux servir vos clients.
- Mesurez les performances de votre application.
- Découvrez quelles parties de votre application génèrent de la valeur et accroissent les performances.
- Bénéficiez d'insights axés sur les données sur les aspects liés à la conservation et à l'attrition clients.

Utilisez les services suivants pour activer l’analyse des applications mobiles.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Les [données d'analyse de l'App Center](/appcenter/analytics/) vous permettent d'élargir votre public en vous concentrant sur l'essentiel. Elles offrent des rapports et des insights détaillés sur les sessions utilisateur, les principaux appareils, les versions du système d'exploitation et l'analyse comportementale. Créez facilement des événements personnalisés pour effectuer un suivi de tout ce qui vous intéresse avec les fonctionnalités d’analyse d’application extensive.

   **Fonctionnalités clés**
   - Suivi gratuit des modèles d'utilisation, de l'adoption par les utilisateurs et d'autres métriques d'engagement.
   - Identification des tendances, du comportement des utilisateurs et de l'engagement par le biais d'événements personnalisés.
   - Métriques prêtes à l'emploi et insights détaillés sur l'utilisation des applications (quotidienne, hebdomadaire, mensuelle), les sessions, les propriétés des appareils et les données démographiques des utilisateurs au sein d'un tableau de bord unique.
   - Exportation en continu de toutes vos données d'analyse de l'App Center vers Azure pour une conservation illimitée. Prise en charge de l'exportation des données d'analyse de l'App Center vers Stockage Blob Azure et Azure Application Insights.
   - Intégration à Azure Application Insights pour des analyses encore plus approfondies (conservation, analyse en entonnoir, cohortes, etc.).
   - Intégration de SDK en une ligne pour bien démarrer en quelques minutes.
   - Prise en charge des plateformes iOS, Android, macOS, tvOS, Xamarin, React Native, Unity et Cordova.

   **Informations de référence**
   - [S’inscrire auprès d’App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Bien démarrer avec l’analyse App Center](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor inclut [Application Insights](/azure/azure-monitor/app/app-insights-overview), qui fournit des outils permettant de recueillir et d'analyser des données de télémétrie afin d'optimiser les performances et de superviser votre application mobile. Vous pouvez tirer parti d'Application Insights en utilisant les données d'analyse de l'App Center pour configurer l'exportation vers Application Insights. Application Insights peut interroger, segmenter, filtrer et analyser les données de télémétrie des événements personnalisés à partir de vos applications, au-delà des outils d'analyse fournis par App Center.

**Fonctionnalités clés**
   - Interrogez les données de télémétrie de vos événements personnalisés.
   - Filtrez les données de télémétrie des événements grâce à de puissantes fonctionnalités de segmentation.
   - Analysez les modèles de conversion, de conservation et de navigation dans votre application. Vous pouvez utiliser :
     - Des entonnoirs, pour analyser et surveiller les taux de conversion
     - La conservation, pour analyser dans quelle mesure votre application conserve les utilisateurs au fil du temps
     - Des classeurs, pour combiner des visualisations et du texte dans un rapport partageable
     - Des cohortes, pour nommer et enregistrer des groupes d'utilisateurs ou d'événements spécifiques afin qu'ils puissent être facilement référencés à partir d'autres outils d'analyse

**Informations de référence**
- [Azure portal](https://portal.azure.com/)
- [Analyser votre application mobile avec App Center et Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Utiliser les données d'analyse de l'App Center avec Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/) offre une plateforme back-end complète avec des services de jeu, des analyses en temps réel et les LiveOps dont vous avez besoin pour créer des jeux de premier ordre connectés au cloud. Ces services réduisent les obstacles au lancement pour les développeurs de jeux. Ils offrent aux grands comme aux petits studios des solutions de développement rentables qui évoluent avec leurs jeux. Ces services peuvent aider les studios à attirer des joueurs, à les fidéliser et à les faire payer. PlayFab permet aux développeurs d'utiliser le cloud intelligent pour créer et exploiter des jeux, analyser des données de jeu et améliorer l'expérience de jeu globale.

**Fonctionnalités clés**
   - Supervision avec des tableaux de bord en temps réel.
   - Évaluation des performances de votre jeu grâce aux métriques les plus importantes.
   - Résumés des performances quotidiennes et mensuelles de votre jeu grâce à des rapports générés automatiquement. Vous pouvez consulter les rapports dans le Gestionnaire de jeux, et les télécharger ou les recevoir quotidiennement dans votre boîte de réception.
   - Tests A/B afin d'exécuter des expérimentations et de déterminer le paramètre optimal pour une variable particulière.
   - Segmentation destinée aux joueurs pour définir des regroupements automatisés de joueurs.
    
**Informations de référence**
- [Portail PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Analyse](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Démarrages rapides](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
