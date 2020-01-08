---
title: Mise à l’échelle automatique dans Azure à l’aide d’une métrique personnalisée
description: Découvrez comment mettre à l’échelle votre ressource par une métrique personnalisée dans Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: f8aaaf8890c3642884b72cc6c8fc2759fec357fa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364541"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Prise en main de la mise à l’échelle automatique par métrique personnalisée dans Azure
Cet article décrit comment mettre à l’échelle votre ressource par une mesure personnalisée dans le portail Azure.

La mise à l’échelle automatique Azure Monitor s’applique uniquement aux [groupes de machines virtuelles identiques](https://azure.microsoft.com/services/virtual-machine-scale-sets/), aux [services cloud](https://azure.microsoft.com/services/cloud-services/), à [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/) et aux [services de gestion des API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Prise en main
Cet article suppose que vous disposez d’une application web avec Application Insights déjà configuré. Si vous n’en avez pas encore, vous pouvez [configurer Application Insights pour votre site web ASP.NET][1].

- Ouvrez le [portail Azure][2].
- Cliquez sur l’icône Azure Monitor dans le volet de navigation gauche.
  ![Lancer Azure Monitor][3]
- Cliquez sur le paramètre de mise à l’échelle automatique pour afficher toutes les ressources pour lesquelles la mise à l’échelle automatique est applicable, ainsi que son état actuel de mise à l’échelle automatique ![Découvrir la mise à l’échelle automatique dans Azure Monitor][4]
- Ouvrez le panneau « Mise à l’échelle automatique » dans Azure Monitor et sélectionnez une ressource à mettre à l’échelle
  > Remarque : Les étapes ci-dessous utilisent un plan de service d’application associé à une application web qui dispose d’Application Insights configuré.
- Dans le panneau de configuration de mise à l’échelle pour la ressource, notez que le nombre d’instances en cours est 1. Cliquez sur « Activer la mise à l’échelle automatique ».
  ![Paramètre d’échelle pour la nouvelle application web][5]
- Fournissez un nom pour le paramètre de mise à l’échelle, puis cliquez sur « Ajouter une règle ». Notez les options de règle de mise à l’échelle qui s’ouvrent dans un volet contextuel dans la partie droite. Par défaut, l’option de mise à l’échelle du nombre d’instances de 1 est définie si le pourcentage processeur de la ressource dépasse 70 %. Modifiez la source de mesure en haut sur « Application Insights », sélectionnez la ressource Application Insights dans la liste déroulante « Ressource » et sélectionnez les métriques personnalisées sur la base desquelles vous souhaitez mettre à l’échelle.
  ![Mettre à l’échelle par métrique personnalisée][6]
- Comme à l’étape précédente, ajoutez une règle de mise à l’échelle qui réduira le nombre de mises à l’échelle de 1 si la métrique personnalisée est inférieure à un seuil.
  ![Mise à l’échelle en fonction du processeur][7]
- Définissez les limites d’instance. Par exemple, si vous souhaitez mettre à l’échelle entre les 2 et 5 instances en fonction des fluctuations de mesures personnalisées, définissez le minimum sur '2', le maximum sur '5' et la valeur par défaut sur '2'
  > Remarque : S’il existe un problème de lecture des métriques de ressource et que la capacité actuelle est inférieure à la capacité par défaut, pour garantir la disponibilité de la ressource, la mise à l’échelle automatique sera modifiée sur la valeur par défaut. Si la capacité actuelle est déjà supérieure à la capacité par défaut, la mise à l’échelle n’est pas réduite.
- Cliquez sur « Enregistrer »

Félicitations ! Vous avez correctement créé vos valeurs de mise à l’échelle pour mettre à l’échelle automatiquement votre application web sur la base d’une métrique personnalisée.

> Remarque : Les mêmes étapes sont applicables pour commencer avec un rôle de service cloud ou un jeu de mise à l’échelle de machine virtuelle.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

