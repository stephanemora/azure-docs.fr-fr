---
title: Configurer les diagnostics
titleSuffix: Azure Digital Twins
description: Découvrez commet activer la journalisation avec les paramètres de diagnostic.
author: baanders
ms.author: baanders
ms.date: 7/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 60ee2102a2b725c68e29afec9a44de03e52e9467
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87379589"
---
# <a name="enable-logging-with-diagnostics-settings"></a>Activer la journalisation avec les paramètres de diagnostic

Vous pouvez choisir d’envoyer vos données de métriques à [Log Analytics](../azure-monitor/log-query/get-started-portal.md), à un point de terminaison [Event Hubs](../event-hubs/event-hubs-about.md) ou au service [Stockage Azure](../storage/blobs/storage-blobs-overview.md) en activant la journalisation avec les paramètres de diagnostic pour votre instance.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Activer les paramètres de diagnostic avec le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre instance Azure Digital Twins. Vous pouvez la trouver en tapant son nom dans la barre de recherche du portail. 

2. Sélectionnez **Paramètres de diagnostic** dans le menu, puis **Ajouter un paramètre de diagnostic**.

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="Capture d’écran montrant la page des paramètres de diagnostic et le bouton à ajouter":::

3. Dans la page qui suit, renseignez les valeurs suivantes :
 * **Nom du paramètre de diagnostic** : Nommez les paramètres de diagnostic.
 * **Détails de la catégorie** : choisissez les opérations à surveiller, puis cochez les cases pour activer les diagnostics pour ces opérations. Les opérations sur lesquelles les paramètres de diagnostic peuvent établir des rapports sont :
    - DigitalTwinsOperation
    - EventRoutesOperation
    - ModelsOperation
    - QueryOperation
    - AllMetrics
 * **Détails de la destination** : choisissez où vous voulez envoyer les journaux d’activité. Vous pouvez sélectionner n’importe quelle combinaison des trois options suivantes :
    - Envoyer à Log Analytics
    - Archiver dans un compte de stockage
    - Diffuser vers un hub d’événements

    Il se peut que vous soyez invité à fournir des détails supplémentaires s’ils sont nécessaires pour la sélection de votre destination.  
    
4. Enregistrez les nouveaux paramètres. 

    :::image type="content" source="media/how-to-set-up-diagnostics/diagnostic-settings-details.png" alt-text="Capture d’écran montrant la page des paramètres de diagnostic et le bouton à ajouter":::

Les nouveaux paramètres prennent effet au bout de 10 minutes environ. Après cela, les journaux réapparaissent dans la cible configurée sur la page **Paramètres de diagnostic** de votre instance. 

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la configuration des diagnostics, consultez [*Collecter et utiliser des données de journaux à partir de vos ressources Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Pour plus d’informations sur les métriques d’Azure Digital Twins, consultez [*Guide pratique : Afficher les métriques avec Azure Monitor*](how-to-view-metrics.md)