---
title: Activer Azure DS Domain Services à l’aide de PowerShell | Microsoft Docs
description: Découvrez comment configurer et activer Azure Active Directory Domain Services via Azure AD PowerShell et Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 9c2345c93a163464ea735400c9269e2e3fc27ecf
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87488165"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Activer Azure Active Directory Domain Services à l’aide de PowerShell

Azure Active Directory Domain Services (Azure AD DS) fournit des services de domaine managés, comme la jonction de domaine, la stratégie de groupe, le protocole LDAP, et l’authentification Kerberos/NTLM entièrement compatible avec Windows Server Active Directory. Vous consommez ces services de domaine sans déployer, gérer et mettre à jour avec des correctifs les contrôleurs de domaine vous-même. Azure AD DS s’intègre à votre locataire Azure AD existant. Cette intégration permet aux utilisateurs de se connecter en utilisant leurs informations d’identification d’entreprise, et vous pouvez utiliser des groupes et des comptes d’utilisateur existants pour sécuriser l’accès aux ressources.

Cet article vous montre comment activer Azure à l’aide de PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des ressources suivantes :

* Installez et configurez Azure PowerShell.
    * Si nécessaire, suivez les instructions pour [installer le module Azure PowerShell et vous connecter à votre abonnement Azure](/powershell/azure/install-az-ps).
    * Veillez à vous connecter à votre abonnement Azure à l’aide de l’applet de commande [Connect-AzAccount][Connect-AzAccount].
* Installez et configurez Azure AD PowerShell.
    * Si nécessaire, suivez les instructions pour [installer le module Azure AD PowerShell et vous connecter à Azure AD](/powershell/azure/active-directory/install-adv2).
    * Veillez à vous connecter à votre locataire Azure AD à l’aide de l’applet de commande [Connect-AzureAD][Connect-AzureAD].
* Vous devez disposer des privilèges d’*Administrateur global* dans votre locataire Azure AD pour activer Azure AD DS.
* Vous avez besoin de privilèges de *Contributeur* dans votre abonnement Azure pour créer les ressources Azure AD DS nécessaires.

## <a name="create-required-azure-ad-resources"></a>Créer les ressources Azure AD nécessaires

Azure AD DS nécessite un principal du service et un groupe Azure AD. Ces ressources permettent au domaine managé Azure AD DS de synchroniser les données et de définir les utilisateurs qui possèdent des autorisations administratives dans le domaine managé.

Tout d’abord, créez un principal du service Azure AD pour permettre à Azure AD DS de communiquer et de s’authentifier. Un ID d’application spécifique est utilisé. Il se nomme *Domain Controller Services* et son ID est *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Ne modifiez pas cet ID d’application.

Créez un principal du service Azure AD à l’aide de l’applet de commande [New-AzureADServicePrincipal][New-AzureADServicePrincipal] :

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Créez ensuite un groupe Azure AD nommé *AAD DC Administrators*. Les utilisateurs ajoutés à ce groupe se voient ensuite accorder des autorisations pour effectuer des tâches d’administration dans le domaine managé.

Créez le groupe *AAD DC Administrators* à l’aide de l’applet de commande [New-AzureADGroup][New-AzureADGroup] :

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Une fois le groupe *AAD DC Administrators* créé, ajoutez un utilisateur au groupe à l’aide de l’applet de commande [Add-AzureADGroupMember][Add-AzureADGroupMember]. Vous obtenez d’abord l’ID d’objet du groupe *AAD DC Administrators* via l’applet de commande [Get-AzureADGroup][Get-AzureADGroup] et ensuite l’ID d’objet de l’utilisateur souhaité via l’applet de commande [Get-AzureADUser][Get-AzureADUser].

Dans l’exemple suivant, l’ID d’objet utilisateur du compte a le nom d’utilisateur principal (UPN) `admin@contoso.onmicrosoft.com`. Remplacez ce compte d’utilisateur par l’UPN de l’utilisateur que vous souhaitez ajouter au groupe *AAD DC Administrators* :

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Créer des ressources Azure de support

Dans un premier temps, inscrivez le fournisseur de ressources Azure AD Domain Services à l’aide de l’applet de commande [Register-AzResourceProvider][Register-AzResourceProvider] :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Créez ensuite un groupe de ressources avec l’applet de commande [New-AzResourceGroup][New-AzResourceGroup]. Dans l’exemple suivant, le groupe de ressources est nommé *myResourceGroup* et est créé dans la région *westus*. Utilisez votre propre nom et la région souhaitée :

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Créez le réseau virtuel et des sous-réseaux pour Azure AD Domain Services. Deux sous-réseaux sont créés : un pour *DomainServices* et un autre pour *Workloads*. Azure AD DS est déployé dans le sous-réseau dédié *DomainServices*. Ne déployez pas d’autres applications ou charges de travail dans ce sous-réseau. Utilisez le sous-réseau indépendant *Workloads* ou d’autres sous-réseaux pour le reste de vos machines virtuelles.

Créez les sous-réseaux à l’aide de l’applet de commande [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig], puis créez le réseau virtuel à l’aide de l’applet de commande [New-AzVirtualNetwork][New-AzVirtualNetwork].

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

## <a name="create-a-managed-domain"></a>Créer un domaine managé

Créons maintenant un domaine managé. Définissez votre ID d’abonnement Azure, puis attribuez un nom au domaine managé, par exemple *aaddscontoso.com*. Vous pouvez obtenir votre ID d’abonnement à l’aide de l’applet de commande [Get-AzSubscription][Get-AzSubscription].

Si vous choisissez une région qui prend en charge les Zones de disponibilité, les ressources Azure AD DS sont réparties entre les zones pour assurer une redondance supplémentaire.

Les Zones de disponibilité sont des emplacements physiques uniques au sein d’une région Azure. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. Pour garantir la résilience, un minimum de trois zones distinctes sont activées dans toutes les régions.

Vous ne devez rien configurer pour la répartition d’Azure AD DS entre les zones. La plateforme Azure gère automatiquement la répartition de zone des ressources. Pour plus d’informations et pour connaître la disponibilité régionale, voir [Zones de disponibilité dans Azure][availability-zones].

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Créer la ressource et retourner le contrôle à l’invite PowerShell prend quelques minutes. Le provisionnement du domaine managé se poursuit en arrière-plan et le déploiement peut prendre jusqu’à une heure. Dans le portail Azure, la page **Vue d’ensemble** de votre domaine managé indique l’état actuel tout au long de cette phase de déploiement.

Une fois que le portail Azure a indiqué que le provisionnement du domaine managé était terminé, voici les tâches qu’il convient d’effectuer :

* Mettez à jour les paramètres DNS pour le réseau virtuel afin que les machines virtuelles puissent trouver le domaine géré pour l’authentification ou la jonction de domaine.
    * Pour configure le système DNS, sélectionnez votre domaine managé dans le portail. Dans la fenêtre **Vue d’ensemble**, vous êtes invité à configurer automatiquement ces paramètres DNS.
* Créez un groupe de sécurité réseau pour limiter le trafic dans le réseau virtuel du domaine managé. Un équilibreur Azure Standard Load Balancer, pour lequel ces règles doivent être en place, est créé. Ce groupe de sécurité réseau, qui sécurise Azure AD DS, est nécessaire pour que le domaine managé fonctionne correctement.
    * Pour créer le groupe de sécurité réseau et les règles nécessaires, installez d’abord le script `New-AzureAddsNetworkSecurityGroup` à l’aide de la commande `Install-Script -Name New-AaddsNetworkSecurityGroup`, puis exécutez `New-AaddsNetworkSecurityGroup`. Les règles nécessaires au domaine managé sont créées automatiquement.
* [Activez la synchronisation de mot de passe avec Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) afin que les utilisateurs finaux puissent se connecter au domaine managé avec leurs informations d’identification d’entreprise.

## <a name="complete-powershell-script"></a>Compléter le script PowerShell

Le script PowerShell complet suivant regroupe toutes les tâches présentées dans cet article. Copiez le script et enregistrez-le dans un fichier avec une extension `.ps1`. Exécutez le script dans une console PowerShell locale ou [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Pour activer Azure AD DS, vous devez être administrateur général du locataire Azure AD. Vous avez aussi besoin au moins de privilèges *Contributeur* dans l’abonnement Azure.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Créer la ressource et retourner le contrôle à l’invite PowerShell prend quelques minutes. Le provisionnement du domaine managé se poursuit en arrière-plan et le déploiement peut prendre jusqu’à une heure. Dans le portail Azure, la page **Vue d’ensemble** de votre domaine managé indique l’état actuel tout au long de cette phase de déploiement.

Une fois que le portail Azure a indiqué que le provisionnement du domaine managé était terminé, voici les tâches qu’il convient d’effectuer :

* Mettez à jour les paramètres DNS pour le réseau virtuel afin que les machines virtuelles puissent trouver le domaine géré pour l’authentification ou la jonction de domaine.
    * Pour configure le système DNS, sélectionnez votre domaine managé dans le portail. Dans la fenêtre **Vue d’ensemble**, vous êtes invité à configurer automatiquement ces paramètres DNS.
* Créez un groupe de sécurité réseau pour limiter le trafic dans le réseau virtuel du domaine managé. Un équilibreur Azure Standard Load Balancer, pour lequel ces règles doivent être en place, est créé. Ce groupe de sécurité réseau, qui sécurise Azure AD DS, est nécessaire pour que le domaine managé fonctionne correctement.
    * Pour créer le groupe de sécurité réseau et les règles nécessaires, installez d’abord le script `New-AzureAddsNetworkSecurityGroup` à l’aide de la commande `Install-Script -Name New-AaddsNetworkSecurityGroup`, puis exécutez `New-AaddsNetworkSecurityGroup`. Les règles nécessaires au domaine managé sont créées automatiquement.
* [Activez la synchronisation de mot de passe avec Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) afin que les utilisateurs finaux puissent se connecter au domaine managé avec leurs informations d’identification d’entreprise.

## <a name="next-steps"></a>Étapes suivantes

Pour voir le domaine managé en action, vous pouvez [joindre une machine virtuelle Windows à un domaine][windows-join], [configurer le protocole LDAP sécurisé][tutorial-ldaps] et [configurer la synchronisation du hachage de mot de passe][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[availability-zones]: ../availability-zones/az-overview.md
