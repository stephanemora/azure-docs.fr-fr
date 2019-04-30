---
title: Configurer les paramètres de règle de détection intelligente Azure Application Insights avec les modèles Azure Resource Manager | Microsoft Docs
description: Automatiser la gestion et la configuration des règles de détection intelligente d’Azure Application Insights avec les modèles Azure Resource Manager
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/07/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 3ab50c92543615488d9ced599df433bf7e1e4061
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461559"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Gérer les règles de détection intelligente d’Application Insights à l’aide de modèles Azure Resource Manager

Les règles de détection intelligente dans Application Insights peuvent être gérées et configurées à l’aide de [modèles Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
Cette méthode peut être utilisée lors du déploiement de nouvelles ressources Application Insights avec Azure Resource Manager Automation, ou pour modifier les paramètres des ressources existantes.

## <a name="smart-detection-rule-configuration"></a>Configuration de règle de détection intelligente

Vous pouvez configurer les paramètres suivants pour une règle de détection intelligente :
- Si la règle est activée (la valeur par défaut est **true**.)
- Si des messages doivent être envoyés aux propriétaires d’abonnement, contributeurs et lecteurs quand une détection est trouvée (la valeur par défaut est **true**.)
- Les destinataires d’e-mails supplémentaires qui doivent recevoir une notification lorsqu’une détection est trouvée.
- * La configuration des e-mails n’est pas disponible pour les règles de détection intelligente indiquant _Préversion_.

Pour autoriser la configuration des paramètres de règle via Azure Resource Manager, la configuration de règle de détection intelligente est désormais disponible en tant que ressource interne au sein de la ressource Application Insights, nommée **ProactiveDetectionConfigs**.
Pour une flexibilité maximale, chaque règle de détection intelligente peut être configurée avec les paramètres de notification unique.

## <a name="examples"></a>Exemples

Voici quelques exemples montrant comment configurer les paramètres des règles de détection intelligente à l’aide de modèles Azure Resource Manager.
Tous les exemples font référence à une ressource Application Insights nommée _« myApplication »_, et à la « règle de détection intelligente de dépendance longue durée », qui est nommée en interne _« longdependencyduration »_.
Assurez-vous de remplacer le nom de la ressource Application Insights et de spécifier le nom interne de règle de détection intelligente pertinente. Consultez le tableau ci-dessous pour obtenir la liste des noms Azure Resource Manager internes correspondants à chaque règle de détection intelligente.

### <a name="disable-a-smart-detection-rule"></a>Gérer des règles de détection intelligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Désactiver l’envoi de notifications par e-mail pour une règle de détection intelligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Ajouter des destinataires d’e-mail supplémentaires pour une règle de détection intelligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

## <a name="smart-detection-rule-names"></a>Règles de détection intelligente

Voici un tableau des noms de règle de détection intelligente tels qu’ils apparaissent dans le portail, ainsi que leurs noms internes, qui doit être utilisé dans le modèle Azure Resource Manager.

> [!NOTE]
> Les règles de détection intelligente marquées comme version préliminaire ne prennent pas en charge les notifications par e-mail. Par conséquent, vous pouvez uniquement définir la propriété activée pour ces règles. 

| Nom de la règle du portail Azure | Nom interne
|:---|:---|
| Durée de chargement de page lente | slowpageloadtime |
| Temps de réponse du serveur lent | slowserverresponsetime |
| Durée de dépendance longue | longdependencyduration |
| Dégradation du temps de réponse du serveur | degradationinserverresponsetime |
| Dégradation de la durée de dépendance | degradationindependencyduration |
| Dégradation du rapport entre les niveaux de gravité des suivis (préversion) | extension_traceseveritydetector |
| Élévation anormale du volume des exceptions (préversion) | extension_exceptionchangeextension |
| Fuite de mémoire potentielle détectée (préversion) | extension_memoryleakextension |
| Problème de sécurité potentiel détecté (préversion) | extension_securityextensionspackage |
| Problème d’utilisation des ressources détecté (préversion) | extension_resourceutilizationextensionspackage |

## <a name="who-receives-the-classic-alert-notifications"></a>Qui reçoit les notifications d'alerte (classiques) ?

Cette section ne s'applique qu'aux alertes de détection intelligente classiques et vous aidera à optimiser vos notifications d'alerte afin que seuls les destinataires de votre choix les reçoivent. Pour mieux comprendre la différence entre les [alertes classiques](../platform/alerts-classic.overview.md) et les nouvelles alertes, reportez-vous à l’[article de présentation des alertes](../platform/alerts-overview.md). Actuellement, les alertes de détection intelligente prennent uniquement en charge les alertes classiques. Les [alertes de détection intelligente sur Azure Cloud Services](./proactive-cloud-services.md) en sont la seule exception. Pour contrôler la notification des alertes de détection intelligente sur Azure Cloud Services, utilisez les [groupes d’actions](../platform/action-groups.md).

* Nous recommandons l'utilisation de destinataires spécifiques pour les notifications d'alertes de détection intelligente/classiques.

* Pour les alertes de détection intelligente, l’option **En bloc/groupe**, si elle est activée, envoie des alertes aux utilisateurs ayant des rôles de propriétaire, contributeur ou lecteur dans l’abonnement. Dans les faits, _tous_ les utilisateurs ayant accès à la ressource Application Insights sont concernés et recevront des notifications. 

> [!NOTE]
> Si vous utilisez actuellement l'option **En bloc/groupe** et que vous la désactivez, vous ne pourrez pas annuler la modification.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la détection automatique :

- [Défaillances](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Fuites de mémoire](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalies de performance](../../azure-monitor/app/proactive-performance-diagnostics.md)