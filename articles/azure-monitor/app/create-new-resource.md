---
title: Créer une ressource Azure Application Insights | Microsoft Docs
description: Configurez manuellement la surveillance d’Application Insights pour une nouvelle application en direct.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbullwin
ms.openlocfilehash: 9da52e5a9dfa3b55431d66ed3162172226f71a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073292"
---
# <a name="create-an-application-insights-resource"></a>Création d’une ressource Application Insights dans Azure

Azure Application Insights affiche les données relatives à votre application dans une *ressource* Microsoft Azure. La création d’une nouvelle ressource fait, par conséquent, partie de la [configuration d’Application Insights pour surveiller une nouvelle application][start]. Après avoir créé votre nouvelle ressource, vous pouvez obtenir sa clé d’instrumentation et l’utiliser pour configurer le Kit de développement logiciel (SDK) Application Insights. La clé d’instrumentation lie vos données de télémétrie à la ressource.

## <a name="sign-in-to-microsoft-azure"></a>Se connecter à Microsoft Azure

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-an-application-insights-resource"></a>Création d’une ressource Application Insights dans Azure

Connectez-vous au [portail Azure](https://portal.azure.com) et créez une ressource Application Insights :

![Cliquez sur le signe « + » dans le coin supérieur gauche. Sélectionner Outils de développement, puis Application Insights](./media/create-new-resource/new-app-insights.png)

   | Paramètres        |  Valeur           | Description  |
   | ------------- |:-------------|:-----|
   | **Nom**      | Valeur globalement unique | Nom identifiant l’application que vous analysez. |
   | **Groupe de ressources**     | myResourceGroup      | Nom du nouveau groupe de ressources ou existant pour héberger les données Application Insights. |
   | **Lieu** | USA Est | Choisissez un emplacement près de chez vous ou proche de l’endroit où votre application est hébergée. |

Entrez les valeurs appropriées dans les champs obligatoires, puis sélectionnez **Vérifier + créer**.

![Entrez des valeurs dans les champs obligatoires, puis sélectionnez « Vérifier + créer ».](./media/create-new-resource/review-create.png)

Une fois votre application créée, un nouveau volet s’ouvre. Dans ce volet, vous trouverez des données relatives à l’utilisation et aux performances de votre application analysée. 

## <a name="copy-the-instrumentation-key"></a>Copie de la clé d’instrumentation

La clé d’instrumentation identifie la ressource à laquelle vous souhaitez associer vos données de télémétrie. Vous devez copier pour ajouter la clé d’instrumentation au code de votre application.

![Cliquer sur et copier la clé d’instrumentation](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Installation du Kit SDK dans votre application

Installez le Kit SDK Application Insights dans votre application. Cette étape repose en grande partie sur votre type d’application.

La clé d’instrumentation permet de configurer le [kit de développement logiciel (SDK) que vous avez installé dans votre application][start].

Le Kit de développement logiciel (SDK) inclut des modules standard qui envoient des données de télémétrie sans avoir à écrire du code supplémentaire. Pour suivre les actions des utilisateurs ou diagnostiquer des problèmes plus en détail, [utilisez l'API][api] pour envoyer votre propre télémétrie.

## <a name="creating-a-resource-automatically"></a>Création automatique d’une ressource
Vous pouvez écrire un [script PowerShell](../../azure-monitor/app/powershell.md) pour créer automatiquement une ressource.

## <a name="next-steps"></a>Étapes suivantes
* [Recherche de diagnostic](../../azure-monitor/app/diagnostic-search.md)
* [Exploration des mesures](../../azure-monitor/app/metrics-explorer.md)
* [Écriture de requêtes Analytics](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md