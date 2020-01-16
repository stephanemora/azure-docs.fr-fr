---
title: Créer, afficher et gérer des alertes de journal d’activité dans Azure Monitor
description: Créer des alertes de journal d’activité à l’aide du portail Azure, d’un modèle Azure Resource Manager et d’Azure PowerShell.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 06/25/2019
ms.openlocfilehash: 4835f1034149a015963569a0b1fc5f9195e3cfca
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969535"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Créer, afficher et gérer des alertes de journal d’activité à l’aide d’Azure Monitor  

## <a name="overview"></a>Vue d’ensemble
Les alertes de journal d’activité s’activent lorsqu’un nouvel événement du journal d’activité correspond aux conditions spécifiées dans l’alerte.

Elles concernent les ressources Azure. Pour les créer, il est possible d’utiliser un modèle Azure Resource Manager. Elles peuvent également être créées, mises à jour ou supprimées dans le portail Azure. En général, les alertes de journal d’activité permettent de recevoir une notification en cas de modification particulière des ressources de l’abonnement Azure, souvent à l’échelle d’un groupe de ressources ou d’une ressource en particulier. Par exemple, vous pouvez être averti lorsqu’une machine virtuelle dans l’exemple de groupe de ressources **myProductionResourceGroup** est supprimée. Vous pouvez également vouloir être averti si de nouveaux rôles sont attribués à un utilisateur dans votre abonnement.

> [!IMPORTANT]
> Il n’est pas possible de créer des alertes de notification Service Health avec l’interface de création d’alertes de journal d'activité. Pour en savoir plus sur la création et l’utilisation des notifications Service Health, consultez [Recevoir des alertes de journal d’activité sur les notifications Service Health](alerts-activity-log-service-notifications.md).

Lorsque vous créez des règles d’alerte, vérifiez les points suivants :

- L’abonnement présent dans l’étendue n’est pas différent de celui dans lequel l’alerte est créée.
- Les critères doivent être : niveau, état, appelant, groupe de ressources, ID de ressource, type de ressource ou catégorie d’événement sur lesquels l’alerte est configurée.
- Il n'existe pas de condition « anyOf » ou de conditions imbriquées dans le fichier JSON de configuration d'alerte. En principe, une seule condition « allOf » est autorisée sans autre condition « allOf » ou « anyOf ».
- Lorsque la catégorie est « administration », vous devez spécifier au moins l’un des critères précédents dans votre alerte. L’alerte créée ne s’activera peut-être pas à chaque fois qu’un événement sera créé dans les journaux d’activité.


## <a name="azure-portal"></a>Portail Azure

Vous pouvez utiliser le portail Azure pour créer et modifier des règles d'alerte du journal d'activité. L’expérience est intégrée au journal d’activité Azure pour garantir la transparence de la création d’alertes pour des événements d’intérêt spécifiques.

### <a name="create-with-the-azure-portal"></a>Créer avec le Portail Azure

Procédez comme suit.

1. Dans le portail Azure, sélectionnez **Surveiller** > **Alertes**.
2. Sélectionnez **Nouvelle règle d'alerte** dans le coin supérieur gauche de la fenêtre **Alertes**.

     ![Nouvelle règle d’alerte](media/alerts-activity-log/AlertsPreviewOption.png)

     La fenêtre **Créer une règle** s’affiche.

      ![Nouvelles options de règle d’alerte](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Sous **Définir une condition d’alerte,** indiquez les informations suivantes, puis sélectionnez **Fait** :

   - **Cible de l’alerte :** Pour afficher et sélectionner la cible de la nouvelle alerte, utilisez **Filtrer par abonnement** / **Filtrer par type de ressource**. Sélectionnez la ressource ou le groupe de ressources dans la liste affichée.

     > [!NOTE]
     > 
     > Vous pouvez sélectionner uniquement ressource suivi [Azure Resource Manager](../../azure-resource-manager/management/overview.md), groupe de ressources ou abonnement complet pour un signal de journal d'activité. 

     **Vue Exemple de cible de l’alerte**

     ![Sélectionner la cible](media/alerts-activity-log/select-target.png)

   - Sous **Critères cibles**, sélectionnez **Ajouter des critères**. Tous les signaux disponibles pour la cible sont affichés, y compris ceux des différentes catégories du **journal d'activité**. Le nom de la catégorie est ajouté au nom du **service d’analyse**.

   - Sélectionnez le signal dans la liste des différentes opérations possibles pour le type **Journal d’activité**.

     Vous pouvez sélectionner la chronologie de l’historique du journal et la logique d’alerte correspondante pour ce signal cible :

     **Écran Ajouter des critères**

     ![Ajouter des critères](media/alerts-activity-log/add-criteria.png)

     - **Échelle de l’historique** : les événements disponibles pour l’opération sélectionnée peuvent s’être déroulés au cours des 6, 12 ou 24 dernières heures ou la dernière semaine.

     - **Logique d'alerte** :

       - **Niveau de l'événement** : Niveau de gravité de l’événement : _Commentaires_, _Information_, _Avertissement_, _Erreur_ ou _Critique_.
       - **État** : État de l’événement : _Démarré_, _Échec_ ou _Réussi_.
       - **Événement lancé par** : également appelé l’appelant. L’adresse e-mail ou l’identificateur Azure Active Directory de l’utilisateur qui a effectué l’opération.

       Une logique d’alerte a été appliquée à cet exemple de graphique de signal :

       ![Critères sélectionnés](media/alerts-activity-log/criteria-selected.png)

4. Sous **Définir les détails de l’alerte**, indiquez les informations suivantes :

    - **Nom de la règle d’alerte** : nom de la nouvelle règle d’alerte.
    - **Description** : Description de la nouvelle règle d’alerte.
    - **Enregistrer l'alerte dans le groupe de ressources** : sélectionnez le groupe de ressources dans lequel vous souhaitez enregistrer cette nouvelle règle.

5. Dans le menu déroulant sous **Groupe d’actions**, spécifiez le groupe d’actions que vous souhaitez affecter à cette nouvelle règle d’alerte. Vous pouvez également [créer un groupe d’actions](../../azure-monitor/platform/action-groups.md) et l’affecter à la nouvelle règle. Pour créer un groupe, sélectionnez **+ Nouveau groupe**.

6. Pour activer les règles après les avoir créées, cliquez sur **Oui** dans l’option **Activer la règle lors de la création**.
7. Sélectionnez **Créer une règle d’alerte**.

    La nouvelle règle d’alerte de journal d’activité est créée et un message de confirmation s’affiche en haut à droite de la fenêtre.

    Vous pouvez activer, désactiver, modifier ou supprimer une règle. En savoir plus sur la gestion des règles de journal d’activité.


Pour bien comprendre les conditions dans lesquelles des règles d’alerte peuvent être créées sur le journal d’activité, il est possible d’explorer ou de filtrer les événements par le biais du [Journal d’activité dans le portail Azure](activity-log-view.md#azure-portal). Dans l’écran **Azure Monitor - Journal d’activité**, vous pouvez filtrer ou rechercher l’événement requis, puis créer une alerte à l’aide du bouton **Ajouter une alerte de journal d’activité**. Suivez ensuite les étapes 4 à 7 comme indiqué précédemment.
    
 ![Ajouter une alerte du journal d’activité](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Afficher et gérer dans le Portail Azure

1. Dans le portail Azure, sélectionnez **Surveiller** > **Alertes**. Dans l’angle supérieur gauche de la fenêtre, sélectionnez **Gérer les règles d'alerte**.

    ![Gérer les règles d’alerte](media/alerts-activity-log/manage-alert-rules.png)

    La liste des règles disponibles s’affiche.

2. Recherchez la règle de journal d’activité à modifier.

    ![Rechercher des règles d’alerte de journal d'activité](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Vous pouvez utiliser les filtres disponibles (_Abonnement_, _Groupe de ressources_, _Ressource_, _Type de signal_ ou _État_) pour trouver la règle d’activité que vous souhaitez modifier.

   > [!NOTE]
   > 
   > Les seuls champs que vous pouvez éditer sont : **Description** , **Critères cibles** et **Groupes d’actions**.

3. Sélectionnez la règle, puis double-cliquez pour modifier ses options. Apportez les modifications nécessaires, puis sélectionnez **Enregistrer**.

   ![Gérer les règles d’alerte](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Vous pouvez activer, désactiver ou supprimer une règle. Sélectionnez l’option souhaitée en haut de la fenêtre, après avoir sélectionné la règle comme décrit à l’étape 2.


## <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager
Pour créer une alerte de journal d’activité à l’aide d’un modèle Azure Resource Manager, créez une ressource de type `microsoft.insights/activityLogAlerts`. Puis, renseignez toutes les propriétés associées. Voici un modèle qui crée une alerte de journal d’activité :

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
L’exemple JSON précédent peut, par exemple, être enregistré en tant que sampleActivityLogAlert.json pour les besoins de cette procédure pas à pas, et peut être déployé à l’aide d’[Azure Resource Manager dans le portail Azure](../../azure-resource-manager/templates/deploy-portal.md).

> [!NOTE]
> L'activation d'une nouvelle règle d'alerte de journal d'activité peut prendre jusqu'à 5 minutes.

## <a name="rest-api"></a>API REST 
L’[API Azure Monitor Activity Log Alerts](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) est une API REST. Elle est entièrement compatible avec l'API REST Azure Resource Manager. Elle peut être utilisée via PowerShell à l’aide d’applets de commande Resource Manager en plus de l’interface de ligne de commande (CLI) Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Déployer le modèle Azure Resource Manager avec PowerShell
Pour utiliser PowerShell afin de déployer l’exemple Resource Manager indiqué dans la section [Modèle Azure Resource Manager](#azure-resource-manager-template) précédente, utilisez la commande suivante :

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

Où sampleActivityLogAlert.parameters.json contient les valeurs communiquées pour les paramètres requis pour la création d’une règle d’alerte.

### <a name="use-activity-log-powershell-cmdlets"></a>Utiliser les applets de commande PowerShell pour le journal d'activité

Les alertes de journal d'activité disposent de cmdlets PowerShell dédiées :

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert) : Crée ou met à jour une alerte de journal d’activité existante.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert) : Obtient une ou plusieurs ressources d’alerte de journal d'activité.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert) : Active une alerte de journal d’activité existante et définit ses balises.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert) : Désactive une alerte de journal d’activité existante et définit ses balises.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert) : Supprime une alerte de journal d’activité.

## <a name="azure-cli"></a>Azure CLI

Les commandes Azure CLI dédiées situées sous l'ensemble [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) permettent de gérer les règles d'alerte de journal d'activité.

Pour créer une règle d'alerte de journal d'activité, utilisez les commandes suivantes dans cet ordre :

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create) : permet de créer une ressource de règle d'alerte de journal d'activité.
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope) : permet d'ajouter une étendue pour la règle d'alerte de journal d'activité créée.
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group) : permet d'ajouter un groupe d'actions à la règle d'alerte de journal d'activité.

Pour récupérer une ressource de règle d'alerte de journal d'activité, utilisez la commande Azure CLI [az monitor activity-log alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Pour afficher toutes les ressources de règle d'alerte de journal d'activité d'un groupe de ressources, utilisez la commande [az monitor activity-log alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Les ressources de règle d'alerte de journal d'activité peuvent être supprimées à l'aide de la commande Azure CLI [az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [schéma de webhook des journaux d’activité](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Lisez une [présentation des journaux d’activité](../../azure-monitor/platform/activity-log-alerts.md).
- En savoir plus sur les [groupes d’actions](../../azure-monitor/platform/action-groups.md).  
- En savoir plus sur les [notifications sur l’intégrité du service](../../azure-monitor/platform/service-notifications.md).
