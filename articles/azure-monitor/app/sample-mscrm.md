---
title: Microsoft Dynamics CRM et Azure Application Insights | Microsoft Docs
description: Obtenez des données de télémétrie à partir de Microsoft Dynamics CRM Online à l’aide d’Application Insights. Procédure pas à pas de configuration, obtention de données, visualisation et exportation.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/16/2018
ms.reviewer: mazhar
ms.author: mbullwin
ms.openlocfilehash: 470f723782ca29409549e0df8e900edf86cd446e
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534281"
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Procédure pas à pas : activation de télémétrie pour Microsoft Dynamics CRM Online à l’aide d’Application Insights
Cet article décrit comment obtenir des données de télémétrie à partir de [Microsoft Dynamics CRM Online](https://www.dynamics.com/) à l’aide [d’Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Nous explorerons le processus complet d’ajout de script Application Insights à votre application, de capture de données et de visualisation des données.

> [!NOTE]
> [Parcourez l’exemple de solution](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Ajouter Application Insights à une instance de CRM Online nouvelle ou existante
Pour analyser votre application, vous devez y ajouter un Kit de développement logiciel (SDK) Application Insights. Le kit de développement logiciel (SDK) envoie les données de télémétrie au [portail Application Insights](https://portal.azure.com), où vous pouvez utiliser nos puissants outils de diagnostic et d’analyse ou exporter les données vers un emplacement de stockage.

### <a name="create-an-application-insights-resource-in-azure"></a>Créer une ressource Application Insights dans Azure
1. Obtenez un [compte dans Microsoft Azure](https://azure.com/pricing). 
2. Connectez-vous au [portail Azure](https://portal.azure.com) et ajoutez une nouvelle ressource Application Insights. C’est là où vos données seront traitées et affichées.

    ![Cliquez sur +, Services de développement, Application Insights.](./media/sample-mscrm/01.png)

    Choisissez le type d'application ASP.NET.
3. Suivez les instructions pour [obtenir le script SDK JavaScript pour votre application](../../azure-monitor/app/javascript.md), copiez l’extrait JavaScript et assurez-vous de remplacer la clé d’instrumentation par la valeur correcte pour votre ressource Application Insights.

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Créer une ressource web JavaScript dans Microsoft Dynamics CRM
1. Ouvrez votre instance de CRM Online et connectez-vous avec des privilèges d’administrateur.
2. Ouvrez les paramètres Microsoft Dynamics CRM, Personnalisations, Personnaliser le système

    ![Paramètres Microsoft Dynamics CRM](./media/sample-mscrm/00001.png)

    ![Paramètres > Personnalisations](./media/sample-mscrm/00002.png)

1. Créez une ressource JavaScript.

    ![Nouvelle boîte de dialogue ressource web](./media/sample-mscrm/07.png)

    Donnez-lui un nom, sélectionnez **Script (JScript)** et ouvrez l’éditeur de texte.

    ![Ouvrez l’éditeur de texte](./media/sample-mscrm/00004.png)
2. Copiez le code du SDK JavaScript Application Insights dans lequel vous avez configuré votre clé d’instrumentation précédemment. Lors de la copie, veillez à ignorer les balises de script. Reportez-vous à la capture d’écran ci-après :

    ![Définissez votre clé d’instrumentation](./media/sample-mscrm/000005.png)

    Le code contient la clé d’instrumentation qui identifie votre ressource Application Insights.
3. Enregistrez et publiez.

    ![Enregistrez et publiez](./media/sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Instrumenter les formulaires
1. Dans Microsoft CRM Online, ouvrez le formulaire Compte.

    ![Formulaire de compte](./media/sample-mscrm/00007.png)
2. Ouvrez les propriétés de formulaire.

    ![Propriétés de formulaire](./media/sample-mscrm/00008.png)
3. Ajoutez la ressource web JavaScript que vous avez créée.

    ![Ajoutez un menu](./media/sample-mscrm/13.png)

4. Enregistrez et publiez vos personnalisations de formulaire.

## <a name="metrics-captured"></a>Mesures capturées
Vous avez maintenant configuré la capture de télémétrie pour le formulaire. Chaque fois qu’il sera utilisé, des données seront envoyées à votre ressource Application Insights.

Voici des exemples de données qui seront affichées.

#### <a name="application-health"></a>Intégrité d’application
![Exemple de temps de chargement de la page](./media/sample-mscrm/15.png)

![Exemple de graphique des affichages de pages](./media/sample-mscrm/16.png)

Exceptions du navigateur :

![Graphique des exceptions du navigateur](./media/sample-mscrm/17.png)

Cliquez sur le graphique pour obtenir plus de détails :

![Liste des exceptions](./media/sample-mscrm/18.png)

#### <a name="usage"></a>Usage
![Utilisateurs, sessions et affichages de page](./media/sample-mscrm/19.png)

![Graphiques de session](./media/sample-mscrm/20.png)

![Versions de navigateur](./media/sample-mscrm/21.png)

#### <a name="browsers"></a>Navigateurs
![Répartition du temps de chargement de la page](./media/sample-mscrm/22.png)

![Nombre de sessions par version de navigateur](./media/sample-mscrm/23.png)

#### <a name="geolocation"></a>Géolocalisation
![Nombre de sessions par pays](./media/sample-mscrm/24.png)

![Sessions et utilisateurs par pays](./media/sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Demande d’affichage de page
![Résumé d’affichage de la page](./media/sample-mscrm/26.png)

![Recherche sur les événements d’affichage de page](./media/sample-mscrm/27.png)

![Affichages de page similaires](./media/sample-mscrm/28.png)

![Propriétés d'affichage de la page](./media/sample-mscrm/29.png)

![Pages par session](./media/sample-mscrm/30.png)

## <a name="sample-code"></a>Exemple de code
[Parcourez l'exemple de code](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Vous pouvez effectuer une analyse encore plus approfondie si vous [exportez les données vers Microsoft Power BI](../../azure-monitor/app/export-power-bi.md ).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Exemple de solution Microsoft Dynamics CRM
[Voici l’exemple de solution implémenté dans Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>En savoir plus
* [Présentation d’Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Application Insights pour les pages web](../../azure-monitor/app/javascript.md)
* [Plus d'exemples et de procédures pas à pas](../../azure-monitor/app/app-insights-overview.md)
