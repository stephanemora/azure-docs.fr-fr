---
title: Mise à niveau de la détection intelligente Azure Monitor Application Insights vers les alertes (préversion) | Microsoft Docs
description: En savoir plus sur les étapes requises pour mettre à niveau votre détection intelligente Azure Monitor Application Insights vers des règles d’alerte
ms.topic: conceptual
ms.date: 05/30/2021
ms.openlocfilehash: d0995c05af707c5a4fb94305ca1211e38ca59e3a
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113587478"
---
# <a name="migrate-azure-monitor-application-insights-smart-detection-to-alerts-preview"></a>Migration de la détection intelligente Azure Monitor Application Insights vers les alertes (préversion)

Cet article décrit le processus de migration de la détection intelligente Application Insights vers les alertes. La migration crée des règles d’alerte pour les différents modules de détection intelligente. Vous pouvez gérer et configurer ces règles comme n’importe quelle autre règle d’alerte d’Azure Monitor. Vous pouvez également configurer des groupes d’actions pour ces règles, ce qui offre plusieurs moyens de prendre des mesures ou de déclencher des notifications en cas de nouvelle détection.

## <a name="benefits-of-migration-to-alerts"></a>Avantages de la migration vers les alertes

Avec la migration, la détection intelligente vous permet désormais de tirer parti des fonctionnalités complètes des alertes Azure Monitor, notamment :

- **Options de notification riches pour tous les détecteurs** - Les [groupes d’actions](../alerts/action-groups.md) vous permettent de configurer plusieurs types de notifications et d’actions déclenchées lors du déclenchement d’une alerte. Vous pouvez configurer des notifications par courrier électronique, SMS, appel vocal ou notifications push, ainsi que des actions telles que l’appel d’un webhook sécurisé, une application logique, un runbook d’automatisation, et bien plus encore. L’action regroupe la gestion à grande échelle en vous permettant de configurer des actions une seule fois et de les utiliser sur plusieurs règles d’alerte.
- **Gestion à grande échelle** des alertes de détection intelligente à l’aide de l’API et de l’expérience d’alertes Azure Monitor.
- **Suppression basée sur les règles des notifications** - Les [règles d’action](../alerts/alerts-action-rules.md) vous permettent de définir ou de supprimer des actions à tout niveau d’Azure Resource Manager (abonnement Azure, groupe de ressources ou ressource cible). Elles disposent de différents filtres vous permettant de limiter le sous-ensemble des instances d’alerte sur lesquelles vous souhaitez agir. 

## <a name="migrated-smart-detection-capabilities"></a>Fonctionnalités de détection intelligente migrées

Un nouvel ensemble de règles d’alerte est créé lors de la migration d’une ressource Application Insights.  Une règle est créée pour chacune des fonctionnalités de détection intelligente migrées. Le tableau suivant mappe les fonctionnalités de détection intelligente pré-migration aux règles d’alerte post-migration :

| Nom de la règle de détection intelligente <sup>(1)</sup>          | Nom de la règle d’alerte <sup>(2)</sup> |
| -------------------------                          | ------------------------------ |
| Dégradation du temps de réponse du serveur                | Dégradation de la latence de réponse - *\<Application Insights resource name\>*  |
| Dégradation de la durée de dépendance                 | Dégradation de la latence de dépendance- *\<Application Insights resource name\>*|
| Dégradation du rapport entre les niveaux de gravité des suivis (préversion)      | Dégradation de la gravité de la trace- *\<Application Insights resource name\>*|
| Élévation anormale du volume des exceptions (préversion)        | Anomalies d’exception- *\<Application Insights resource name\>*|
| Fuite de mémoire potentielle détectée (préversion)           | Fuites de mémoire potentielles - *\<Application Insights resource name\>*|
| Durée de chargement de page lente                                | *abandonné* <sup>(3)</sup>  |
| Temps de réponse du serveur lent                          | *abandonné* <sup>(3)</sup>  |
| Durée de dépendance longue                           | *abandonné* <sup>(3)</sup>  |
| Problème de sécurité potentiel détecté (préversion)        | *abandonné* <sup>(3)</sup>  |
| Élévation anormale du volume de données quotidien (préversion)       | *abandonné* <sup>(3)</sup>  |

<sup>(1)</sup> Nom de règle tel qu’il apparaît dans le panneau Paramètres de détection intelligente  
<sup>(2)</sup> Nom de la nouvelle règle d’alerte après la migration  
<sup>(3)</sup> Ces fonctionnalités de détection intelligente ne sont pas converties en alertes, en raison de la faible utilisation et de la réévaluation de l’efficacité de la détection. Ces détecteurs ne seront plus pris en charge pour cette ressource une fois sa migration terminée. 

La migration ne modifie pas la conception algorithmique et le comportement de la détection intelligente. Les mêmes performances de détection sont attendues avant et après la modification.

Vous devez appliquer la migration à chaque ressource Application Insights séparément. Pour les ressources qui ne sont pas explicitement migrées, la détection intelligente continue de fonctionner comme avant.

### <a name="action-group-configuration-for-the-new-smart-detection-alert-rules"></a>Configuration du groupe d’actions pour les nouvelles règles d’alerte de détection intelligente

Dans le cadre de la migration, chaque nouvelle règle d’alerte est automatiquement configurée avec un groupe d’actions. La migration peut affecter un groupe d’actions par défaut pour chaque règle. Le groupe d’actions par défaut est configuré en fonction de la notification de la règle avant la migration :

- si la **règle de détection intelligente comportait l’e-mail par défaut ou aucune notification n’est configurée**, la nouvelle règle d’alerte est configurée avec un groupe d’actions nommé « Application Insights la détection intelligente ».
    - Si l’outil de migration trouve un groupe d’actions existant portant ce nom, il lie la nouvelle règle d’alerte à ce groupe d’actions.  
    - Dans le cas contraire, il crée un nouveau groupe d’actions portant ce nom. Le nouveau groupe est configuré pour les actions « E-mail rôle Azure Resource Manager » et envoie une notification à vos utilisateurs du contributeur et du lecteur de surveillance Azure Resource Manager.

- si la **notification par courrier électronique par défaut a été modifiée** avant la migration, un groupe d’actions appelé « détection intelligente Application Insights\<n\> » est créé, avec une action de messagerie qui envoie des notifications aux adresses de messagerie précédemment configurées.

Au lieu d’utiliser le groupe d’actions par défaut, vous sélectionnez un groupe d’actions existant qui sera configuré pour toutes les nouvelles règles d’alerte.

## <a name="executing-smart-detection-migration-process"></a>Exécution du processus de migration de la détection intelligente

### <a name="migrate-your-smart-detection-using-the-azure-portal"></a>Migrer votre détection intelligente à l’aide du portail Azure

Appliquez la migration à une ressource Application Insights spécifique à la fois.

Pour migrer la détection intelligente dans votre ressource, procédez comme suit :

1. Sélectionnez **Détection intelligente** sous l’en-tête **Investiguer** dans le menu de gauche de la ressource Application Insights.

2. Cliquez sur la lecture de la bannière **«migrer la détection intelligente vers les alertes (préversion)** . La boîte de dialogue de migration s’ouvre.

   ![Bannière de flux de détection intelligente](media/alerts-smart-detections-migration/smart-detection-feed-banner.png)

3. Sélectionnez un groupe d’actions à configurer pour les nouvelles règles d’alerte. Vous pouvez choisir entre utiliser le groupe d’actions par défaut (comme expliqué ci-dessus) ou l’un de vos groupes d’actions existants.

4. Sélectionnez **Migrer** pour commencer le processus de migration.

   ![Boîte de dialogue de migration de détection intelligente](media/alerts-smart-detections-migration/smart-detection-migration-dialog.png)

Après la migration, de nouvelles règles d’alerte sont créées pour votre ressource Application Insight, comme expliqué ci-dessus.

### <a name="migrate-your-smart-detection-using-azure-cli"></a>Migrer votre détection intelligente à l’aide d’Azure CLI

Vous pouvez démarrer la migration de la détection intelligente à l’aide de la commande Azure CLI suivante. La commande déclenche le processus de migration préconfiguré comme décrit précédemment.

```azurecli
az rest --method POST --uri /subscriptions/{subscriptionId}/providers/Microsoft.AlertsManagement/migrateFromSmartDetection?api-version=2021-01-01-preview --body @body.txt
```

Où body.txt doit inclure :

```json
{
      "scope": [
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName} /providers/microsoft.insights/components/{resourceName} "
      ],
      "actionGroupCreationPolicy" : "{Auto/Custom}",
      "customActionGroupName" : "{actionGroupName}"           
}
```

**ActionGroupCreationPolicy** sélectionne la stratégie de migration des paramètres de messagerie dans les règles de détection intelligente dans les groupes d’actions. Les valeurs autorisées sont les suivantes :

- **'Auto'** , qui utilise les groupes d’actions par défaut comme décrit dans ce document.
- **’Custom’** , qui crée toutes les règles d’alerte avec le groupe d’actions spécifié dans **customActionGroupName**.
- *\<blank\>* -Si **ActionGroupCreationPolicy** n’est pas spécifié, la stratégie « Auto » est utilisée.

### <a name="migrate-your-smart-detection-using-azure-resource-manager-templates"></a>Migrer votre détection intelligente à l’aide de modèles Azure Resource Manager

Vous pouvez déclencher la migration de la détection intelligente vers des alertes pour une ressource Application Insights spécifique, à l’aide de modèles Azure Resource Manager. À l’aide de cette méthode, vous devez :

- Créer une règle d’alerte de détection intelligente pour chaque détecteur pris en charge
- Modifier les propriétés d’Application Insight pour indiquer que la migration a été effectuée

Cette méthode vous permet de contrôler les règles d’alerte à créer, de définir le nom et la description de votre propre règle d’alerte et de sélectionner le groupe d’actions souhaité pour chaque règle.

Les modèles suivants doivent être utilisés à cette fin (modifiez-les si nécessaire pour fournir votre ID d’abonnement et le nom de ressource Application Insights).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "applicationInsightsResourceName": {
            "type": "string"
        },
        "actionGroupName": {
            "type": "string",
            "defaultValue": "Application Insights Smart Detection"
        },
        "actionGroupResourceGroup": {
            "type": "string",
            "defaultValue": "[resourceGroup().Name]"
        }
    },
    "variables": {
        "applicationInsightsResourceId": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().Name,'/providers/microsoft.insights/components/',parameters('applicationInsightsResourceName'))]",
        "actionGroupId": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',parameters('actionGroupResourceGroup'),'/providers/microsoft.insights/ActionGroups/',parameters('actionGroupName'))]",
        "requestPerformanceDegradationDetectorRuleName": "[concat('Response Latency Degradation - ', parameters('applicationInsightsResourceName'))]",
        "dependencyPerformanceDegradationDetectorRuleName": "[concat('Dependency Latency Degradation - ', parameters('applicationInsightsResourceName'))]",
        "traceSeverityDetectorRuleName": "[concat('Trace Severity Degradation - ', parameters('applicationInsightsResourceName'))]",
        "exceptionVolumeChangedDetectorRuleName": "[concat('Exception Anomalies - ', parameters('applicationInsightsResourceName'))]",
        "memoryLeakRuleName": "[concat('Potential Memory Leak - ', parameters('applicationInsightsResourceName'))]"
    },
    "resources": [
        {
            "name": "[variables('requestPerformanceDegradationDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Response Latency Degradation notifies you of an unusual increase in latency in your app response to requests.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "RequestPerformanceDegradationDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {   
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('dependencyPerformanceDegradationDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Dependency Latency Degradation notifies you of an unusual increase in response by a dependency your app is calling (e.g. REST API or database)",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "DependencyPerformanceDegradationDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('traceSeverityDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Trace Severity Degradation notifies you of an unusual increase in the severity of the traces generated by your app.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "TraceSeverityDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('exceptionVolumeChangedDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Exception Anomalies notifies you of an unusual rise in the rate of exceptions thrown by your app.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "ExceptionVolumeChangedDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('memoryLeakRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Potential Memory Leak notifies you of increased memory consumption pattern by your app which may indicate a potential memory leak.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "MemoryLeakDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[concat(parameters('applicationInsightsResourceName'),'/migrationToAlertRulesCompleted')]",
            "type": "Microsoft.Insights/components/ProactiveDetectionConfigs",
            "location": "[resourceGroup().location]",
            "apiVersion": "2018-05-01-preview",
            "properties": {
                "name": "migrationToAlertRulesCompleted",
                "sendEmailsToSubscriptionOwners": false,
                "customEmails": [],
                "enabled": true
            },
            "dependsOn": [
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('requestPerformanceDegradationDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('dependencyPerformanceDegradationDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('traceSeverityDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('exceptionVolumeChangedDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('memoryLeakRuleName'))]"
            ]
        }
    ]
}
```

## <a name="viewing-your-alerts-after-the-migration"></a>Affichage de vos alertes après la migration

Après le processus de migration, vous pouvez afficher vos alertes de détection intelligente en sélectionnant l’entrée Alertes dans le menu de gauche de votre ressource Application Insights. Définissez **Type de signal** sur **Détecteur intelligent** pour filtrer et présenter uniquement les alertes de détection intelligente. Vous pouvez sélectionner une alerte pour afficher ses détails de détection.

![Alertes de détection intelligente](media/alerts-smart-detections-migration/smart-detector-alerts.png)

Vous pouvez également toujours voir les détections disponibles dans le flux de détection intelligente de votre ressource Application Insights.

![Flux de détection intelligente](media/alerts-smart-detections-migration/smart-detection-feed.png)

## <a name="managing-smart-detection-alert-rules-settings-after-the-migration"></a>Gestion des paramètres des règles d’alerte de détection intelligente après la migration

### <a name="managing-alert-rules-settings-using-the-azure-portal"></a>Gestion des paramètres des règles d’alerte à l’aide du portail Azure

Une fois la migration terminée, vous accédez aux nouvelles règles d’alerte de détection intelligente de la même façon que les autres règles d’alerte définies pour la ressource :

1. Sélectionnez **Alertes** sous l’en-tête **Surveillance** dans le menu de gauche de votre ressource Application Insights.

   ![Menu Alertes](media/alerts-smart-detections-migration/application-insights-alerts.png)

2. Cliquez sur **Gérer les règles d’alerte**.

   ![Gérer les règles d’alerte](media/alerts-smart-detections-migration/manage-alert-rules.png)

3. Définissez **Type de signal** sur **Détecteur intelligent** pour filtrer et présenter les règles d’alerte de détection intelligente.

   ![Règles de détecteur intelligent](media/alerts-smart-detections-migration/smart-detector-rules.png)

### <a name="enabling--disabling-smart-detection-alert-rules"></a>Activation/désactivation des règles d’alerte de détection intelligente 

Les règles d’alerte de détection intelligente peuvent être activées ou désactivées par le biais de l’interface utilisateur du portail ou par programme, comme n’importe quelle autre règle d’alerte.

Si une règle de détection intelligente spécifique a été désactivée avant la migration, la nouvelle règle d’alerte sera également désactivée.

### <a name="configuring-action-group-for-your-alert-rules"></a>Configuration du groupe d’actions pour vos règles d’alerte

Vous pouvez créer et gérer des groupes d’actions pour les nouvelles règles d’alerte de détection intelligente, comme pour toute autre règle d’alerte Azure Monitor. 

### <a name="managing-alert-rule-settings-using-azure-resource-manager-templates"></a>Gestion des paramètres des règles d’alerte à l’aide de modèles Azure Resource Manager

Une fois la migration terminée, vous pouvez utiliser des modèles Azure Resource Manager pour configurer les paramètres des règles d’alerte de détection intelligente.

> [!NOTE]
> Une fois la migration terminée, les paramètres de détection intelligente doivent être configurés à l’aide de modèles de règles d’alerte de détection intelligente et ne peuvent plus être configurés à l’aide du [modèle Application Insights Resource Manager](../app/proactive-arm-config.md#smart-detection-rule-configuration).

Cet exemple de modèle Azure Resource Manager illustre la configuration d’une règle d’alerte de **dégradation de latence de réponse** dans un état **Activé** avec une gravité de 2.
* La détection intelligente est un service global. Par conséquent, l’emplacement de la règle est créé sur l’emplacement **global**.
* La propriété « ID » doit changer en fonction du détecteur spécifique configuré. La valeur doit être l’une des suivantes :

  - **FailureAnomaliesDetector**
  - **RequestPerformanceDegradationDetector**
  - **DependencyPerformanceDegradationDetector**
  - **ExceptionVolumeChangedDetector**
  - **TraceSeverityDetector**  
  - **MemoryLeakDetector**
 
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Response Latency Degradation - my-app",
            "location": "global", 
            "properties": {
                  "description": "Response Latency Degradation notifies you of an unusual increase in latency in your app response to requests.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT24H",
                  "detector": {
                  "id": "RequestPerformanceDegradationDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```



## <a name="next-steps"></a>Étapes suivantes

- [Plus d’informations sur les alertes dans Azure](./alerts-overview.md)
- [En savoir plus sur la détection intelligente dans Application Insights](../app/proactive-diagnostics.md)
