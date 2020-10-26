---
title: Créer et gérer des groupes d’actions sur le Portail Azure
description: Découvrez comment créer et gérer des groupes d’action sur le Portail Azure.
author: dkamstra
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: ab76b4aafd73173077d5b2b9f29e6e2da24c1710
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104391"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Créer et gérer des groupes d’actions sur le Portail Azure
Un groupe d’actions est une collection de préférences de notification définies par le propriétaire d’un abonnement Azure. Les alertes Azure Monitor et Service Health utilisent des groupes d’actions pour avertir les utilisateurs qu’une alerte a été déclenchée. Plusieurs alertes peuvent utiliser le même groupe d’actions ou des groupes d’actions différents selon les besoins de l’utilisateur. Vous pouvez configurer jusqu'à 2 000 groupes d'actions au sein d'un abonnement.

Cet article vous montre comment créer et gérer des groupes d’actions dans le portail Azure.

Chaque action se compose des propriétés suivantes :

* **Type**  : notification ou action effectuée. Exemples : envoi d'un appel vocal, d'un SMS ou d'un e-mail ; ou déclenchement de différents types d'actions automatisées. Reportez-vous aux types présentés plus loin dans cet article.
* **Name**  : identificateur unique au sein du groupe d’actions.
* **Détails** : détails correspondants qui varient selon le *type* .

Pour plus d’informations sur l’utilisation de modèles Azure Resource Manager pour configurer des groupes d’actions, consultez la page [Modèles Resource Manager de groupes d’actions](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Créer un groupe d’actions à l’aide du Portail Azure

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez **Monitor** Le volet **Moniteur** consolide tous vos paramètres et données de supervision dans une même vue.

1. Sélectionnez **Alertes** , puis **Gérer les actions** .

    ![Bouton Gérer les actions](./media/action-groups/manage-action-groups.png)
    
1. Sélectionnez **Ajouter un groupe d’actions** , puis remplissez les champs appropriés dans l’expérience de l’assistant.

    ![La commande « Ajouter un groupe d’actions »](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Configurer les paramètres du groupe d’actions de base

Sous **Détails du projet**  :

Sélectionnez l’ **Abonnement** et le **Groupe de ressources** dans lequel le groupe d’actions est enregistré.

Sous **Détails de l’instance**  :

1. Entrez un **Nom de groupe d’actions** .

1. Entrez un **Nom d’affichage** . Le nom d’affichage est utilisé à la place du nom complet du groupe d’actions lorsque les notifications sont envoyées à l’aide de ce groupe.

      ![La boîte de dialogue « Ajouter un groupe d’actions »](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Configurer les notifications

1. Cliquez sur le bouton **Suivant : Notifications >** pour accéder à l’onglet **Notifications** , ou sélectionnez l’onglet **Notifications** en haut de l’écran.

1. Définissez une liste de notifications à envoyer quand une alerte est déclenchée. Fournissez les informations suivantes pour chaque notification :

    a. **Type de notification**  : Sélectionnez le type de notification que vous souhaitez envoyer. Options disponibles :
      * Envoyer un e-mail à un rôle Azure Resource Manager - Envoyer un e-mail aux utilisateurs affectés à certains rôles ARM au niveau abonnement.
      * E-mail/SMS/Push/Voice - Envoie ces types de notifications à des destinataires spécifiques.
    
    b. **Name**  : entrez un nom pour la notifications.

    c. **Détails** : en fonction du type de notification sélectionné, entrez une adresse e-mail, un numéro de téléphone, etc.
    
    d. **Schéma d’alerte courant** : Vous pouvez également utiliser le [schéma d’alerte commun](./alerts-common-schema.md), qui offre l’avantage de générer une seule charge utile d’alerte extensible et unifiée sur tous les services d’alerte dans Azure Monitor.

    ![Le volet Notifications](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Configurer les actions

1. Cliquez sur le bouton **Suivant : Actions >** pour accéder à l’onglet **Actions** , ou sélectionnez l’onglet **Actions** en haut de l’écran.

1. Définissez une liste d’actions à déclencher quand une alerte est déclenchée. Fournissez les informations suivantes pour chaque action :

    a. **Type d’action** : sélectionnez Automation Runbook, Azure Function, ITSM, Logic App, Secure Webhook, Webhook.
    
    b. **Nom**  : entrez un nom unique pour l’action.

    c. **Détails** : selon le type d’action, saisissez un URI de Webhook, une application Azure, une connexion ITSM ou un runbook Automation. Pour ITSM Action, spécifiez l’ **élément de travail** et les autres champs nécessaires à votre outil ITSM.
    
    d. **Schéma d’alerte courant** : Vous pouvez également utiliser le [schéma d’alerte commun](./alerts-common-schema.md), qui offre l’avantage de générer une seule charge utile d’alerte extensible et unifiée sur tous les services d’alerte dans Azure Monitor.
    
    ![Onglet Actions](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>Créer le groupe d’actions

1. Si vous le souhaitez, vous pouvez explorer les paramètres **Balises** . Cela vous permet d’associer des paires clé/valeur au groupe d’actions pour votre catégorisation. Cette fonctionnalité est disponible pour toutes les ressources Azure.

    ![Onglet Balises](./media/action-groups/action-group-4-tags.png)
    
1. Cliquez sur **Vérifier + créer** pour passer en revue les paramètres. Cela permet de valider rapidement vos entrées pour vous assurer que tous les champs obligatoires sont sélectionnés. En cas de problème, ceux-ci seront signalés ici. Après avoir examiné les paramètres, cliquez sur **Créer** pour approvisionner le groupe d’actions.
    
    ![Onglet Vérifier + créer](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> Quand vous configurez une action pour notifier une personne par e-mail ou SMS, cette personne reçoit une confirmation indiquant qu’elle a été ajoutée au groupe d’actions.

## <a name="manage-your-action-groups"></a>Gérer des groupes d’actions

Une fois que vous avez créé un groupe d’actions, vous pouvez voir les **groupes d’actions** en sélectionnant **Gérer les actions** dans la page d’arrivée **Alertes** du volet **Monitor** . Sélectionnez le groupe d’actions que vous souhaitez gérer pour :

* Ajouter, modifier ou supprimer des actions.
* Supprimer le groupe d’actions.

## <a name="action-specific-information"></a>Informations spécifiques sur l’action

> [!NOTE]
> Consultez [Subscription Service Limits for Monitoring (Limites du service d’abonnement pour la surveillance)](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) pour connaître les limites numériques sur chacun des éléments ci-dessous.  

### <a name="automation-runbook"></a>Runbook Automation
Consultez l’article [Limites du service d’abonnement Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) pour connaître les limites de charges utiles Runbook.

Vous pouvez avoir un nombre limité d’actions de runbook par groupe d’actions. 

### <a name="azure-app-push-notifications"></a>Notifications Push Azure App
Vous pouvez avoir un nombre limité d’actions d’application Azure par groupe d’actions.

### <a name="email"></a>E-mail
Les e-mails seront envoyés à partir des adresses e-mail suivantes. Vérifiez que le filtrage e-mail est correctement configuré
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Vous pouvez avoir un nombre limité d’actions d’e-mail par groupe d’actions. Consultez l’article [Informations de limitation du débit](./alerts-rate-limiting.md).

### <a name="email-azure-resource-manager-role"></a>Envoyer un message au rôle Azure Resource Manager
Envoyer un message aux membres du rôle de l’abonnement. L’e-mail sera envoyé uniquement aux **utilisateurs Azure AD** membres du rôle. L’e-mail ne sera pas envoyé aux groupes ou principaux du service Azure AD.

Un e-mail de notification est envoyé uniquement à l’ *adresse e-mail principale* .

Vous pouvez avoir un nombre limité d’actions d’e-mail par groupe d’actions. Consultez l’article [Informations de limitation du débit](./alerts-rate-limiting.md).

### <a name="function"></a>Fonction
Appelle un point de terminaison de déclencheur HTTP existant dans [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

Vous pouvez avoir un nombre limité d’actions de fonction par groupe d’actions.

### <a name="itsm"></a>ITSM
ITSM Action requiert une connexion ITSM. Découvrez comment créer une [connexion ITSM](./itsmc-overview.md).

Vous pouvez avoir un nombre limité d’actions ITSM par groupe d’actions. 

### <a name="logic-app"></a>Application logique
Vous pouvez avoir un nombre limité d’actions d’application logique par groupe d’actions.

### <a name="secure-webhook"></a>Webhook sécurisé
L’action de webhook de groupes d’actions vous permet de tirer parti d’Azure Active Directory pour sécuriser la connexion entre votre groupe d’actions et votre API web protégée (point de terminaison webhook). Le workflow global pour tirer parti de cette fonctionnalité est décrit ci-dessous. Pour obtenir une vue d’ensemble des applications Azure AD et des principaux de service, consultez [Présentation de la plateforme d’identités Microsoft (v2.0)](../../active-directory/develop/v2-overview.md).

1. Créez une application Azure AD pour votre API web protégée. Voir [API web protégée : Inscription d’application](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Configurez votre API protégée pour qu’elle soit [appelée par une application démon](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Activez les groupes d’actions pour utiliser votre application Azure AD.

    > [!NOTE]
    > Pour exécuter ce script, vous devez être membre du [rôle d’administrateur de l’application Azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles).
    
    - Modifiez l’appel Connect-AzureAD du script PowerShell pour utiliser votre ID de locataire Azure AD.
    - Modifiez la variable du script PowerShell $myAzureADApplicationObjectId pour utiliser l’ID d’objet de votre application Azure AD.
    - Exécutez le script modifié.
    
3. Configurez l’action de webhook sécurisé du groupe d’actions.
    - Copiez la valeur $myApp.ObjectId à partir du script et entrez-la dans le champ de l’ID d’objet d’application dans la définition de l’action de webhook.
    
    ![Action de webhook sécurisé](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Script PowerShell de webhook sécurisé

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>sms
Pour obtenir d’autres informations importantes, consultez [Informations de limitation du débit](./alerts-rate-limiting.md) et [Comportement des alertes SMS](./alerts-sms-behavior.md). 

Vous pouvez avoir un nombre limité d’actions SMS par groupe d’actions.

> [!NOTE]
> Si l’interface utilisateur du groupe d’actions sur le portail Azure ne vous permet pas de sélectionner votre indicatif de pays/région, cela signifie que les SMS ne sont pas pris en charge pour votre pays/région.  Si votre indicatif de pays/région n’est pas disponible, vous pouvez voter pour que votre pays/région soit ajouté sur la page d’[UserVoice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). En attendant, une solution consiste à demander à votre groupe d’actions d’appeler un webhook auprès d’un fournisseur SMS tiers prenant en charge votre pays/région.  

La tarification pour les pays/régions pris en charge est indiquée dans la [page de tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
  

### <a name="voice"></a>Voix
Pour obtenir un comportement supplémentaire important, consultez l’article [Informations de limitation du débit](./alerts-rate-limiting.md).

Vous pouvez avoir un nombre limité d’actions de voix par groupe d’actions.

> [!NOTE]
> Si l’interface utilisateur du groupe d’actions sur le portail Azure ne vous permet pas de sélectionner votre indicatif de pays/région, cela signifie que les appels vocaux ne sont pas pris en charge pour votre pays/région. Si votre indicatif de pays/région n’est pas disponible, vous pouvez voter pour que votre pays/région soit ajouté sur la page d’[UserVoice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  En attendant, une solution consiste à demander à votre groupe d’actions d’appeler un webhook auprès d’un fournisseur d’appels vocaux tiers prenant en charge votre pays/région.  

La tarification pour les pays/régions pris en charge est indiquée dans la [page de tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>webhook
Les Webhooks sont exécutés avec les règles suivantes
- Un appel de webhook est tenté au maximum 3 fois.
- L’appel sera retenté si aucune réponse n’est reçue dans le délai imparti ou si l’un des codes d’état HTTP suivants est renvoyé : 408, 429, 503 ou 504.
- Le premier appel attendra 10 secondes pour une réponse.
- Les deuxième et troisième tentatives attendent 30 secondes pour une réponse.
- Une fois que les 3 tentatives d’appel du webhook ont échoué, aucun groupe d’actions n’appellera le point de terminaison pendant 15 minutes.

Plage d’adresses IP sources
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Pour recevoir des mises à jour sur les modifications apportées à ces adresses IP, nous vous recommandons de configurer une alerte Service Health qui surveille les notifications d’information relatives au service Groupes d’actions.

Vous pouvez avoir un nombre limité d’actions de webhook par groupe d’actions.

### <a name="service-tag"></a>Étiquette du service
Une balise de service représente un groupe de préfixes d’adresses IP d’un service Azure donné. Microsoft gère les préfixes d’adresses inclus dans l’étiquette de service et met à jour automatiquement l’étiquette de service quand les adresses changent, ce qui réduit la complexité des mises à jour fréquentes des règles de sécurité réseau pour un ActionGroup.

1. Dans le portail Azure, sous services Azure, recherchez *Groupe de sécurité réseau* .
2. Cliquez sur **Ajouter** , puis créez un groupe de sécurité réseau.

   1. Ajoutez le nom du groupe de ressources, puis entrez les *Détails de l’instance* .
   1. Cliquez sur **Vérifier + créer** , puis sur *Créer* .
   
   :::image type="content" source="media/action-groups/action-group-create-security-group.png" alt-text="Exemple de création d’un groupe de sécurité réseau."border="true":::

3. Accédez à un Groupe de ressources, puis cliquez sur le *groupe de sécurité réseau* que vous avez créé.

    1. Sélectionnez *Règles de sécurité de trafic entrant* .
    1. Cliquez sur **Ajouter** .
    
    :::image type="content" source="media/action-groups/action-group-add-service-tag.png" alt-text="Exemple d’ajout d’étiquette de service."border="true":::

4. Une nouvelle fenêtre s’ouvre dans le volet de droite.
    1.  Sélectionnez Source : **Étiquette de service**
    1.  Étiquette de service source : **ActionGroup**
    1.  Cliquez sur **Add** .
    
    :::image type="content" source="media/action-groups/action-group-service-tag.png" alt-text="Exemple d’ajout d’étiquette de service."border="true":::

L’utilisation d’ **Étiquette de service** pour ActionGroup permet de réduire la complexité des mises à jour fréquentes des adresses IP.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le [comportement des alertes SMS](./alerts-sms-behavior.md).  
* [Comprendre le schéma Webhook des alertes du journal d’activité](./activity-log-alerts-webhook.md).  
* Découvrez-en plus sur le [connecteur ITSM](./itsmc-overview.md).
* En savoir plus sur la [limitation de la fréquence](./alerts-rate-limiting.md) des alertes.
* Obtenir une [vue d’ensemble des alertes du journal d’activité](./alerts-overview.md) et découvrir comment recevoir des alertes.  
* Découvrir comment [configurer des alertes lorsqu’une notification d’intégrité de service est publiée](../../service-health/alerts-activity-log-service-notifications-portal.md).