---
title: Créer, afficher et gérer des alertes de journal d’activité dans Azure Monitor
description: Comment créer des alertes de journal d’activité depuis le portail Azure, un modèle de ressources et PowerShell
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.openlocfilehash: 2b069e55d98da824363dc480c211cde0fcc2518c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090812"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Créer, afficher et gérer des alertes de journal d'activité avec Azure Monitor  

## <a name="overview"></a>Présentation
Les alertes de journal d’activité s’activent lorsqu’un nouvel événement du journal d’activité correspond aux conditions spécifiées dans l’alerte.

Elles concernent les ressources Azure. Pour les créer, il est possible d’utiliser un modèle Azure Resource Manager. Elles peuvent également être créées, mises à jour ou supprimées dans le portail Azure. En général, les alertes de journal d’activité permettent de recevoir une notification en cas de modification particulière des ressources de l’abonnement Azure, souvent à l’échelle d’un groupe de ressources ou d’une ressource en particulier. Par exemple, vous pouvez demander à être informé lorsqu’une machine virtuelle de **myProductionResourceGroup** (exemple de groupe de ressources) est supprimée, ou que de nouveaux rôles sont attribués à l’un des utilisateurs de votre abonnement.

> [!IMPORTANT]
> Il n’est pas possible de créer des alertes de notification Service Health avec l’interface de création d’alertes de journal d'activité. Pour en savoir plus sur la création et l’utilisation des notifications Service Health, consultez [Recevoir des alertes de journal d’activité sur les notifications Service Health](alerts-activity-log-service-notifications.md).

## <a name="azure-portal"></a>Portail Azure

> [!NOTE]
> 
>  Lorsque vous créez des règles d’alerte, vérifiez les points suivants :
> 
> - L’abonnement présent dans l’étendue n’est pas différent de celui dans lequel l’alerte est créée.
> - Les critères doivent être : niveau / état / appelant / groupe de ressources / ID de ressource / type de ressource / catégorie d’événement, sur lesquels l’alerte est configurée.
> - Il n’existe aucune condition « anyOf » ni aucune condition imbriquée dans le JSON de configuration des alertes (en fait, une seule condition allOf est autorisée, sans aucune autre allOf/anyOf).
> - Lorsque la catégorie est « administratif ». Vous devez spécifier au moins l’un des critères précédents dans votre alerte. L’alerte créée ne s’activera peut-être pas à chaque fois qu’un événement sera créé dans les journaux d’activité.

### <a name="create-with-azure-portal"></a>Créer avec le Portail Azure

Procédez comme suit :

1. Dans le portail Azure, sélectionnez **Surveiller** > **Alertes**.
2. Cliquez sur **Nouvelle règle d’alerte** en haut de la fenêtre **Alertes**.

     ![nouvelle règle d'alerte](media/alerts-activity-log/AlertsPreviewOption.png)

     La fenêtre **Créer une règle** s’affiche.

      ![nouvelles options d'alerte](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Sous **Définir une condition d’alerte,** indiquez les informations suivantes, puis cliquez sur **Fait**.

   - **Cible de l’alerte :** pour afficher et sélectionner la cible de la nouvelle alerte, utilisez **Filtrer par abonnement** / **Filtrer par type de ressource** et sélectionnez la ressource ou le groupe de ressources dans la liste qui s’affiche.

     > [!NOTE]
     > 
     > Vous pouvez sélectionner une ressource, un groupe de ressources ou un abonnement entier comme signal du journal d'activité.

     **Exemple de vue de cible d’alerte**
     ![sélectionner la cible](media/alerts-activity-log/select-target.png)

   - Sous **Critères de ciblage**, cliquez sur **Ajouter des critères**. Tous les signaux disponibles pour la cible s’affichent alors, y compris ceux des différentes catégories du **journal d’activité**. Le nom de la catégorie est ajouté dans le nom **Service de surveillance**.

   - Sélectionnez le signal dans la liste des différentes opérations possibles pour le type **Journal d’activité**.

     Vous pouvez sélectionner la chronologie de l’historique du journal et la logique d’alerte correspondante pour ce signal cible :

     **Écran Ajouter des critères**

     ![ajouter des critères](media/alerts-activity-log/add-criteria.png)

     **Échelle de l’historique** : les événements disponibles pour l’opération sélectionnée peuvent s’être déroulés au cours des 6/12/24 dernières heures ou la dernière semaine.

     **Logique d'alerte** :

     - **Niveau de l'événement** : le niveau de gravité de l’événement. _Commentaires_, _Information_, _Avertissement_, _Erreur_ ou _Critique_.
     - **Statut** : État de l’événement. _Démarré_, _Échec_ ou _Réussi_.
     - **Événement lancé par** : également connu en tant qu’appelant. Adresse e-mail ou identificateur Azure Active Directory de l’utilisateur qui a effectué l’opération.

       Exemple de graphique de signal avec application d’une logique d’alerte :

       ![ critères sélectionnés](media/alerts-activity-log/criteria-selected.png)

4. Sous **Définir les détails des règles d’alerte**, indiquez les informations suivantes :

    - **Nom de la règle d’alerte** : nom de la nouvelle règle d’alerte.
    - **Description** : description de la nouvelle règle d’alerte.
    - **Enregistrer l’alerte dans le groupe de ressources** : sélectionnez le groupe de ressources dans lequel vous souhaitez enregistrer cette nouvelle règle.

5. Dans le menu déroulant sous **Groupe d’actions**, spécifiez le groupe d’actions que vous souhaitez affecter à cette nouvelle règle d’alerte. Vous pouvez également [créer un nouveau groupe d’actions](../../azure-monitor/platform/action-groups.md) et l’affecter à la nouvelle règle. Pour créer un groupe, cliquez sur **+ Nouveau groupe**.

6. Pour activer les règles après l’avoir créé, cliquez sur **Oui** dans l’option **Activer la règle lors de la création**.
7. Cliquez sur **Créer une règle d'alerte**.

    La nouvelle règle d’alerte de journal d’activité est créée et un message de confirmation s’affiche en haut à droite de la fenêtre.

    Vous pouvez activer, désactiver, modifier ou supprimer une règle. En savoir plus sur la gestion des règles de journal d’activité.


Sinon, pour bien comprendre les conditions dans lesquelles des règles d’alerte peuvent être créées sur le journal d’activité, il est possible d’explorer ou de filtrer les événements par le biais du [Journal d’activité dans le portail Azure](../../azure-monitor/platform/activity-logs-overview.md#query-the-activity-log-in-the-azure-portal). Dans Azure Monitor - Journal d’activité, il est possible de filtrer ou de rechercher l’événement requis, puis de créer une alerte à l’aide du bouton **Ajouter une alerte de journal d’activité**. Poursuivez ensuite jusqu’à l’étape 4 comme indiqué dans le didacticiel ci-dessus.
    
 ![ ajouter une alerte du journal d’activité](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-azure-portal"></a>Afficher et gérer dans le Portail Azure

1. Dans le portail Azure, cliquez sur **Surveiller** > **Alertes**, puis sur **Gérer les règles** en haut à gauche de la fenêtre.

    ![ gérer les règles d’alerte](media/alerts-activity-log/manage-alert-rules.png)

    La liste des règles disponibles s’affiche.

2. Recherchez la règle de journal d’activité à modifier.

    ![ rechercher des règles d’alerte de journal d'activité](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Vous pouvez utiliser les filtres disponibles (_Abonnement_, _Groupe de ressources_, _Ressource_, _Type de signal_ ou _État_) pour trouver la règle d’activité que vous souhaitez modifier.

   > [!NOTE]
   > 
   > Les seuls champs que vous pouvez éditer sont : **Description** , **Critères cibles** et **Groupes d’actions**.

3. Sélectionnez la règle, puis double-cliquez pour modifier ses options. Apportez les modifications nécessaires, puis cliquez sur **Enregistrer**.

   ![ gérer les règles d’alerte](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Vous pouvez désactiver, activer ou supprimer un profil. Sélectionnez l’option souhaitée en haut de la fenêtre, après avoir sélectionné la règle en suivant les instructions de l’étape 2.


## <a name="azure-resource-template"></a>Modèle de ressource Azure
Pour créer une alerte de journal d’activité à l’aide d’un modèle Resource Manager, créez une ressource de type `microsoft.insights/activityLogAlerts`. Puis, renseignez toutes les propriétés associées. Voici un modèle qui crée une alerte de journal d’activité.

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
L’exemple json ci-dessus peut par exemple être enregistré en tant que sampleActivityLogAlert.json pour les besoins de cette procédure pas à pas, et peut être déployé à l’aide d’[Azure Resource Manager dans le portail Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> L'activation d'une nouvelle règle d'alerte de journal d'activité peut prendre jusqu'à 5 minutes.

## <a name="rest-api"></a>API REST 
L’[API Azure Monitor - Alertes du journal d'activité](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) est une API REST entièrement compatible avec l’API REST Azure Resource Manager. Elle peut donc être utilisée par le biais de Powershell à l’aide d’applets de commande Resource Manager en plus d’Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-resource-manager-template-with-powershell"></a>Déployer le modèle Resource Manager avec PowerShell
Utilisation de PowerShell pour déployer l’exemple de modèle de ressource indiqué dans une section précédente de [modèle de ressources] (#-modèle resource manager-, utilisez la commande suivante :

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

où le sampleActivityLogAlert.parameters.json contient les valeurs fournies pour les paramètres nécessaires pour la création d’une règle d’alerte.

### <a name="use-activity-log-powershell-cmdlets"></a>Utiliser les applets de commande PowerShell de journal d’activité

Les alertes de journal d'activité disposent de cmdlets PowerShell dédiées :

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert?view=azps-1.3.0) : Crée ou mettre à jour une alerte de journal d’activité existante.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert?view=azps-1.3.0) : Obtient un ou plusieurs activités ressources alerte de journal.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert?view=azps-1.3.0) : Permet à une alerte de journal d’activité existante et définit ses balises.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert?view=azps-1.3.0) : Désactive une alerte de journal d’activité existante et définit ses balises.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert?view=azps-1.3.0)    : Supprime une alerte de journal d’activité.

## <a name="cli"></a>Interface de ligne de commande

Les commandes Azure CLI dédiées situées sous l'ensemble [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) permettent de gérer les règles d'alerte de journal d'activité.

Pour créer une règle d'alerte de journal d'activité, utilisez dans cet ordre :

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create) : permet de créer une ressource de règle d'alerte de journal d'activité
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope) : permet d'ajouter une étendue pour la règle d'alerte de journal d'activité créée
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group) : permet d'ajouter un groupe d'actions à la règle d'alerte de journal d'activité

Pour récupérer une ressource de règle d'alerte de journal d'activité, vous pouvez utiliser la commande Azure CLI [az monitor activity-log alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Et pour afficher toutes les ressources de règle d'alerte de journal d'activité d'un groupe de ressources, utilisez la commande [az monitor activity-log alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Les ressources de règle d'alerte de journal d'activité peuvent être supprimées à l'aide de la commande Azure CLI [az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Étapes suivantes

- [Schéma de webhook des journaux d’activité](../../azure-monitor/platform/activity-log-alerts-webhook.md)
- [Présentation des journaux d’activité](../../azure-monitor/platform/activity-log-alerts.md) 
- En savoir plus sur les [groupes d’actions](../../azure-monitor/platform/action-groups.md).  
- En savoir plus sur les [notifications sur l’intégrité du service](../../azure-monitor/platform/service-notifications.md).
