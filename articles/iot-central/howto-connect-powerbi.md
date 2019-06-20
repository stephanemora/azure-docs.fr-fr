---
title: Visualiser des données Azure IoT Central dans un tableau de bord Power BI | Microsoft Docs
description: Utilisez la solution Power BI pour Azure IoT Central pour visualiser et analyser vos données IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.openlocfilehash: 16d7691d59e0805c7f8cf565a599ca5e6a78a7cf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053050"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualiser et analyser des données Azure IoT Central dans un tableau de bord Power BI

*Cette rubrique s’applique aux administrateurs.*

![Pipeline de solution Power BI](media/howto-connect-powerbi/iot-continuous-data-export.png)

Utilisez la solution Power BI pour Azure IoT Central pour créer un tableau de bord Power BI puissant qui effectue le monitoring des performances de vos appareils IoT. Ce tableau de bord Power BI permet :
- d’effectuer le suivi de la quantité de données envoyées par les appareils au fil du temps ;
- de comparer le volume de données entre la télémétrie, les états et les événements ;
- d’identifier les appareils qui signalent un grand nombre de mesures ;
- d’observer des tendances historiques dans les mesures des appareils ;
- d’identifier les appareils problématiques qui envoient un grand nombre d’événements critiques.

Cette solution configure le pipeline qui prend les données du compte Stockage Blob Azure provenant de l’[exportation continue de données](howto-export-data.md). Ces données transitent par Azure Functions, Azure Data Factory et Azure SQL Database pour leur traitement et leur transformation. La sortie peut être visualisée et analysée dans un rapport Power BI téléchargeable au format PBIX. Toutes ces ressources étant créées dans votre abonnement Azure, vous pouvez personnaliser chaque composant en fonction de vos besoins.

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Obtenez la [solution Power BI pour Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate) à partir de Microsoft AppSource.

## <a name="prerequisites"></a>Prérequis
Voici les prérequis pour configurer la solution :
- l’accès à un abonnement Azure ;
- des données exportées de votre application IoT Central avec [l’exportation de données continue](howto-export-data.md) (il est recommandé d’activer les mesures, les appareils et les flux de modèles d’appareils pour tirer le meilleur parti possible du tableau de bord Power BI) ;
- Power BI Desktop (dernière version) ;
- Power BI Pro (si vous souhaitez partager le tableau de bord avec d’autres utilisateurs).

## <a name="reports"></a>Rapports

Deux rapports sont générés automatiquement. 

Le premier rapport montre l’historique des mesures signalées par les appareils et décompose les différents types de mesures et les appareils qui ont envoyé le plus grand nombre de mesures.

![Page 1 du rapport Power BI](media/howto-connect-powerbi/template-page1-hasdata.PNG)

Le deuxième rapport explore plus en détail les événements et présente l’historique des erreurs et des avertissements signalés. Il indique également quels sont les appareils qui signalent le plus grand nombre d’événements, ou plus précisément d’événements d’erreur et d’événements d’avertissement.

![Page 2 du rapport Power BI](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="architecture"></a>Architecture
Toutes les ressources créées sont accessibles dans le portail Azure. Tous les éléments doivent se trouver sous un groupe de ressources.

![Affichage dans le portail Azure du groupe de ressources](media/howto-connect-powerbi/azure-deployment.PNG)

Les spécificités de chaque ressource, ainsi que leur utilisation sont décrites ci-dessous.

### <a name="azure-functions"></a>Azure Functions
L’application Azure Functions est déclenchée chaque fois qu’un nouveau fichier est écrit dans le stockage Blob. Les fonctions extraient les champs dans chaque mesure, appareil et fichier de modèle d'appareil, et renseignent plusieurs tables SQL intermédiaires à utiliser par Azure Data Factory.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory se connecte à la base de données SQL en tant que service lié. Il exécute des activités de procédure stockée qui traitent les données et les stockent dans les tables d’analyse.

### <a name="azure-sql-database"></a>Azure SQL Database
Ces tables sont automatiquement créées pour renseigner les rapports par défaut. Explorez ces schémas dans Power BI et créez vos propres visualisations sur ces données.

| Nom de la table |
|------------|
|[analytics].[Measurements]|
|[analytics].[Messages]|
|[stage].[Measurements]|
|[analytics].[Properties]|
|[analytics].[PropertyDefinitions]|
|[analytics].[MeasurementDefinitions]|
|[analytics].[Devices]|
|[analytics].[DeviceTemplates]|
|[dbo].[date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>Coûts estimés

Voici une estimation des coûts Azure (Azure Function, Data Factory, Azure SQL) impliqués. Tous les prix s’entendent en USD. Gardez à l'esprit que les prix varient selon la région et qu'il vous faut consulter les prix les plus récents des services individuels pour obtenir les prix réels.
Les valeurs par défaut suivantes sont automatiquement définies dans le modèle (vous pouvez les modifier au terme de la configuration) :

- Azure Functions : Plan App Service S1, 74,40$/mois
- Azure SQL S1, ~ 30$/mois

Nous vous conseillons de vous familiariser avec les diverses options de tarification et de procéder à des ajustements selon vos besoins.

## <a name="resources"></a>Ressources

Rendez-vous sur AppSource pour obtenir la [solution Power BI pour Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à visualiser vos données dans Power BI, nous vous recommandons de passer à l’étape suivante :

> [!div class="nextstepaction"]
> [Guide pratique pour gérer les appareils](howto-manage-devices.md)