---
title: Configurer une instance et l’authentification (PowerShell)
titleSuffix: Azure Digital Twins
description: Découvrez comment configurer une instance du service Azure Digital Twins à l’aide de PowerShell
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c52cd013d1a5d2cbde0ab88eac24d2fd3ef77d46
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110613524"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Configurer une instance Azure Digital Twins et l’authentification (PowerShell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Cet article explique comment **configurer une nouvelle instance Azure Digital Twins**, notamment la création de l’instance et la configuration de l’authentification. À l’issue de cet article, vous aurez une instance Azure Digital Twins prête pour la programmation.

Cette version de cet article suit ces étapes manuellement, une par une, à l’aide d’[Azure PowerShell](/powershell/azure/new-azureps-module-az).

* Pour suivre ces étapes manuellement à l’aide du portail Azure, consultez la version de cet article relative au portail : [Guide pratique : Configurer une instance et l’authentification (portail)](how-to-set-up-instance-portal.md) .
* Pour exécuter une configuration automatisée à l’aide d’un exemple de script de déploiement, consultez la version avec script de cet article : [Guide pratique : Configurer une instance et l’authentification (procédure scriptée)](how-to-set-up-instance-scripted.md) .

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

## <a name="prepare-your-environment"></a>Préparation de votre environnement

1. Tout d’abord, choisissez où exécuter les commandes indiquées dans cet article. Vous pouvez choisir d’exécuter des commandes Azure PowerShell à l’aide d’une installation locale d’Azure PowerShell ou dans une fenêtre de navigateur à l’aide d’[Azure Cloud Shell](https://shell.azure.com).
    * Si vous choisissez d’utiliser Azure PowerShell localement :
       1. [Installez le module PowerShell Az](/powershell/azure/install-az-ps).
       1. Ouvrez une fenêtre PowerShell sur votre ordinateur.
       1. Connectez-vous à votre compte Azure à l’aide de la cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).
    * Si vous choisissez d’utiliser Azure Cloud Shell :
        1. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Cloud Shell](../cloud-shell/overview.md).
        1. [Ouvrez une fenêtre Cloud Shell dans votre navigateur](https://shell.azure.com).
        1. Dans la barre d’icônes Cloud Shell, vérifiez que Cloud Shell est configuré pour exécuter la version PowerShell.
    
            :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Capture d’écran de la fenêtre Cloud Shell dans le portail Azure, montrant la sélection de la version de PowerShell":::
    
1. Si vous avez plusieurs abonnements Azure, sélectionnez l’abonnement approprié dans lequel les ressources doivent être facturées. Sélectionnez un abonnement spécifique avec l’applet de commande [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Si c’est la première fois que vous utilisez Azure Digital Twins avec cet abonnement, vous devez inscrire le fournisseur de ressources **Microsoft.DigitalTwins**. (En cas de doute, il est possible de la réexécuter même si vous l’avez fait par le passé.)

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

1. Utilisez la commande suivante pour installer le module PowerShell **Az.DigitalTwins**.
    ```azurepowershell-interactive
    Install-Module -Name Az.DigitalTwins
    ```

> [!IMPORTANT]
> Tant que le module PowerShell **Az.DigitalTwins** est en préversion, vous devez l’installer séparément à l’aide de l’applet de commande `Install-Module`, comme décrit ci-dessus. Une fois que ce module PowerShell sera en disponibilité générale, il fera partie intégrante des versions futures du module PowerShell Az et sera disponible par défaut dans Azure Cloud Shell.

## <a name="create-the-azure-digital-twins-instance"></a>Créer l’instance Azure Digital Twins

Dans cette section, vous allez **créer une nouvelle instance d’Azure Digital Twins** à l’aide d’Azure PowerShell.
Vous devrez fournir les éléments suivants :

* Un [groupe de ressources Azure](../azure-resource-manager/management/overview.md) où l’instance sera déployée. Si vous n’avez pas encore de groupe de ressources, vous pouvez en créer un avec l’applet de commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* Une région pour le déploiement. Pour connaître les régions qui prennent en charge Azure Digital Twins, visitez [Disponibilité des produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Un nom pour votre instance. Le nom de la nouvelle instance doit être unique dans la région de votre abonnement. Si votre abonnement a une autre instance Azure Digital Twins dans la région qui utilise déjà le nom spécifié, vous êtes invité à choisir un autre nom.

Utilisez vos valeurs dans la commande suivante pour créer l’instance :

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>Vérifier la réussite de l’exécution et collecter les valeurs importantes

Si l’instance a été créée avec succès, le résultat ressemble à la sortie suivante, qui contient des informations sur la ressource que vous avez créée :

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

Ensuite, affichez les propriétés de votre nouvelle instance en exécutant `Get-AzDigitalTwinsInstance` et en la canalisant vers `Select-Object -Property *`, comme suit :

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

> [!TIP]
> Vous pouvez utiliser cette commande pour afficher toutes les propriétés de votre instance à tout moment.

Notez les valeurs **host name**, **name** et **resource group** de l’instance Azure Digital Twins. Il s’agit de valeurs importantes dont vous pouvez avoir besoin quand vous continuez à travailler avec votre instance Azure Digital Twins pour configurer l’authentification et les ressources Azure associées. Si d’autres utilisateurs doivent programmer pour l’instance, vous devez partager ces valeurs avec eux.

Vous disposez maintenant d’une instance Azure Digital Twins opérationnelle. Ensuite, vous allez accorder les autorisations utilisateur Azure appropriées pour la gérer.

## <a name="set-up-user-access-permissions"></a>Configurer les autorisations d’accès utilisateur

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

### <a name="prerequisites-permission-requirements"></a>Configuration requise : Spécifications relatives aux autorisations
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

### <a name="assign-the-role"></a>Attribuer le rôle

Pour accorder à un utilisateur les autorisations nécessaires pour gérer une instance Azure Digital Twins, vous devez lui attribuer le rôle _**Propriétaire de données Azure Digital Twins**_ au sein de l’instance.

Tout d’abord, déterminez l’**ObjectId** du compte Azure AD de l’utilisateur qui doit recevoir le rôle. Vous pouvez trouver cette valeur à l’aide de l’applet de commande [Get-AzAdUser](/powershell/module/az.resources/get-azaduser) en transmettant le nom d’utilisateur principal sur le compte Azure AD pour récupérer son ObjectId (et d’autres informations utilisateur). Dans la plupart des cas, le nom d’utilisateur principal correspond à l’adresse e-mail de l’utilisateur sur le compte Azure AD.

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

Ensuite, utilisez l’**ObjectId** dans la commande suivante pour attribuer le rôle. Dans la commande, vous devez également entrer les mêmes ID d’abonnement, nom de groupe de ressources et nom d’instance Azure Digital Twins que ceux que vous avez choisis précédemment lors de la création de l’instance. La commande doit être exécutée par un utilisateur doté d’[autorisations suffisantes](#prerequisites-permission-requirements) dans l’abonnement Azure.

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-ID-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

Les résultats de cette commande décrivent l’attribution de rôle que vous avez créée.

### <a name="verify-success"></a>Vérifier la réussite de l’exécution

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Vous disposez maintenant d’une instance Azure Digital Twins prête à l’emploi et des autorisations pour la gérer.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment connecter une application cliente à votre instance avec un code d’authentification :
* [Guide pratique : Écrire le code d’authentification de l’application](how-to-authenticate-client.md)
