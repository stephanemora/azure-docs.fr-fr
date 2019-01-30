---
title: Visualiser des données Azure IoT Central dans un tableau de bord Power BI | Microsoft Docs
description: Utilisez le modèle de solution Power BI Azure IoT Central Analytics pour visualiser et analyser vos données IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: a37eb68d135d156c25c58bf91489d5b50ac2c170
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54423118"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Visualiser et analyser des données Azure IoT Central dans un tableau de bord Power BI

*Cette rubrique s’applique aux administrateurs.*

![Pipeline du modèle de solution Power BI](media/howto-connect-powerbi/iot-continuous-data-export.png)

Utilisez le modèle de solution Power BI Azure IoT Central Analytics pour créer un tableau de bord Power BI puissant qui effectue le monitoring des performances de vos appareils IoT. Ce tableau de bord Power BI permet :
- d’effectuer le suivi de la quantité de données envoyées par les appareils au fil du temps ;
- de comparer le volume de données entre la télémétrie, les états et les événements ;
- d’identifier les appareils qui signalent un grand nombre de mesures ;
- d’observer des tendances historiques dans les mesures des appareils ;
- d’identifier les appareils problématiques qui envoient un grand nombre d’événements critiques.

Ce modèle de solution configure le pipeline qui prend les données du compte Stockage Blob Azure provenant de l’[exportation continue de données](howto-export-data.md). Ces données transitent par Azure Functions, Azure Data Factory et Azure SQL Database pour leur traitement et leur transformation. La sortie peut être visualisée et analysée dans un rapport Power BI téléchargeable au format PBIX. Toutes ces ressources étant créées dans votre abonnement Azure, vous pouvez personnaliser chaque composant en fonction de vos besoins. Ce modèle de solution est entièrement open source. Pour en savoir plus sur l’architecture et étendre la solution, visitez le [dépôt GitHub](https://aka.ms/iotcentralgithubpowerbisolutiontemplate).

## <a name="get-the-azure-iot-central-analytics-solution-templatehttpsbpsolutiontemplatescomnamemicrosoft-iotcontinuousdataexporttemplate-from-microsoft-appsource"></a>Obtenez le [modèle de solution Azure IoT Central Analytics](https://bpsolutiontemplates.com/?name=Microsoft-IoTContinuousDataExportTemplate) sur Microsoft AppSource.

## <a name="prerequisites"></a>Prérequis
Voici les prérequis pour configurer le modèle :
- l’accès à un abonnement Azure ;
- des données exportées de votre application IoT Central avec [l’exportation continue de données](howto-export-data.md) (il est recommandé d’activer les mesures, les appareils et les flux de modèles d’appareils pour tirer le meilleur parti possible du tableau de bord Power BI) ;
- Power BI Desktop (dernière version) ;
- Power BI Pro (si vous souhaitez partager le tableau de bord avec d’autres utilisateurs).

## <a name="reports"></a>Rapports

Deux rapports sont générés automatiquement. 

Le premier rapport montre l’historique des mesures signalées par les appareils et décompose les différents types de mesures et les appareils qui ont envoyé le plus grand nombre de mesures.

![Page 1 du rapport Power BI](media/howto-connect-powerbi/template-page1-hasdata.PNG)

Le deuxième rapport explore plus en détail les événements et présente l’historique des erreurs et des avertissements signalés. Il indique également quels sont les appareils qui signalent le plus grand nombre d’événements, ou plus précisément d’événements d’erreur et d’événements d’avertissement.

![Page 2 du rapport Power BI](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="resources"></a>Ressources

Rendez-vous sur AppSource pour obtenir le [modèle de solution Azure IoT Central Analytics](https://bpsolutiontemplates.com/?name=Microsoft-IoTContinuousDataExportTemplate).

Visitez le [dépôt GitHub](https://aka.ms/iotcentralgithubpowerbisolutiontemplate) pour en savoir plus sur l’architecture et étendre la solution.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à visualiser vos données dans Power BI, nous vous recommandons de passer à l’étape suivante :

> [!div class="nextstepaction"]
> [Guide pratique pour gérer les appareils](howto-manage-devices.md)
