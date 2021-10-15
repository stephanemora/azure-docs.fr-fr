---
title: Créer, afficher et gérer des alertes de journal d’activité dans Azure Monitor
description: Créer des alertes de journal d’activité à l’aide du portail Azure, d’un modèle Azure Resource Manager et d’Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 08/12/2021
ms.openlocfilehash: 5cd113692cfe0c024980cb95252acf9f7763a2eb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360139"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Créer, afficher et gérer des alertes de journal d’activité à l’aide d’Azure Monitor  

Les *alertes de journal d’activité* s’activent lorsqu’un nouvel événement du journal d’activité correspond aux conditions spécifiées dans l’alerte. Vous créez ces alertes pour les ressources Azure en utilisant un modèle Azure Resource Manager. Vous pouvez également créer, mettre à jour ou supprimer ces alertes dans le portail Azure.

En général, les alertes de journal d’activité permettent de recevoir une notification en cas de modification particulière des ressources de l’abonnement Azure, souvent à l’échelle d’un groupe de ressources ou d’une ressource en particulier. Par exemple, vous pouvez être averti lorsqu’une machine virtuelle dans l’exemple de groupe de ressources `myProductionResourceGroup` est supprimée. Vous pouvez également vouloir être averti si de nouveaux rôles sont attribués à un utilisateur dans votre abonnement.

> [!IMPORTANT]
> Vous ne pouvez pas créer d’alertes sur les notifications d’intégrité de service en utilisant l’interface de création d’alertes de journal d’activité. Pour en savoir plus sur la création et l’utilisation des notifications Service Health, consultez [Recevoir des alertes de journal d’activité sur les notifications Service Health](../../service-health/alerts-activity-log-service-notifications-portal.md).

Lorsque vous créez des règles d’alerte, assurez-vous que :

- L’abonnement présent dans l’étendue n’est pas différent de celui dans lequel l’alerte est créée.
- Les critères doivent être : niveau, état, appelant, groupe de ressources, ID de ressource, type de ressource ou catégorie d’événement sur lesquels l’alerte est configurée.
- Il n’existe pas de condition `anyOf` ni de conditions imbriquées dans le fichier JSON de configuration de l’alerte. Une seule condition `allOf` est autorisée, sans autres conditions `allOf` ou `anyOf`.
- Lorsque la catégorie est `administrative`, vous devez spécifier au moins l’un des critères précédents dans votre alerte. Vous ne pouvez pas créer une alerte qui s’active chaque fois qu’un événement est créé dans les journaux d’activité.
- Les alertes ne peuvent pas être créées pour des événements dans la catégorie `alert` du journal d’activité.

## <a name="azure-portal"></a>Portail Azure

Vous pouvez utiliser le portail Azure pour créer et modifier des règles d'alerte du journal d'activité. L’expérience est intégrée au journal d’activité Azure pour garantir la transparence de la création d’alertes pour des événements d’intérêt spécifiques. Sur le portail Azure, vous pouvez créer une règle d’alerte de journal d’activité à partir du volet Alertes ou du volet Journal d’activité d’Azure Monitor. 

### <a name="create-an-alert-rule-from-the-azure-monitor-alerts-pane"></a>Créer une règle d’alerte à partir du volet Alertes d’Azure Monitor

Voici comment créer une règle d’alerte de journal d’activité dans le portail Azure :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Moniteur**. Le volet Moniteur consolide tous vos paramètres et données de supervision dans une même vue.

2. Sélectionnez **Alertes** >  **+ Nouvelle règle d’alerte**.

    :::image type="content" source="media/alerts-activity-log/create-alert-rule-button-new.png" alt-text="Capture d’écran illustrant le bouton de nouvelle règle d’alerte.":::
    > [!TIP]
    > La plupart des volets de ressources ont également l’option **Alertes** dans leur menu de ressources, sous **Supervision**. Vous pouvez également créer des alertes à partir de là.

3. Choisissez **Sélectionner une cible**, puis sélectionnez une ressource cible pour laquelle vous voulez créer une alerte. Pour rechercher la ressource à surveiller utilisez les listes dans **Abonnement** et **Type de ressource**. Vous pouvez également utiliser la barre de recherche pour rechercher votre ressource.
    
    > [!NOTE]
    > En guise que cible, vous pouvez sélectionner l’intégralité d’un abonnement, un groupe de ressources ou une ressource spécifique. Si vous choisissez un abonnement ou un groupe de ressources comme cible, et que vous sélectionnez également un type de ressource, la règle s’appliquera à toutes les ressources de ce type au sein de l’abonnement sélectionné ou d’un groupe de ressources. Si vous choisissez une ressource cible spécifique, la règle s’appliquera uniquement à celle-ci. Vous ne pouvez pas sélectionner explicitement plusieurs abonnements, groupes de ressources ou ressources à l’aide du sélecteur de cible. 

4. Si la ressource sélectionnée a des opérations de journal d’activité sur lesquelles vous pouvez créer des alertes, vous verrez que la liste **Types de signaux disponibles** contient **Journal d’activité**. Vous trouverez la liste complète des types de ressources pris en charge pour les alertes de journal d’activité dans [Opérations de fournisseur de ressources Azure](../../role-based-access-control/resource-provider-operations.md).

    :::image type="content" source="media/alerts-activity-log/select-target-new.png" alt-text="Capture d’écran du volet de sélection de la cible." lightbox="media/alerts-activity-log/select-target-new.png":::

5. Après avoir sélectionné une ressource cible, sélectionnez **Ajouter une condition**.

6. Vous verrez s’afficher une liste des signaux pris en charge pour la ressource, incluant ceux de différentes catégories de **Journal d’activité**. Sélectionnez le signal ou l’opération du journal d’activité sur lesquels vous souhaitez créer une alerte.

7. Vous verrez s’afficher un graphique présentant l’opération de journal d’activité au cours des six dernières heures. Utilisez la liste déroulante **Période du graphique** pour afficher un historique plus long de l’opération.

8. Sous **Logique d’alerte**, vous pouvez éventuellement définir des critères de filtrage supplémentaires :

    - **Niveau de l'événement** : Niveau de gravité de l’événement : _Commentaires_, _Information_, _Avertissement_, _Erreur_ ou _Critique_.
    - **État** : État de l’événement : _Démarré_, _Échec_ ou _Réussi_.
    - **Événement lancé par** : également appelé l’appelant. L’adresse e-mail ou l’identificateur Azure Active Directory de l’utilisateur qui a effectué l’opération.

    > [!NOTE]
    > Définir au moins un de ces critères vous aide à obtenir des règles plus efficaces. Par exemple, si l’étendue de l’alerte est un abonnement entier et que le signal sélectionné est `All Administrative Operations`, votre règle sera plus spécifique si vous fournissez le niveau de l’événement, l’état ou les informations d’initiation.
        
9. Sélectionnez **Terminé**.

    :::image type="content" source="media/alerts-activity-log/condition-selected-new.png" alt-text="Capture d’écran du volet de sélection des conditions." lightbox="media/alerts-activity-log/condition-selected-new.png":::

10. Renseignez les détails de l’alerte, tels que **Nom de la règle d’alerte**, **Description** et **Gravité**.

    > [!NOTE]
    > Actuellement, l’utilisateur ne peut pas configurer la gravité des alertes du journal d’activité. Le niveau de gravité est toujours défini par défaut sur **Sev4**.

11. Ajoutez un groupe d’actions à l’alerte, soit en sélectionnant un groupe d’actions existant, soit en créant un nouveau groupe d’actions.

12. Sélectionnez **Terminé** pour enregistrer la règle d’alerte du journal d’activité.
     
     
### <a name="create-an-alert-rule-from-the-azure-monitor-activity-log-pane"></a>Créer une règle d’alerte à partir du volet Journal d’activité d’Azure Monitor

Une autre façon de créer une alerte de journal d’activité consiste à démarrer avec un événement de journal d’activité qui s’est déjà produit, via le [Journal d’activité dans le portail Azure](../essentials/activity-log.md#view-the-activity-log). 

1. Dans le volet **Azure Monitor – Journal d’activité**, vous pouvez filtrer ou rechercher l’événement souhaité, puis créer une alerte sur les futurs événements similaires en sélectionnant **Ajouter une alerte de journal d’activité**. 

    :::image type="content" source="media/alerts-activity-log/create-alert-rule-from-activity-log-event-new.png" alt-text="Capture d’écran montrant la création d’une règle d’alerte à partir d’un événement de journal d’activité." lightbox="media/alerts-activity-log/create-alert-rule-from-activity-log-event-new.png":::

2. Le volet **Création de règle d’alerte** s’ouvre avec l’étendue et la condition de la règle d’alerte déjà fournies en fonction de l’événement du journal d’activité sélectionné précédemment. Le cas échéant, vous pouvez modifier l’étendue et la condition. Notez que, par défaut, l’étendue et la condition exactes de la nouvelle règle sont copiées à partir des attributs de l’événement d’origine. Par exemple, la ressource exacte sur laquelle l’événement s’est produit, ainsi que le nom de l’utilisateur ou du service spécifique qui a initié l’événement, sont tous deux inclus par défaut dans la nouvelle règle d’alerte. Si vous souhaitez rendre la règle d’alerte plus générale, modifiez l’étendue et la condition en conséquence (voir les étapes 3 à 9 de la section « Créer une règle d’alerte à partir du volet Alertes d’Azure Monitor »). 

3. Suivez ensuite les étapes 10 à 12 de la section « Créer une règle d’alerte à partir du volet Alertes d’Azure Monitor ».
    
### <a name="view-and-manage-in-the-azure-portal"></a>Afficher et gérer dans le Portail Azure

1. Dans le portail Azure, sélectionnez **Surveiller** > **Alertes**. Sélectionnez ensuite **Gérer les règles d’alerte**.

    :::image type="content" source="media/alerts-activity-log/manage-alert-rules-button-new.png" alt-text="Capture d’écran montrant le bouton Gérer les règles d’alerte.":::
    
    La liste des règles disponibles s’affiche.

2. Filtrez ou recherchez la règle de journal d’activité à modifier.

    :::image type="content" source="media/alerts-activity-log/manage-alert-rules-new.png" alt-text="Capture d’écran montrant le volet de gestion des règles d’alerte." lightbox="media/alerts-activity-log/manage-alert-rules-new.png":::

    Vous pouvez utiliser les filtres disponibles (_Abonnement_, _Groupe de ressources_, _Ressource_, _Type de signal_ ou _État_) pour trouver la règle d’activité que vous souhaitez modifier.
 
3. Sélectionnez la règle, puis double-cliquez pour modifier ses options. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**. 

## <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager
Pour créer une règle d’alerte de journal d’activité à l’aide d’un modèle Azure Resource Manager, créez une ressource de type `microsoft.insights/activityLogAlerts`. Puis, renseignez toutes les propriétés associées. Voici un modèle qui crée une règle d’alerte de journal d’activité :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
L’exemple JSON précédent peut être enregistré sous le nom *sampleActivityLogAlert.json*, par exemple. Vous pouvez déployer l’exemple en utilisant [Azure Resource Manager dans le portail Azure](../../azure-resource-manager/templates/deploy-portal.md).

> [!NOTE]
> Notez que le plus haut niveau d’alerte du journal d’activité pouvant être défini est le niveau de l’abonnement. Il n’existe aucune option permettant de définir une alerte sur deux abonnements. La définition doit être une alerte par abonnement.

Les champs suivants sont les options que vous pouvez utiliser dans le modèle Azure Resource Manager pour les champs de conditions. (Notez que **Resource Health**, **Advisor** et **Service Health** ont des champs de propriétés supplémentaires pour leurs champs spéciaux.) 

1. `resourceId` : ID de la ressource concernée dans l’événement du journal d’activité sur lequel l’alerte doit être générée.
1. `category` : Catégorie de l’événement du journal d’activité. Par exemple : `Administrative`, `ServiceHealth`, `ResourceHealth`, `Autoscale`, `Security`, `Recommendation` ou `Policy`.
1. `caller` : Adresse e-mail ou identificateur Azure Active Directory de l’utilisateur qui a effectué l’opération de l’événement du journal d’activité.
1. `level` : Niveau de l’activité dans l’événement du journal d’activité sur lequel l’alerte doit être générée. Par exemple : `Critical`, `Error`, `Warning`, `Informational` ou `Verbose`.
1. `operationName` : Nom de l’opération dans l’événement du journal d’activité. Par exemple : `Microsoft.Resources/deployments/write`.
1. `resourceGroup` : Nom du groupe de ressources pour la ressource concernée dans l’événement du journal d’activité.
1. `resourceProvider` : Pour plus d’informations, consultez [Fournisseurs et types de ressources Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Pour obtenir la liste qui mappe les fournisseurs de ressources aux services Azure, consultez [Fournisseurs de ressources pour les services Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
1. `status` : Chaîne décrivant l’état de l’opération dans l’événement d’activité. Par exemple : `Started`, `In Progress`, `Succeeded`, `Failed`, `Active` ou `Resolved`.
1. `subStatus` : Généralement, ce champ désigne le code d’état HTTP de l’appel REST correspondant. Mais il peut également inclure d’autres chaînes décrivant un sous-état. Parmi les exemples de codes d’état HTTP, citons `OK` (code d’état HTTP : 200), `No Content` (code d’état HTTP : 204) et `Service Unavailable` (code d’état HTTP : 503), entre autres.
1. `resourceType` : Type de la ressource concernée par l’événement. Par exemple : `Microsoft.Resources/deployments`.

Par exemple :

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```

Pour plus d’informations sur les champs du journal d’activité, consultez [Schéma d’événements du journal d’activité Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).

> [!NOTE]
> L'activation d'une nouvelle règle d'alerte de journal d'activité peut prendre jusqu'à 5 minutes.

## <a name="rest-api"></a>API REST 
L’API Azure Monitor Activity Log Alerts est une API REST. Elle est entièrement compatible avec l'API REST Azure Resource Manager. Vous pouvez l’utiliser avec PowerShell, en utilisant la cmdlet Resource Manager ou avec Azure CLI.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Déployer le modèle Azure Resource Manager avec PowerShell
Pour utiliser PowerShell afin de déployer l’exemple Resource Manager indiqué dans la section [Modèle Azure Resource Manager](#azure-resource-manager-template) précédente, utilisez la commande suivante :

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

Le fichier *sampleActivityLogAlert.parameters.json* contient les valeurs communiquées pour les paramètres nécessaires à la création des règles d’alerte.

### <a name="use-activity-log-powershell-cmdlets"></a>Utiliser les applets de commande PowerShell pour le journal d'activité

Les alertes de journal d'activité disposent de cmdlets PowerShell dédiées :

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert) : Crée ou met à jour une alerte de journal d’activité existante.
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert) : Obtient une ou plusieurs ressources d’alerte de journal d'activité.
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert) : Active une alerte de journal d’activité existante et définit ses balises.
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert) : Désactive une alerte de journal d’activité existante et définit ses balises.
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert) : Supprime une alerte de journal d’activité.

### <a name="azure-cli"></a>Azure CLI

Vous pouvez gérer les règles d’alerte de journal d’activité à l’aide de commandes Azure CLI dédiées sous l’ensemble [az monitor activity-log alert](/cli/azure/monitor/activity-log/alert).

Pour créer une règle d’alerte de journal d’activité, utilisez les commandes suivantes :

1. [az monitor activity-log alert create](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create) : permet de créer une ressource de règle d'alerte de journal d'activité.
2. [az monitor activity-log alert scope](/cli/azure/monitor/activity-log/alert/scope) : permet d'ajouter une étendue pour la règle d'alerte de journal d'activité créée.
3. [az monitor activity-log alert action-group](/cli/azure/monitor/activity-log/alert/action-group) : permet d'ajouter un groupe d'actions à la règle d'alerte de journal d'activité.

Pour récupérer une ressource de règle d'alerte de journal d'activité, utilisez la commande Azure CLI [az monitor activity-log alert show](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Pour afficher toutes les ressources de règle d'alerte de journal d'activité d'un groupe de ressources, utilisez la commande [az monitor activity-log alert list](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Vous pouvez supprimer les ressources de règle d’alerte de journal d’activité à l’aide de la commande Azure CLI [az monitor activity-log alert delete](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [schéma de webhook des journaux d’activité](./activity-log-alerts-webhook.md).
- Lisez une [présentation des journaux d’activité](./activity-log-alerts.md).
- En savoir plus sur les [groupes d’actions](./action-groups.md).  
- En savoir plus sur les [notifications sur l’intégrité du service](../../service-health/service-notifications.md).