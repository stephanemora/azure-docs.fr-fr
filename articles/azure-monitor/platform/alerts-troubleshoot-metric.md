---
title: Résolution des problèmes liés aux alertes de métrique Azure
description: Problèmes couramment rencontrés avec les alertes de métrique Azure Monitor et solutions possibles
author: harelbr
ms.author: harelbr
ms.topic: reference
ms.date: 04/28/2020
ms.subservice: alerts
ms.openlocfilehash: 605d1f550335417a26340b6ee54736321ad69f80
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84302661"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Résolution des problèmes liés aux alertes de métrique dans Azure Monitor 

Cet article décrit les problèmes couramment rencontrés avec les alertes de métrique dans [Azure Monitor](alerts-metric-overview.md) et explique comment y remédier.

Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes avant que les utilisateurs de votre système ne les remarquent. Pour plus d’informations sur les alertes, consultez [Vue d’ensemble des alertes dans Microsoft Azure](alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>Une alerte de métrique ne s'est pas déclenchée alors qu'elle aurait dû 

Si vous pensez qu'une alerte de métrique aurait dû se déclencher mais que cela n'a pas été le cas et qu'elle est introuvable sur le portail Azure, procédez comme suit :

1. **Configuration** - Vérifiez la configuration de la règle d'alerte de métrique pour vous assurer qu'elle est correcte :
    - Vérifiez que les valeurs spécifiées dans les champs **Type d'agrégation**, **Granularité d'agrégation (période)** ainsi que **Valeur de seuil** ou **Sensibilité** sont configurées comme prévu.
    - Pour une règle d'alerte qui utilise des seuils dynamiques, vérifiez si certains paramètres avancés sont configurés, car l'option **Nombre de violations** peut filtrer les alertes et l'option **Ignorer les données avant** peut impacter le calcul des seuils.

       > [!NOTE] 
       > Les seuils dynamiques nécessitent au moins 3 jours et 30 échantillons de métriques avant de devenir actifs.

2. **Déclenchée mais pas de notification** - Consultez la [liste des alertes déclenchées](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) pour essayer de localiser l'alerte déclenchée. Si l'alerte figure dans la liste, mais que vous rencontrez des problèmes avec certaines de ses actions ou notifications, cliquez [ici](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-troubleshoot#action-or-notification-on-my-alert-did-not-work-as-expected) pour en savoir plus.

3. **Déjà active** - Vérifiez si une alerte a déjà été déclenchée sur la série chronologique de métrique pour laquelle vous vous attendiez à recevoir une alerte. Un état est attribué aux alertes de métrique. Autrement dit, une fois qu'une alerte est déclenchée sur une série chronologique de métrique spécifique, aucune alerte supplémentaire n'est déclenchée sur cette série chronologique, même si le problème persiste. Ce choix de conception réduit le bruit. L'alerte est automatiquement résolue lorsque la condition d'alerte n'est pas remplie au bout de trois évaluations consécutives.

4. **Dimensions utilisées** - Si vous avez sélectionné des [valeurs de dimension pour une métrique](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions), la règle d'alerte supervise chaque série chronologique de métrique (comme défini par la combinaison de valeurs de dimension) pour détecter un dépassement de seuil. Pour superviser également la série chronologique de métrique agrégée (sans aucune dimension sélectionnée), configurez une règle d'alerte supplémentaire sur la métrique sans sélectionner de dimensions.

5. **Agrégation et granularité temporelle** - Si vous visualisez la métrique à l'aide de [graphiques de métriques](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), assurez-vous que :
    * La valeur d'**Agrégation** sélectionnée dans le graphique de métriques est identique à la valeur de **Type d'agrégation** de votre règle d'alerte
    * La valeur de **Granularité temporelle** sélectionnée est identique à la valeur de **Granularité d'agrégation (période)** de votre règle d'alerte (et qu'elle n'est pas définie sur « Automatique »)

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Alerte de métrique déclenchée à tort

Si vous pensez que votre alerte de métrique a été déclenchée à tort, la procédure ci-dessous peut vous aider à résoudre le problème.

1. Passez en revue la [liste des alertes déclenchées](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) pour localiser l'alerte déclenchée, puis cliquez pour afficher les détails correspondants. Consultez les informations fournies sous **Pourquoi cette alerte a-t-elle été déclenchée ?** pour voir le graphique des métriques, la **Valeur métrique** et la **Valeur seuil** au moment où l'alerte a été déclenchée.

    > [!NOTE] 
    > Si vous utilisez un type de condition Seuils dynamiques et pensez que les seuils utilisés ne sont pas corrects, envoyez un smiley mécontent en guise de commentaire. Ce commentaire aura un impact sur la recherche algorithmique de l'apprentissage automatique et contribuera à améliorer les détections futures.

2. Si vous avez sélectionné plusieurs valeurs de dimension pour une métrique, l'alerte sera déclenchée lorsqu'**une** métrique de la série chronologique (comme défini par la combinaison de valeurs de dimension) dépassera le seuil. Pour plus d’informations sur l’utilisation des dimensions dans les alertes de métrique, consultez [cette page](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions).

3. Vérifiez la configuration de la règle d'alerte pour vous assurer qu'elle est correcte :
    - Vérifiez que les valeurs spécifiées dans les champs **Type d'agrégation**, **Granularité d'agrégation (période)** ainsi que **Valeur de seuil** ou **Sensibilité** sont configurées comme prévu.
    - Pour une règle d'alerte qui utilise des seuils dynamiques, vérifiez si certains paramètres avancés sont configurés, car l'option **Nombre de violations** peut filtrer les alertes et l'option **Ignorer les données avant** peut impacter le calcul des seuils.

   > [!NOTE]
   > Les seuils dynamiques nécessitent au moins 3 jours et 30 échantillons de métriques avant de devenir actifs.

4. Si vous visualisez la métrique à l'aide d'un [graphique de métriques](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics), assurez-vous que :
    - La valeur d'**Agrégation** sélectionnée dans le graphique de métriques est identique à la valeur de **Type d'agrégation** de votre règle d'alerte
    - La valeur de **Granularité temporelle** sélectionnée est identique à la valeur de **Granularité d'agrégation (période)** de votre règle d'alerte (et qu'elle n'est pas définie sur « Automatique »)

5. Si la règle d'alerte a été déclenchée alors que d'autres alertes ont déjà été déclenchées pour superviser les mêmes critères (qui ne sont pas résolus), vérifiez si la règle d'alerte a été configurée avec la propriété *autoMitigate* définie sur **false** (cette propriété ne peut être configurée que par le biais de REST/PowerShell/CLI ; par conséquent, vérifiez le script utilisé pour déployer la règle d'alerte). Dans ce cas, la règle d'alerte ne résout pas automatiquement les alertes déclenchées, et n'exige pas la résolution d'une alerte déclenchée avant un nouveau déclenchement.


## <a name="cant-find-metric-to-alert-on---virtual-machines"></a>Métrique introuvable pour déclencher l'alerte - machines virtuelles 

Pour déclencher des alertes liées à des métriques invitées sur des machines virtuelles (pour la mémoire ou l'espace disque), veillez à ce que les paramètres de diagnostic soient configurés de manière à envoyer des données à un récepteur Azure Monitor :
    * [Pour les machines virtuelles Windows](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm)
    * [Pour les machines virtuelles Linux](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)
    
> [!NOTE] 
> Si vous avez configuré les métriques invitées pour qu'elles soient envoyées à un espace de travail Log Analytics, ces métriques apparaissent sous la ressource de l'espace de travail Log Analytics. Elles commencent à afficher les données **uniquement** après la création d'une règle d'alerte qui les supervise. Pour ce faire, suivez les étapes permettant de [configurer une alerte de métrique pour les journaux](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#configuring-metric-alert-for-logs).

## <a name="cant-find-the-metric-to-alert-on"></a>Métrique introuvable pour déclencher l'alerte

Si vous souhaitez déclencher une alerte sur une métrique spécifique mais ne voyez aucune métrique pour la ressource, [vérifiez si le type de ressource est pris en charge pour les alertes de métrique](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-near-real-time).
Si des métriques existent pour la ressource mais qu'une métrique spécifique est introuvable, [vérifiez si cette métrique est disponible](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), et si oui, consultez sa description pour savoir si elle est uniquement disponible dans des versions ou éditions spécifiques de la ressource.

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Dimension de métrique introuvable pour déclencher l'alerte

Si vous souhaitez déclencher des alertes liées aux [valeurs de dimension spécifiques d'une métrique](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions), mais que vous ne trouvez pas ces valeurs, notez ce qui suit :

1. L’affichage des valeurs de dimension sous la liste **Valeurs de dimension** peut prendre quelques minutes.
1. Les valeurs de dimension affichées sont basées sur les données de métriques collectées au cours des trois derniers jours
1. Si la valeur de dimension n'est pas encore émise, cliquez sur le signe « + » pour ajouter une valeur personnalisée
1. Si vous souhaitez déclencher une alerte sur l'ensemble des valeurs possibles d'une dimension (y compris les valeurs futures), cochez la case « Sélectionner * »

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Règles d'alerte de métrique toujours définies sur une ressource supprimée 

Lors de la suppression d’une ressource Azure, les règles d’alerte de métrique associées ne sont pas supprimées automatiquement. Pour supprimer les règles d'alerte associées à une ressource qui a été supprimée :

1. Ouvrez le groupe de ressources dans lequel la ressource supprimée a été définie.
1. Dans la liste affichant les ressources, cochez la case **Afficher les types masqués**.
1. Filtrez la liste par Type == **microsoft.insights/metricalerts**.
1. Sélectionnez les règles d'alerte appropriées, puis **Supprimer**.

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Générer des alertes de métrique chaque fois que ma condition est remplie

Par défaut, un état est attribué aux alertes de métrique. Par conséquent, aucune alerte supplémentaire n'est déclenchée si une alerte est déjà déclenchée sur une série chronologique donnée. Si vous souhaitez créer une règle d'alerte de métrique spécifique sans état et être alerté chaque fois qu'une évaluation établit que la condition d'alerte est remplie, créez la règle d'alerte par programmation (par exemple via [Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates), [PowerShell](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1), [REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate), [CLI](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)) et définissez la propriété *autoMitigate* sur « False ».

> [!NOTE] 
> La création d'une règle d'alerte de métrique sans état empêche la résolution des alertes déclenchées. Par conséquent, même si la condition n'est plus remplie, les alertes restent déclenchées jusqu'à la fin de la période de rétention de 30 jours.


## <a name="metric-alert-rules-quota-too-small"></a>Quota de règles d'alerte de métrique trop bas

Le nombre autorisé de règles d'alerte de métrique par abonnement est soumis à des [limites de quota](https://docs.microsoft.com/azure/azure-monitor/service-limits).

Si vous avez atteint la limite de quota, les étapes suivantes peuvent vous aider à résoudre le problème :
1. Essayez de supprimer ou de désactiver les règles d'alerte de métrique qui ne sont plus utilisées.

2. Utilisez plutôt des règles d’alerte de métrique qui supervisent plusieurs ressources. Avec cette fonctionnalité, une même règle d'alerte peut superviser plusieurs ressources en utilisant une seule règle d'alerte comptabilisée dans le quota. Pour plus d'informations sur cette fonctionnalité et sur les types de ressources pris en charge, consultez [cette page](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

3. Si vous avez besoin d'augmenter la limite de quota, ouvrez une demande de support et fournissez les informations suivantes :

    - ID d'abonnement pour lesquels la limite de quota doit être relevée
    - Type de ressource pour l’augmentation de quota : **Alertes de métrique** ou **Alertes de métrique (classiques)**
    - Limite de quota demandée

## <a name="check-total-number-of-metric-alert-rules"></a>Vérifier le nombre total de règles d'alerte de métrique

Pour vérifier l'utilisation actuelle des règles d'alerte de métrique, procédez comme suit.

### <a name="from-the-azure-portal"></a>À partir du portail Azure

1. Ouvrez l’écran **Alertes**, puis cliquez sur **Gérer les règles d’alerte**.
2. Filtrez sur l'abonnement approprié à l'aide du contrôle de liste déroulante **Abonnement**.
3. Veillez à ne PAS filtrer sur un groupe de ressources, un type de ressource ou une ressource spécifique.
4. Dans le contrôle de liste déroulante **Type de signal**, sélectionnez **Métriques**.
5. Vérifiez que le contrôle de liste déroulante **État** est défini sur la valeur **Activé**.
6. Le nombre total de règles d'alerte de métrique est affiché au-dessus de la liste des règles.

### <a name="from-api"></a>À partir de l’API

- PowerShell - [Get-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- API REST - [Répertorier par abonnement](https://docs.microsoft.com/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI - [az monitor metrics alert list](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Gérer les règles d'alerte à l'aide de modèles Resource Manager, de l'API REST, de PowerShell ou d'Azure CLI

Si vous rencontrez des problèmes lors de la création, de la mise à jour, de la récupération ou de la suppression d'alertes de métrique avec des modèles Resource Manager, l'API REST, PowerShell ou l'interface de ligne de commande (CLI) Azure, les étapes suivantes peuvent vous aider à les résoudre.

### <a name="resource-manager-templates"></a>Modèles Resource Manager

- Consultez la liste des [erreurs courantes de déploiement Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) et procédez aux mesures correctives en conséquence
- Consultez les [exemples de modèles Azure Resource Manager d'alertes de métrique](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates) pour vous assurer que vous transmettez correctement tous les paramètres

### <a name="rest-api"></a>API REST

Consulter le [guide de l’API REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/) pour vérifier que vous passez tous les paramètres correctement

### <a name="powershell"></a>PowerShell

Veillez à utiliser les cmdlets PowerShell appropriées pour les alertes de métrique :

- Les applets de commande PowerShell pour les alertes de métrique sont disponibles dans le [module Az.Monitor](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1).
- Veillez à utiliser les cmdlets qui se terminent par « V2 » pour les nouvelles alertes de métrique (non classiques). Par exemple : [Add-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/Add-AzMetricAlertRuleV2?view=azps-3.6.1).

### <a name="azure-cli"></a>Azure CLI

Veillez à utiliser les commandes CLI appropriées pour les alertes de métrique :

- Les commandes CLI des alertes de métrique commencent par `az monitor metrics alert`. Consultez la [référence Azure CLI](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest) pour en savoir plus sur la syntaxe.
- Vous pouvez voir un [exemple illustrant l’utilisation de l’interface CLI pour les alertes de métrique](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric#with-azure-cli).
- Pour déclencher une alerte sur une métrique personnalisée, veillez à faire précéder le nom de la métrique par l’espace de noms de métrique approprié : NAMESPACE.METRIC

### <a name="general"></a>Général

- Si vous recevez une `Metric not found`erreur :

   - Pour une métrique de plateforme : vérifiez que vous utilisez le nom de la **métrique** mentionnée sur [la page des métriques prises en charge par Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported) et non le **nom d'affichage de la métrique**.

   - Pour une métrique personnalisée : vérifiez que la métrique est déjà émise (vous ne pouvez pas créer de règle d'alerte sur une métrique personnalisée qui n'existe pas encore) et que vous fournissez l'espace de noms de la métrique personnalisée (consultez un exemple de modèle ARM [ici](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)).

- Si vous créez des [alertes de métrique sur les journaux](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs), vérifiez que les dépendances appropriées sont incluses. Consultez [Exemple de modèle](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#resource-template-for-metric-alerts-for-logs).

- Si vous créez une règle d'alerte qui contient plusieurs critères, notez les contraintes suivantes :

   - Vous ne pouvez sélectionner qu’une seule valeur par dimension au sein de chaque critère
   - Vous ne pouvez pas utiliser « \* » comme valeur de dimension
   - Lorsque des métriques configurées dans différents critères prennent en charge la même dimension, une valeur de dimension configurée doit être explicitement définie de la même façon pour toutes ces métriques (consultez un exemple de modèle Resource Manager [ici](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)).


## <a name="no-permissions-to-create-metric-alert-rules"></a>Aucune autorisation pour créer des règles d'alerte de métrique

Pour créer une règle d'alerte de métrique, vous devez disposer des autorisations suivantes :

- Autorisation de lecture sur la ressource cible de la règle d'alerte
- Autorisation d'écriture sur le groupe de ressources dans lequel la règle d'alerte est créée (si vous créez la règle d'alerte à partir du portail Azure, elle est créée dans le groupe de ressources dans lequel réside la ressource cible)
- Autorisation de lecture sur tout groupe d'actions associé à la règle d'alerte (le cas échéant)

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations de dépannage générales sur les alertes et les notifications, consultez [Résolution des problèmes relatifs aux alertes Azure Monitor](alerts-troubleshoot.md).
