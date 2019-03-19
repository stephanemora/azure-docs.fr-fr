---
title: Exploration des données HockeyApp dans Azure Application Insights | Microsoft Docs
description: Analysez l’utilisation et les performances de votre application Azure avec Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 97783cc6-67d6-465f-9926-cb9821f4176e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/30/2017
ms.author: mbullwin
ms.openlocfilehash: 79adfbfde25903bfe92c94507071c9d0fe303ef1
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448388"
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Exploration des données HockeyApp dans Application Insights

> [!NOTE]
> HockeyApp n’est plus disponible pour les nouvelles applications. Les déploiements HockeyApp existants continueront de fonctionner. Visual Studio App Center est désormais le service recommandé de Microsoft pour surveiller les nouvelles applications mobiles. [Découvrez comment configurer vos applications avec App Center et Application Insights](../../azure-monitor/learn/mobile-center-quickstart.md).

[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) est un service pour la surveillance des applications mobiles et de bureau en production. À partir de HockeyApp, vous pouvez envoyer de la télémétrie de trace et personnalisée pour surveiller l’utilisation et faciliter le diagnostic (en plus de l’obtention de données de panne). Ce flux de données de télémétrie peut être interrogé à l’aide de la puissante fonctionnalité [Analytics](../../azure-monitor/app/analytics.md) [d’Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). En outre, vous pouvez [exporter la télémétrie personnalisée et de trace](export-telemetry.md). Pour activer ces fonctionnalités, vous configurez un pont qui relaie les données personnalisée HockeyApp vers Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>Application de pont HockeyApp
Cette application est la fonctionnalité principale qui vous permet d’accéder à votre télémétrie de trace et personnalisée HockeyApp dans Application Insights via les fonctionnalités d’analyse et d’exportation continue. Les événements de trace et personnalisés collectés par HockeyApp après la création de l’application HockeyApp Bridge seront accessibles à partir de ces fonctionnalités. Nous allons voir comment configurer une de ces applications de pont.

Dans HockeyApp, ouvrez Paramètres de compte, [Jetons d’API](https://rink.hockeyapp.net/manage/auth_tokens). Créez un nouveau jeton ou réutilisez un jeton existant. Les droits minimaux requis sont « lecture seule ». Effectuez une copie du jeton d’API.

![Obtenir un jeton d’API HockeyApp](./media/hockeyapp-bridge-app/01.png)

Ouvrez le portail Microsoft Azure et [créez une ressource Application Insights](../../azure-monitor/app/create-new-resource.md ). Définissez la valeur Type d’application sur « Application de pont HockeyApp » :

![Nouvelle ressource Application Insights](./media/hockeyapp-bridge-app/02.png)

Vous n’avez pas besoin de définir de nom. Celui-ci sera automatiquement configuré à partir du nom de HockeyApp.

Les champs du pont HockeyApp s’affichent. 

![Renseignez les champs de pont](./media/hockeyapp-bridge-app/03.png)

Entrez le jeton HockeyApp que vous avez noté précédemment. Cette action remplit le menu déroulant « Application HockeyApp » avec toutes vos applications HockeyApp. Sélectionnez celle que vous souhaitez utiliser et complétez le reste des champs. 

Ouvrez la nouvelle ressource. 

Notez que les données prennent un certain temps avant de commencer à circuler.

![Ressource Application Insights en attente de données](./media/hockeyapp-bridge-app/04.png)

Et voilà ! Les données de trace et personnalisées collectées dans votre application instrumentée HockeyApp à partir de ce point sont désormais également disponibles dans les fonctionnalités d’analyse et d’exportation continue.

Nous allons brièvement passer en revue chacune de ces fonctionnalités, qui sont désormais à votre disposition.

## <a name="analytics"></a>Analytics
Analytique est un outil puissant pour des requêtes ad hoc de vos données, ce qui vous permet de diagnostiquer et d’analyser vos données de télémétrie et de découvrir rapidement les causes et les modèles.

![Analytics](./media/hockeyapp-bridge-app/05.png)

* [En savoir plus sur Analyse](../../azure-monitor/log-query/get-started-portal.md)

## <a name="continuous-export"></a>Exportation continue
Exportation continue vous permet d’exporter des données dans un conteneur de stockage Blob Azure. Cela est très utile si vous souhaitez conserver vos données plus longtemps que la période de rétention actuellement proposée par Application Insights. Vous pouvez conserver les données dans le stockage Blob, le traiter dans une base de données SQL ou votre solution d’entrepôt de données préférée.

[En savoir plus sur l’exportation continue](export-telemetry.md)

## <a name="next-steps"></a>Étapes suivantes
* [Appliquer Analytics à vos données](../../azure-monitor/log-query/get-started-portal.md)

