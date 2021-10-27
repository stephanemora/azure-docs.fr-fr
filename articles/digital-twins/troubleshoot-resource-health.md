---
title: 'Résolution des problèmes : Intégrité des ressources'
titleSuffix: Azure Digital Twins
description: Découvrez comment utiliser Azure Resource Health pour vérifier l’intégrité de votre instance Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 10/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q1
ms.openlocfilehash: 9c816e613fe6f495de9abb57e2ac041960bffdbe
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131445"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Dépannage d’Azure Digital Twins : Intégrité des ressources

[Azure Service Health](../service-health/index.yml) est une suite d’expériences qui peut vous aider à diagnostiquer et à obtenir un support pour les problèmes de service qui affectent vos ressources Azure. Il contient des informations sur l’**intégrité des ressources**, l'**intégrité du service** et l'**état**, ainsi que des rapports sur les informations d’intégrité actuelles et passées.

## <a name="use-azure-resource-health"></a>Utilisation d’Azure Resource Health

[Azure Resource Health](../service-health/resource-health-overview.md) peut vous aider à analyser si votre instance Azure Digital Twins est en cours d’exécution. Vous pouvez également l’utiliser pour savoir si une panne régionale a un impact sur l’intégrité de votre instance.

Pour vérifier l’intégrité de votre instance, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre instance Azure Digital Twins. Vous pouvez la trouver en tapant son nom dans la barre de recherche du portail. 

2. Dans le menu de votre instance, sélectionnez **Intégrité des ressources** sous Support + résolution des problèmes. Cela vous permet d’accéder à la page d’affichage de l’historique d’intégrité des ressources. 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="Capture d’écran montrant la page « Intégrité des ressources ». La section « Historique des états » montre un rapport quotidien des neuf derniers jours.":::

Dans l’image ci-dessus, cette instance est affichée comme étant **Disponible**, et ce depuis neuf jours. Pour en savoir plus sur l’état Disponible et les autres types d’état qui peuvent apparaître, consultez [Vue d’ensemble d’Azure Resource Health](../service-health/resource-health-overview.md).

Vous pouvez également en savoir plus sur les différentes vérifications de l’intégrité des ressources pour différents types de ressources Azure dans [Types de ressources et contrôles d’intégrité dans Azure Resource Health](../service-health/resource-health-checks-resource-types.md).

## <a name="use-azure-service-health"></a>Utiliser Azure Service Health

[Azure Service Health](../service-health/service-health-overview.md) peut vous aider à vérifier l’intégrité de l’ensemble du service Azure Digital Twins dans une certaine région et à connaître les événements tels que les problèmes de service en cours et la maintenance planifiée à venir.

Pour vérifier l’intégrité du service, connectez-vous au [portail Azure](https://portal.azure.com) et accédez au service **Service Health**. Vous pouvez le trouver en tapant « service health » dans la barre de recherche du portail. 

Vous pouvez ensuite filtrer les problèmes de service par abonnement, région et service.

Pour plus d’informations sur l’utilisation de Azure Service Health, consultez [Vue d’ensemble Service Health](../service-health/service-health-overview.md).

## <a name="use-azure-status"></a>Utilisez l’état d’Azure

Le page [état d’Azure](../service-health/azure-status-overview.md) vous présente une vue globale de l'intégrité des régions et des services Azure. Alors qu’Azure Service Health et Azure Resource Health sont personnalisés en fonction de votre ressource spécifique, l’état d’Azure a une plus grande portée et peut être utile pour comprendre les incidents ayant un large impact.

Pour vérifier l’état d’Azure, accédez à la page [État d’Azure](https://status.azure.com/status/). La page affiche un tableau des services Azure, ainsi que des indicateurs d’intégrité par région. Vous pouvez afficher Azure Digital Twins en recherchant son entrée de table sur la page.

Pour plus d’informations sur l’utilisation de la page d’état d’Azure, consultez [vue d’ensemble de l’état d’Azure](../service-health/azure-status-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez d’autres façons d’analyser votre instance Azure Digital Twins dans les articles suivants :
* [Résolution des problèmes : Métriques](troubleshoot-metrics.md)
* [Résolution des problèmes : Journaux de diagnostic](troubleshoot-diagnostics.md)
* [Résolution des problèmes : Alertes](troubleshoot-alerts.md)
