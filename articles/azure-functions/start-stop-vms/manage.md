---
title: Gérer Start/Stop VMs v2 (préversion)
description: Cet article explique comment surveiller l’état de vos machines virtuelles Azure gérées par la fonctionnalité Start/Stop VMs v2 (préversion) et effectuer d’autres tâches de gestion.
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: e9d6f7a93f2bd59b120be059d7f9c254d1bde56b
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110073159"
---
# <a name="how-to-manage-startstop-vms-v2-preview"></a>Gestion de Start/Stop VMs v2 (préversion)

## <a name="azure-dashboard"></a>Tableau de bord Azure

Start/Stop VMs v2 (préversion) comprend un [tableau de bord](../../azure-monitor/visualizations.md#azure-dashboards) pour vous aider à comprendre le périmètre de gestion et les opérations récentes sur vos machines virtuelles. Il s’agit d’un moyen simple et rapide de vérifier l’état de chaque opération effectuée sur vos machines virtuelles Azure. La visualisation dans chaque vignette est basée sur une requête de journal. Pour voir la requête, sélectionnez l’option **Ouvrir dans le panneau Journaux** dans le coin droit de la vignette. Cela ouvre l’outil [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md#starting-log-analytics) dans le portail Azure, à partir duquel vous pouvez évaluer la requête et la modifier pour répondre à vos besoins, comme des [alertes de journal](../../azure-monitor/alerts/alerts-log.md) personnalisées, un [classeur](../../azure-monitor/visualize/workbooks-overview.md) personnalisé, etc.

Les données de journal de chaque vignette du tableau de bord sont actualisés toutes les heures, avec une option d’actualisation manuelle à la demande en cliquant sur l’icône **Actualiser** d’une visualisation donnée ou en actualisant le tableau de bord complet.

Pour en savoir plus sur l’utilisation d’un tableau de bord basé sur les journaux, consultez le [didacticiel](../../azure-monitor/visualize/tutorial-logs-dashboards.md) suivant.

## <a name="configure-email-notifications"></a>Configurer les notifications par e-mail

Pour modifier les notifications par e-mail une fois la fonctionnalité Start/Stop VMs v2 (préversion) déployée, modifiez le groupe d’actions créé lors du déploiement.

1. Dans le portail Azure, accédez à **Surveiller**, puis **Alertes**. Sélectionnez **Gérer les actions**.

1. Dans la page **Gérer les actions**, sélectionnez le groupe d’actions appelé **StartStopV2_VM_Notication**.

    :::image type="content" source="media/manage/alerts-action-groups.png" alt-text="Capture d’écran montrant la page Groupes d’actions.":::

1. Sur la page **StartStopV2_VM_Notification**, vous pouvez modifier les options de notification E-mail/SMS/Push/Voix. Ajoutez d’autres actions ou mettez à jour votre configuration dans ce groupe d’actions, puis cliquez sur **OK** pour enregistrer vos modifications.

    :::image type="content" source="media/manage/email-notification-type-example.png" alt-text="Capture d’écran montrant la page E-mail/SMS/Push/Voice affichant un exemple d’adresse e-mail mise à jour.":::

    Pour en savoir plus sur les groupes d’actions, consultez [Groupes d’actions](../../azure-monitor/alerts/action-groups.md).

La capture d’écran suivante est un exemple d’e-mail envoyé lorsque la fonctionnalité arrête des machines virtuelles.

:::image type="content" source="media/manage/email-notification-example.png" alt-text="Capture d’écran montrant un exemple de message électronique envoyé lorsque la fonctionnalité arrête des machines virtuelles.":::

## <a name="next-steps"></a>Étapes suivantes

Pour gérer les problèmes lors de la gestion des machines virtuelles, consultez [Résoudre les problèmes liés à Start/Stop VMs v2 (préversion)](troubleshoot.md).