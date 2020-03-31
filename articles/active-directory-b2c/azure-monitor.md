---
title: Superviser Azure AD B2C avec Azure Monitor
titleSuffix: Azure AD B2C
description: Découvrez comment journaliser des événements Azure AD B2C avec Azure Monitor en utilisant la gestion des ressources déléguées.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: acba378badb41324b2124b84833407da920a0e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190056"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Superviser Azure AD B2C avec Azure Monitor

Utilisez Azure Monitor pour router les journaux de connexion et d’[audit](view-audit-logs.md) d’Azure Active Directory B2C (Azure AD B2C) vers différentes solutions de supervision. Vous pouvez ensuite conserver les journaux pour les utiliser sur le long terme ou les intégrer à des outils SIEM (Security Information and Event Management) tiers pour obtenir davantage d’insights sur votre environnement.

Vous pouvez router des événements de journal vers :

* Un [compte de stockage](../storage/blobs/storage-blobs-introduction.md) Azure.
* Un [hub d’événements](../event-hubs/event-hubs-about.md) Azure (pour les intégrer à vos instances Splunk et Sumo Logic).
* Un [espace de travail Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) (pour analyser les données, créer des tableaux de bord et générer des alertes sur des événements spécifiques).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes décrites dans cet article, vous déployez un modèle Azure Resource Manager à l’aide du module Azure PowerShell.

* [Module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) version 6.13.1 ou supérieure

Vous pouvez également utiliser [Azure Cloud Shell](https://shell.azure.com), qui comprend la version la plus récente du module Azure PowerShell.

## <a name="delegated-resource-management"></a>Gestion des ressources déléguées

Azure AD B2C tire parti de la [supervision Azure Active Directory](../active-directory/reports-monitoring/overview-monitoring.md). Pour activer les *paramètres de diagnostic* dans Azure Active Directory au sein de votre locataire Azure AD B2C, vous utilisez la [gestion des ressources déléguées](../lighthouse/concepts/azure-delegated-resource-management.md).

Vous autorisez un utilisateur ou un groupe de votre annuaire Azure AD B2C (le **fournisseur de services**) à configurer l’instance Azure Monitor au sein du locataire qui contient votre abonnement Azure (le **client**). Pour créer l’autorisation, vous déployez un modèle [Azure Resource Manager](../azure-resource-manager/index.yml) sur votre locataire Azure AD contenant l’abonnement. Les sections suivantes vous guident tout au long de ce processus.

## <a name="create-or-choose-resource-group"></a>Créer ou choisir un groupe de ressources

Il s’agit du groupe de ressources qui contient le compte de stockage Azure, le hub d’événements ou l’espace de travail Log Analytics de destination où seront stockées les données envoyées par Azure Monitor. Vous spécifiez le nom du groupe de ressources lorsque vous déployez le modèle Azure Resource Manager.

[Créez un groupe de ressources](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) ou choisissez-en un existant dans le locataire Azure Active Directory (Azure AD) qui contient votre abonnement Azure, et *non* dans l’annuaire qui contient votre locataire Azure AD B2C.

Cet exemple utilise un groupe de ressources nommé *azure-ad-b2c-monitor* dans la région *USA Centre*.

## <a name="delegate-resource-management"></a>Gestion des ressources déléguées

Ensuite, rassemblez les informations suivantes :

**ID d’annuaire** de votre annuaire Azure AD B2C (également appelé ID de locataire).

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’utilisateur doté du rôle *Administrateur d’utilisateurs* (ou d’un rôle plus élevé).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Sélectionnez **Azure Active Directory**, puis **Propriétés**.
1. Enregistrez l’**ID de l’annuaire**.

**ID d’objet** du groupe ou de l’utilisateur Azure AD B2C auquel vous voulez octroyer l’autorisation *Contributeur* sur le groupe de ressources que vous avez créé avant dans l’annuaire contenant votre abonnement.

Pour faciliter la gestion, nous vous recommandons d’utiliser des *groupes* d’utilisateurs Azure AD pour chaque rôle, ce qui vous permet d’ajouter ou de supprimer des utilisateurs dans le groupe au lieu d’attribuer directement des autorisations aux utilisateurs. Dans le cadre de cette procédure pas à pas, vous ajoutez un utilisateur.

1. Avec **Azure Active Directory** toujours sélectionné dans le portail Azure, sélectionnez **Utilisateurs**, puis sélectionnez un utilisateur.
1. Enregistrez l’**ID d’objet** de l’utilisateur.

### <a name="create-an-azure-resource-manager-template"></a>Créer un modèle Azure Resource Manager

Pour intégrer votre locataire Azure AD (le **client**), créez un [modèle Azure Resource Manager](../lighthouse/how-to/onboard-customer.md) pour votre offre avec les informations suivantes. Les valeurs `mspOfferName` et `mspOfferDescription` sont visibles quand vous consultez les détails de l’offre dans la [page Fournisseurs de services](../lighthouse/how-to/view-manage-service-providers.md) du portail Azure.

| Champ   | Définition |
|---------|------------|
| `mspOfferName`                     | Nom décrivant cette définition. Par exemple, *Services managés Azure AD B2C*. Cette valeur est présentée au client comme titre de l’offre. |
| `mspOfferDescription`              | Courte description de votre offre. Par exemple, *Active Azure Monitor dans Azure AD B2C*.|
| `rgName`                           | Nom du groupe de ressources que vous avez créé précédemment dans votre locataire Azure AD. Par exemple, *azure-ad-b2c-monitor*. |
| `managedByTenantId`                | **ID d’annuaire** de votre locataire Azure AD B2C (également appelé ID de locataire). |
| `authorizations.value.principalId` | **ID d’objet** du groupe ou de l’utilisateur B2C qui aura accès aux ressources de cet abonnement Azure. Pour cette procédure pas à pas, spécifiez l’ID d’objet de l’utilisateur que vous avez enregistré précédemment. |

Téléchargez le modèle Azure Resource Manager et les fichiers de paramètres :

- [rgDelegatedResourceManagement.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Ensuite, mettez à jour les fichiers de paramètres avec les valeurs que vous avez enregistrées précédemment. L’extrait de code JSON suivant illustre un exemple de fichier de paramètres de modèle Azure Resource Manager. Pour `authorizations.value.roleDefinitionId`, utilisez la valeur du [rôle intégré](../role-based-access-control/built-in-roles.md) du *rôle Contributeur*, `b24988ac-6180-42a0-ab88-20f7382dd24c`.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>Déployer les modèles Azure Resource Manager

Une fois que vous avez mis à jour votre fichier de paramètres, déployez le modèle Azure Resource Manager sur le locataire Azure sous forme de déploiement au niveau de l’abonnement. Comme il s’agit d’un déploiement au niveau de l’abonnement, il ne peut pas être lancé dans le Portail Azure. Vous pouvez le déployer à l’aide du module Azure PowerShell ou d’Azure CLI. La méthode Azure PowerShell est illustrée ci-dessous.

Connectez-vous à l’annuaire contenant votre abonnement à l’aide de [Connect-AzAccount](/powershell/azure/authenticate-azureps). Utilisez l’indicateur `-tenant` pour forcer l’authentification auprès du bon annuaire.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Utilisez l’applet de commande [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) pour lister les abonnements auxquels le compte actuel peut accéder sous le locataire Azure AD. Enregistrez l’ID de l’abonnement que vous souhaitez projeter dans votre locataire Azure AD B2C.

```PowerShell
Get-AzSubscription
```

Ensuite, basculez vers l’abonnement que vous souhaitez projeter dans le locataire Azure AD B2C :

``` PowerShell
Select-AzSubscription <subscription ID>
```

Enfin, déployez le modèle Azure Resource Manager et les fichiers de paramètres que vous avez précédemment téléchargés et mis à jour. Remplacez les valeurs `Location`, `TemplateFile` et `TemplateParameterFile` en conséquence.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

La réussite du déploiement du modèle produit une sortie similaire à la suivante (la sortie est tronquée par souci de concision) :

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

Une fois le modèle déployé, quelques minutes peuvent être nécessaires pour effectuer la projection des ressources. Vous devrez peut-être patienter quelques minutes (en règle générale, pas plus de cinq) avant de passer à la section suivante pour sélectionner l’abonnement.

## <a name="select-your-subscription"></a>Sélectionnez votre abonnement

Une fois que vous avez déployé le modèle et que vous avez patienté pendant quelques minutes pour que la projection des ressources s’effectue, associez votre abonnement à votre annuaire Azure AD B2C à l’aide des étapes suivantes.

1. **Déconnectez-vous** du portail Azure si vous êtes actuellement connecté. Cette étape et la suivante sont effectuées pour actualiser vos informations d’identification dans la session du portail.
1. Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte d’administrateur Azure AD B2C.
1. Sélectionnez l’icône **Répertoire + abonnement** dans la barre d’outils du portail.
1. Sélectionnez le répertoire qui contient votre abonnement.

    ![Changer d’annuaire](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Vérifiez que vous avez sélectionné l’annuaire et l’abonnement appropriés. Dans cet exemple, tous les annuaires et abonnements sont sélectionnés.

    ![Option Tous les annuaires sélectionnée dans le filtre Annuaire et abonnement](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Configurer les paramètres de diagnostic

Les paramètres de diagnostic définissent où les journaux et les métriques d’une ressource doivent être envoyés. Les destinations possibles sont les suivantes :

- [Compte Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md)
- [Hub d’événements](../azure-monitor/platform/resource-logs-stream-event-hubs.md)
- [Espace de travail Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md)

Si vous ne l’avez pas encore fait, créez une instance du type de destination de votre choix dans le groupe de ressources que vous avez spécifié dans le [modèle Azure Resource Manager](#create-an-azure-resource-manager-template).

### <a name="create-diagnostic-settings"></a>Créer des paramètres de diagnostic

Vous êtes maintenant prêt à [créer des paramètres de diagnostic](../active-directory/reports-monitoring/overview-monitoring.md) dans le portail Azure.

Pour configurer les paramètres de supervision des journaux d’activité Azure AD B2C :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Sélectionnez **Azure Active Directory**.
1. Sous **Supervision**, sélectionnez **Paramètres de diagnostic**.
1. S’il des paramètres sont définis pour la ressource, la liste des paramètres configurés s’affiche. Sélectionnez **Ajouter un paramètre de diagnostic** pour ajouter un paramètre, ou **Modifier le paramètre** pour modifier un paramètre existant. Chaque paramètre doit avoir un seul type de destination.

    ![Volet Paramètres de diagnostic dans le portail Azure](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Si votre paramètre n’a pas de nom, nommez-le.
1. Cochez la case correspondant à chaque destination à laquelle envoyer les journaux. Sélectionnez **Configurer** pour spécifier les paramètres comme décrit dans le tableau suivant.

    | Paramètre | Description |
    |:---|:---|
    | Archiver dans un compte de stockage | Nom du compte de stockage. |
    | Diffuser vers un hub d’événements | Espace de noms dans lequel le hub d’événements est créé (si c’est la première fois que vous diffusez en continu des journaux) ou vers lequel il est diffusé (si des ressources diffusent déjà cette catégorie de journal d’activité vers cet espace de noms).
    | Envoyer à Log Analytics | Nom de l’espace de travail. |

1. Sélectionnez **AuditLogs** et **SignInLogs**.
1. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’ajout et la configuration de paramètres de diagnostic dans Azure Monitor, consultez le [Tutoriel : Collecter et analyser des journaux de ressources à partir d’une ressource Azure](../azure-monitor/insights/monitor-azure-resource.md).

Pour plus d’informations sur le streaming des journaux Azure AD vers un hub d’événements, consultez le [Tutoriel : Envoyer les journaux Azure Active Directory vers un hub d’événements Azure](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
