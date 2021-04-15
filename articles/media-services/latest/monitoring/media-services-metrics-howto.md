---
title: Afficher les mesures avec Azure Monitor
description: Cet article montre comment surveiller les mesures avec les graphiques du portail Azure et Azure CLI.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7f8aa7e8cd38de37f5d3757804f7836612ad64cb
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227753"
---
# <a name="monitor-media-services-metrics"></a>Surveiller les mesures Media Services

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

[Azure Monitor](/azure/azure-monitor/overview) vous permet de surveiller les métriques et journaux de diagnostic pour vous aider à comprendre le fonctionnement de vos applications. Pour une description détaillée de cette fonctionnalité et pour comprendre pourquoi vous devez utiliser les métriques et les journaux de diagnostic Azure Media Services, consultez [Surveiller les métriques et journaux de diagnostic Media Services](monitor-media-services-data-reference.md).

Azure Monitor offre plusieurs moyens d’interagir avec les mesures, y compris en créant des graphiques dans le portail, en y accédant via l’API REST ou en envoyant des requêtes avec Azure CLI. Cet article montre comment surveiller les mesures avec les graphiques du portail Azure et Azure CLI.

## <a name="prerequisites"></a>Prérequis

- [Créer un compte Media Services](../account-create-how-to.md)
- Évaluation [Surveiller les mesures et les journaux de diagnostic Media Services](monitor-media-services-data-reference.md)

## <a name="view-metrics-in-azure-portal"></a>Voir les mesures depuis le portail Azure

1. Connectez-vous au portail Azure sur https://portal.azure.com.
1. Accédez à votre compte Azure Media Services et sélectionnez **Mesures**.
1. Cliquez sur la zone **Étendue** et sélectionnez la ressource que vous souhaitez surveiller.

    La fenêtre **Sélectionner une étendue** s’affiche sur la droite avec la liste des ressources disponibles pour vous. Dans ce cas, vous voyez :

    * &lt;Nom du compte Media Services&gt;
    * &lt;Nom du compte Media Services&gt;/&lt;nom de point de terminaison de streaming&gt;
    * &lt;Nom du compte de stockage&gt;

    Filtrez, sélectionnez la ressource, puis appuyez sur **Appliquer**. Pour plus d’informations sur les ressources prises en charge et les mesures, consultez [Surveiller les mesures Media Services](monitor-media-services-data-reference.md).

    > [!NOTE]
    > Pour basculer entre les ressources que vous souhaitez surveiller, cliquez à nouveau sur la zone **Source** et répétez cette étape.

1. Facultatif : Nommez votre graphique (modifiez le nom en appuyant sur le crayon en haut).
1. Ajoutez les métriques que vous souhaitez afficher.
1. Vous pouvez épingler votre graphique à votre tableau de bord.

## <a name="view-metrics-with-azure-cli"></a>Afficher les mesures avec Azure CLI

Pour obtenir des mesures de « Sortie » avec Azure CLI, vous devez exécuter la commande `az monitor metrics` suivante :

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Pour obtenir d’autres mesures, remplacez « Sortie » par le nom de la mesure qui vous intéresse.

## <a name="see-also"></a>Voir aussi

- [Mesures Azure Monitor](/azure/azure-monitor/data-platform)
- [Créer, afficher et gérer des alertes de mesure à l’aide d’Azure Monitor](/azure/azure-monitor/alerts/alerts-metric).

## <a name="next-steps"></a>Étapes suivantes

[Journaux de diagnostic](../media-services-diagnostic-logs-howto.md)
