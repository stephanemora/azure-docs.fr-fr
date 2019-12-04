---
title: Surveillance de la FAQ sur les alertes et les rapports
description: Dans cet article, découvrez les réponses aux questions les plus fréquentes sur les alertes de supervision Sauvegarde Azure et les rapports de Sauvegarde Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 9cf7bf49d29b5faa9811a591b45179fe83c1d483
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172925"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Alerte de supervision de Sauvegarde Azure - Questions fréquentes (FAQ)

Cet article répond aux questions courantes sur l’alerte de supervision d’Azure.

## <a name="configure-azure-backup-reports"></a>Configurer les rapports de la Sauvegarde Azure

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Comment vérifier si les données de rapports ont commencé à arriver dans un compte de stockage ?

Accédez au compte de stockage que vous avez configuré et sélectionnez les conteneurs. Si le conteneur a une entrée insights-logs-azurebackupreport, cela signifie que les données de rapports ont commencé à arriver.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Quelle est la fréquence des Push de données vers un compte de stockage et le pack de contenu de la Sauvegarde Azure dans Power BI ?

  Pour les nouveaux utilisateurs, il faut environ 24 heures pour effectuer une transmission des données de type push vers un compte de stockage. Une fois cette transmission de type push initiale terminée, les données sont actualisées selon la fréquence indiquée dans la figure suivante.

* Les données relatives aux **Travaux**, **Alertes**, **Éléments de sauvegarde**, **Coffres**, **Serveurs protégés** et **Stratégies** sont transmises au compte de stockage client au fur et à mesure qu’elles sont consignées.

* Les données relatives au **Stockage** sont transmises à un compte de stockage client toutes les 24 heures.

    ![Fréquence des Push de données des rapports de la Sauvegarde Azure](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

* Power BI effectue une [actualisation planifiée une fois par jour](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Vous pouvez effectuer une actualisation manuelle des données dans Power BI pour le pack de contenu.

### <a name="how-long-can-i-retain-reports"></a>Combien de temps puis-je conserver les rapports ?

Lorsque vous configurez un compte de stockage, vous pouvez sélectionner une période de rétention pour les données de rapport dans le compte de stockage. Suivez l’étape 6 dans la section [Configurer le compte de stockage pour les rapports](backup-azure-configure-reports.md#configure-storage-account-for-reports). Vous pouvez également [analyser les rapports sous Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) et les enregistrer pour allonger la période de rétention en fonction de vos besoins.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Verrai-je toutes mes données dans des rapports après avoir configuré le compte de stockage ?

 Toutes les données générées après que vous ayez configuré un compte de stockage sont transmises au compte de stockage et sont disponibles dans les rapports. Les travaux En cours ne sont pas transmis pour la création de rapports. Une fois que la tâche se termine ou échoue, elle est envoyée aux rapports.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Si j’ai déjà configuré le compte de stockage pour afficher les rapports, puis-je modifier la configuration afin d’utiliser un autre compte de stockage ?

Oui, vous pouvez modifier la configuration de façon à pointer vers un autre compte de stockage. Utilisez le compte de stockage qui vient d’être configuré pour vous connecter au pack de contenu de la Sauvegarde Azure. En outre, une fois qu’un autre compte de stockage est configuré, les nouvelles données arrivent dans ce compte de stockage. Les données plus anciennes (précédents à votre modification de la configuration) restent dans le compte de stockage précédent.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Puis-je afficher des rapports sur différents coffres et abonnements ?

Oui, vous pouvez configurer le même compte de stockage sur différents coffres pour afficher des rapports multicoffres. Vous pouvez également configurer le même compte de stockage pour des coffres de différents abonnements. Vous pouvez ensuite utiliser ce compte de stockage pour vous connecter au pack de contenu de la Sauvegarde Azure dans Power BI afin d’afficher les rapports. Le compte de stockage sélectionné doit se trouver dans la même région que le coffre Recovery Services.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Combien de temps faut-il pour que l’état du travail de l’agent de sauvegarde Azure apparaisse dans le portail ?

Une quinzaine de minutes peuvent être nécessaires avant que l’état du travail de l’agent de sauvegarde Azure ne s’affiche dans le portail.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>En cas d’échec d’un travail de sauvegarde, au bout de combien de temps l’alerte est-elle déclenchée ?

Une alerte est générée dans les 20 minutes qui suivent l’échec de la sauvegarde Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Est-il possible qu’aucun e-mail ne soit envoyé alors que les notifications sont activées ?

Oui. Dans les cas suivants, les notifications ne sont pas envoyées :

* Si les notifications sont configurées sur une base horaire, et qu’une alerte est déclenchée et résolue dans l’heure
* Si un travail est annulé
* Si un travail de sauvegarde secondaire a échoué, parce que le travail de sauvegarde principal est en cours

## <a name="recovery-services-vault"></a>Coffre Recovery Services

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Combien de temps faut-il pour que l’état du travail de l’agent de sauvegarde Azure apparaisse dans le portail ?

Une quinzaine de minutes peuvent être nécessaires avant que l’état du travail de l’agent de sauvegarde Azure ne s’affiche dans le portail.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>En cas d’échec d’un travail de sauvegarde, au bout de combien de temps l’alerte est-elle déclenchée ?

Une alerte est générée dans les 20 minutes qui suivent l’échec de la sauvegarde Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Est-il possible qu’aucun e-mail ne soit envoyé alors que les notifications sont activées ?

Oui. Dans les cas suivants, les notifications ne sont pas envoyées :

* Si les notifications sont configurées sur une base horaire, et qu’une alerte est déclenchée et résolue dans l’heure
* Si un travail est annulé
* Si un travail de sauvegarde secondaire a échoué, parce que le travail de sauvegarde principal est en cours

## <a name="next-steps"></a>Étapes suivantes

Lire les autres forums aux questions :

* [Questions courantes](backup-azure-vm-backup-faq.md) sur la sauvegarde des machines virtuelles Azure.
* [Questions courantes](backup-azure-file-folder-backup-faq.md) sur l’agent Sauvegarde Azure.
