---
title: Comment gérer les affectations avec PowerShell
description: Découvrez comment gérer les affectations de plan avec le module PowerShell de plans Azure officielle, Az.Blueprint.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d8eacffe4b792eda5d81051f6aa65caa3292c896
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256870"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Comment gérer les affectations avec PowerShell

Une affectation de plan peut être gérée à l’aide de la **Az.Blueprint** module Azure PowerShell. Le module prend en charge l’extraction, de création, de la mise à jour et de suppression des affectations. Le module peut également extraire des détails sur les définitions de plan existant. Cet article explique comment installer le module et l’utiliser.

## <a name="add-the-azblueprint-module"></a>Ajouter le module Az.Blueprint

Pour activer Azure PowerShell pour gérer les affectations de plan, le module doit être ajouté. Vous pouvez utiliser ce module avec PowerShell installé localement, avec [Azure Cloud Shell](https://shell.azure.com) ou avec l’[image Docker Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Configuration de base requise

Le module de plans d’Azure nécessite les logiciels suivants :

- Azure PowerShell 1.5.0 ou une version ultérieure. S’il n’est pas installé, suivez [ces instructions](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 ou une version ultérieure. S’il n’est pas installé ou mis à jour, suivez [ces instructions](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Installer le module

Le module de plans pour PowerShell est **Az.Blueprint**.

1. À partir d’une invite PowerShell **d’administration**, exécutez la commande suivante :

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Si **Az.Accounts** est déjà installé, il peut être nécessaire d’utiliser `-AllowClobber` pour forcer l’installation.

1. Vérifiez que le module a été importé et que sa version est correcte (0.1.0) :

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Obtenir les définitions de plan

La première étape pour travailler avec une affectation est bien souvent une référence à une définition de plan.
Le `Get-AzBlueprint` applet de commande Obtient une ou plusieurs définitions de plan. L’applet de commande peut obtenir les définitions de plan à partir d’un groupe d’administration avec `-ManagementGroupId {mgId}` ou un abonnement avec `-SubscriptionId {subId}`. Le **nom** paramètre Obtient une définition de plan, mais doit être utilisé avec **ManagementGroupId** ou **SubscriptionId**. **Version** peut être utilisé avec **nom** pour être plus explicite sur la définition de plan qui est retournée. Au lieu de **Version**, le commutateur `-LatestPublished` extrait le plus récemment les version publiée.

L’exemple suivant utilise `Get-AzBlueprint` pour obtenir toutes les versions d’une définition de plan nommé « 101-plans-definition-subscription » à partir d’un abonnement spécifique représenté sous la forme `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

L’exemple de sortie pour une définition de plan avec plusieurs versions ressemble à ceci :

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

Le [blueprint paramètres](../concepts/parameters.md#blueprint-parameters) sur le plan en définition peut être développée pour fournir plus d’informations.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Obtenir l’attribution de plan

Si la plan attribution existe déjà, vous pouvez obtenir une référence à celle-ci avec le `Get-AzBlueprintAssignment` applet de commande. L’applet de commande prend **SubscriptionId** et **nom** en tant que paramètres facultatifs. Si **SubscriptionId** n’est pas spécifié, le contexte de l’abonnement actuel est utilisé.

L’exemple suivant utilise `Get-AzBlueprintAssignment` pour obtenir une affectation de plan unique nommée « Affectation-verrou-resource-groups » à partir d’un abonnement spécifique représenté sous la forme `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

L’exemple de sortie pour une affectation de plan se présente comme suit :

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Créer des attributions de plan

Si l’affectation de plan n’existe pas encore, vous pouvez le créer avec le `New-AzBlueprintAssignment` applet de commande. Cette applet de commande utilise les paramètres suivants :

- **Name** [required]
  - Spécifie le nom de l’affectation de plan
  - Doit être unique et n’existe pas déjà dans **SubscriptionId**
- **Blueprint** [required]
  - Spécifie la définition de plan à affecter
  - Utilisez `Get-AzBlueprint` pour obtenir l’objet de référence
- **Emplacement** [requis]
  - Spécifie la région pour l’attribué par le système géré identité et abonnement objet de déploiement doit être créé dans
- **Abonnement** (facultatif)
  - Spécifie l’abonnement de sur que l’attribution est déployée
  - Si ne pas fournie, valeur par défaut est le contexte actuel de l’abonnement
- **Verrou** (facultatif)
  - Définit le [blueprint verrouillage de ressources](../concepts/resource-locking.md) à utiliser pour les ressources déployées
  - Options prises en charge : _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Si ne pas fournie, valeur par défaut est _None_
- **SystemAssignedIdentity** (optional)
  - Sélectionnez pour créer une identité gérée attribué par le système pour l’attribution et de déployer les ressources
  - Valeur par défaut pour le jeu de paramètres « identité »
  - Ne peut pas être utilisé avec **UserAssignedIdentity**
- **UserAssignedIdentity** (optional)
  - Spécifie l’identité gérée affectée à l’utilisateur à utiliser pour l’attribution et à déployer les ressources
  - Partie de l’ensemble de paramètres « identité »
  - Ne peut pas être utilisé avec **SystemAssignedIdentity**
- **Paramètre** (facultatif)
  - Un [table de hachage](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de paires clé/valeur pour le paramètre [paramètres dynamiques](../concepts/parameters.md#dynamic-parameters) sur l’affectation de plan
  - Par défaut pour un paramètre dynamique est la **defaultValue** dans la définition
  - Si un paramètre n’est pas fourni et n’a aucun **defaultValue**, le paramètre n’est pas facultatif

    > [!NOTE]
    > **Paramètre** ne prend pas en charge secureStrings.

- **ResourceGroupParameter** (facultatif)
  - Un [table de hachage](/powershell/module/microsoft.powershell.core/about/about_hash_tables) d’artefacts de groupe de ressources
  - Chaque espace réservé d’artefact de ressource groupe aura une paires clé/valeur pour définir dynamiquement **nom** et/ou **emplacement** sur cet artefact de groupe de ressources
  - Si un paramètre de groupe de ressources n’est pas fourni et qu’il n’a aucun **defaultValue**, le paramètre de groupe de ressources n’est pas facultatif

L’exemple suivant crée une nouvelle attribution de la version « 1.1 » de la définition de plan de « mon plan » récupérée avec `Get-AzBlueprint`, définit l’emplacement d’objet identity et l’affectation managé à 'westus2', verrouille les ressources avec  _AllResourcesReadOnly_et définit les tables de hachage pour les deux **paramètre** et **ResourceGroupParameter** sur un abonnement spécifique représenté sous la forme `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

L’exemple de sortie pour la création d’une affectation de plan se présente comme suit :

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadyOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="update-blueprint-assignments"></a>Attribution de plan de mise à jour

Il est parfois nécessaire mettre à jour d’une affectation de plan qui a déjà été créée. Le `Set-AzBlueprintAssignment` applet de commande gère cette action. L’applet de commande utilise une grande partie des mêmes paramètres que le `New-AzBlueprintAssignment` applet de commande ne, ce qui permet de tout ce qui a été défini sur l’attribution de mise à jour. Les exceptions à ce sont les _nom_, _Blueprint_, et _SubscriptionId_. Seules les valeurs fournies sont mises à jour.

Pour comprendre ce qui se passe lors de la mise à jour d’une affectation de plan, consultez [règles de mise à jour des affectations de](./update-existing-assignments.md#rules-for-updating-assignments).

- **Name** [required]
  - Spécifie le nom de l’affectation de plan pour mettre à jour
  - Utilisé pour localiser l’attribution de mise à jour, ne pas pour modifier l’affectation
- **Blueprint** [required]
  - Spécifie la définition de plan de l’affectation de plan
  - Utilisez `Get-AzBlueprint` pour obtenir l’objet de référence
  - Utilisé pour localiser l’attribution de mise à jour, ne pas pour modifier l’affectation
- **Emplacement** (facultatif)
  - Spécifie la région pour l’attribué par le système géré identité et abonnement objet de déploiement doit être créé dans
- **Abonnement** (facultatif)
  - Spécifie l’abonnement de sur que l’attribution est déployée
  - Si ne pas fournie, valeur par défaut est le contexte actuel de l’abonnement
  - Utilisé pour localiser l’attribution de mise à jour, ne pas pour modifier l’affectation
- **Verrou** (facultatif)
  - Définit le [blueprint verrouillage de ressources](../concepts/resource-locking.md) à utiliser pour les ressources déployées
  - Options prises en charge : _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (optional)
  - Sélectionnez pour créer une identité gérée attribué par le système pour l’attribution et de déployer les ressources
  - Valeur par défaut pour le jeu de paramètres « identité »
  - Ne peut pas être utilisé avec **UserAssignedIdentity**
- **UserAssignedIdentity** (optional)
  - Spécifie l’identité gérée affectée à l’utilisateur à utiliser pour l’attribution et à déployer les ressources
  - Partie de l’ensemble de paramètres « identité »
  - Ne peut pas être utilisé avec **SystemAssignedIdentity**
- **Paramètre** (facultatif)
  - Un [table de hachage](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de paires clé/valeur pour le paramètre [paramètres dynamiques](../concepts/parameters.md#dynamic-parameters) sur l’affectation de plan
  - Par défaut pour un paramètre dynamique est la **defaultValue** dans la définition
  - Si un paramètre n’est pas fourni et n’a aucun **defaultValue**, le paramètre n’est pas facultatif

    > [!NOTE]
    > **Paramètre** ne prend pas en charge secureStrings.

- **ResourceGroupParameter** (facultatif)
  - Un [table de hachage](/powershell/module/microsoft.powershell.core/about/about_hash_tables) d’artefacts de groupe de ressources
  - Chaque espace réservé d’artefact de ressource groupe aura une paires clé/valeur pour définir dynamiquement **nom** et/ou **emplacement** sur cet artefact de groupe de ressources
  - Si un paramètre de groupe de ressources n’est pas fourni et qu’il n’a aucun **defaultValue**, le paramètre de groupe de ressources n’est pas facultatif

L’exemple suivant met à jour l’attribution de la version « 1.1 » de la définition de plan de « mon plan » récupérée avec `Get-AzBlueprint` en modifiant le mode de verrouillage :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

L’exemple de sortie pour la création d’une affectation de plan se présente comme suit :

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Supprimer l’attribution de plan

Lorsqu’il est temps pour une affectation de plan à supprimer, le `Remove-AzBlueprintAssignment` applet de commande gère cette action. L’applet de commande prend soit **nom** ou **InputObject** pour spécifier quel plan attribution à supprimer. **ID d’abonnement** est _requis_ et doit être fourni dans tous les cas.

L’exemple suivant extrait une affectation de plan existant avec `Get-AzBlueprintAssignment` , puis le supprime de l’abonnement spécifique représenté sous la forme `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Exemple de code de bout en bout

Rassemble toutes les étapes, l’exemple suivant obtient la définition de plan, puis crée, met à jour et supprime une affectation de plan dans l’abonnement spécifique représenté sous la forme `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrir le [cycle de vie d’un blueprint](../concepts/lifecycle.md).
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../concepts/resource-locking.md).
- Résoudre les problèmes durant l’affectation d’un blueprint en suivant les étapes de [dépannage général](../troubleshoot/general.md).