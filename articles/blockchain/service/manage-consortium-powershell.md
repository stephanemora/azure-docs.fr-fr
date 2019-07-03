---
title: Gérer les membres d’un consortium Azure Blockchain Service à l’aide d’Azure PowerShell
description: Découvrez comment gérer les membres du consortium Azure Blockchain Service à l’aide d’Azure PowerShell.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 4bb72bc3fe8b85a8d2aed88e02f5f3150abb6899
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493675"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>Gérer les membres du consortium dans Azure Blockchain Service à l’aide de PowerShell

Vous pouvez utiliser PowerShell pour gérer les membres du consortium blockchain pour votre Service de Blockchain Azure. Les membres qui disposent des privilèges d’administrateur peuvent inviter, ajouter, supprimer et modifier des rôles pour tous les participants dans le consortium blockchain. Les membres qui disposent des privilèges d’utilisateur peuvent afficher tous les participants dans le consortium blockchain et modifier leur nom complet du membre.

## <a name="prerequisites"></a>Conditions préalables

* Créer un membre de blockchain à l’aide de la [Azure portal](create-member.md).
* Pour plus d’informations sur les nœuds, les membres et les consortiums, consultez [consortium de Azure Blockchain Service](consortium.md).

## <a name="open-azure-cloud-shell"></a>Ouvrir Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte.

Vous pouvez également ouvrir Cloud Shell dans un onglet distinct du navigateur en accédant à [shell.azure.com/powershell](https://shell.azure.com/powershell). Sélectionnez **copie** pour copier les blocs de code, collez-le dans Cloud Shell, puis sélectionnez **entrée** pour l’exécuter.

## <a name="install-the-powershell-module"></a>Installer le module PowerShell

Installer le package Microsoft.AzureBlockchainService.ConsortiumManagement.PS à partir de PowerShell Gallery.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Définissez la préférence d’informations

Vous pouvez obtenir plus d’informations lors de l’exécution d’applets de commande en définissant la variable de préférence d’informations. Par défaut, *$InformationPreference* a la valeur *SilentlyContinue*.

Pour obtenir des informations plus détaillées des applets de commande, définissez la préférence dans PowerShell comme suit :

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Établir une connexion Web3

Pour gérer les membres du consortium, établir une connexion de Web3 à votre point de terminaison de membre de Service de Blockchain. Vous pouvez utiliser ce script pour définir des variables globales pour appeler les applets de commande de gestion consortium.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Remplacez *\<mot de passe de compte membre\>* avec le mot de passe de compte de membre que vous avez utilisé lorsque vous avez créé le membre.

Rechercher les autres valeurs dans le portail Azure :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à votre membre de Blockchain Service par défaut **vue d’ensemble** page.

    ![Vue d’ensemble de membres](./media/manage-consortium-powershell/member-overview.png)

    Remplacez *\<compte membre\>* et *\<RootContract adresse\>* avec les valeurs à partir du portail.

1. Pour l’adresse de point de terminaison, sélectionnez **des nœuds de Transaction**, puis sélectionnez le **nœud de transaction par défaut**. Le nœud par défaut a le même nom que le membre de la fonctionnalité « blockchain ».
1. Sélectionnez **Chaînes de connexion**.

    ![Chaînes de connexion](./media/manage-consortium-powershell/connection-strings.png)

    Remplacez *\<adresse de point de terminaison\>* avec la valeur de **HTTPS (clé d’accès 1)** ou **HTTPS (clé d’accès 2)** .

## <a name="manage-the-network-and-smart-contracts"></a>Gérer le réseau et les contrats intelligents

Utiliser le réseau et les applets de commande de contrat intelligents pour établir une connexion à des contrats intelligents du point de terminaison blockchain responsables de la gestion du consortium.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Utilisez cette applet de commande pour vous connecter à des contrats intelligents de la gestion consortium. Ces contrats sont utilisés pour gérer et appliquer des membres au sein du consortium.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| RootContractAddress | Adresse du contrat racine des contrats intelligents consortium gestion | Oui |
| Web3Client | Objet Web3Client obtenu à partir de New-Web3Connection | Oui |

#### <a name="example"></a>Exemples

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Web3Account d’importation

Utilisez cette applet de commande pour créer un objet pour contenir les informations de compte de gestion d’un nœud distant.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Adresse de compte de membre Blockchain | Oui |
| ManagedAccountPassword | Mot de passe de compte adresse | Oui |

#### <a name="example"></a>Exemples

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Nouvelle Web3Connection

Utilisez cette applet de commande pour établir une connexion au point de terminaison RPC d’un nœud de la transaction.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Adresse de point de terminaison membre Blockchain | Oui |

#### <a name="example"></a>Exemples

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Gérer les membres du consortium

Utiliser les applets de commande consortium membre management pour gérer des membres au sein du consortium. Les actions disponibles varient selon votre rôle consortium.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Utilisez cette applet de commande pour obtenir des détails sur le membre ou la liste des membres du consortium.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| Name | Le nom du membre Blockchain Service que vous souhaitez récupérer les détails sur. Lorsqu’un nom est entré, il retourne les détails du membre. Si un nom est omis, elle retourne une liste de tous les membres du consortium. | Non |
| Members | Objet de membres obtenu à partir de l’importation-ConsortiumManagementContracts | Oui |
| Web3Client | Objet Web3Client obtenu à partir de New-Web3Connection | Oui |

#### <a name="example"></a>Exemples

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Exemple de sortie

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Utilisez cette applet de commande pour supprimer un membre de la fonctionnalité « blockchain ».

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| Name | Nom de membre à supprimer | Oui |
| Members | Objet de membres obtenu à partir de l’importation-ConsortiumManagementContracts | Oui |
| Web3Account | Objet Web3Account obtenu à partir de l’importation-Web3Account | Oui |
| Web3Client | Objet Web3Client obtenu à partir de New-Web3Connection | Oui |

#### <a name="example"></a>Exemples

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Cette applet de commande permet de définir des attributs de membre, y compris le nom complet et le rôle du consortium de blockchain.

Les administrateurs de consortium peuvent définir **DisplayName** et **rôle** pour tous les membres. Un membre de consortium avec le rôle d’utilisateur peut modifier le nom d’affichage de leur propre membre uniquement.

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| Name | Nom du membre blockchain | Oui |
| DisplayName | Nouveau nom d’affichage | Non |
| AccountAddress | Adresse du compte | Non |
| Members | Objet de membres obtenu à partir de l’importation-ConsortiumManagementContracts | Oui |
| Web3Account | Objet Web3Account obtenu à partir de l’importation-Web3Account | Oui |
| Web3Client |  Objet Web3Client obtenu à partir de New-Web3Connection| Oui |

#### <a name="example"></a>Exemples

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Gérer les invitations de membres du consortium

Utilisez les applets de commande consortium membre invitation management pour gérer les invitations à participer à des membres du consortium. Les actions disponibles varient selon votre rôle consortium.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Utilisez cette applet de commande pour inviter de nouveaux membres pour le consortium.

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| SubscriptionId | ID d’abonnement Azure du membre à inviter | Oui |
| Role | Le rôle consortium. Valeurs peuvent être administrateur ou utilisateur. ADMIN est le rôle d’administrateur consortium. UTILISATEUR est le rôle de membre consortium. | Oui |
| Members | Objet de membres obtenu à partir de l’importation-ConsortiumManagementContracts | Oui |
| Web3Account | Objet Web3Account obtenu à partir de l’importation-Web3Account | Oui |
| Web3Client | Objet Web3Client obtenu à partir de New-Web3Connection | Oui |

#### <a name="example"></a>Exemples

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Utilisez cette applet de commande pour récupérer ou répertorier le statut de l’invitation d’un membre consortium.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| SubscriptionId | ID d’abonnement Azure du membre à inviter. Si l’abonnement ID est fourni, il retourne l’abonnement ID des détails de l’invitation. Si l’ID d’abonnement est omise, elle retourne une liste de toutes les invitations de membre. | Non |
| Members | Objet de membres obtenu à partir de l’importation-ConsortiumManagementContracts | Oui |
| Web3Client | Objet Web3Client obtenu à partir de New-Web3Connection | Oui |

#### <a name="example"></a>Exemples

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Exemple de sortie

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Utilisez cette applet de commande pour révoquer l’invitation d’un membre consortium.

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| SubscriptionId | ID d’abonnement Azure du membre à révoquer | Oui |
| Members | Objet de membres obtenu à partir de l’importation-ConsortiumManagementContracts | Oui |
| Web3Account | Objet Web3Account obtenu à partir de l’importation-Web3Account | Oui |
| Web3Client | Objet Web3Client obtenu à partir de New-Web3Connection | Oui |

#### <a name="example"></a>Exemples

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Utilisez cette applet de commande pour définir le **rôle** une invitation existant. Seuls les administrateurs consortium peuvent modifier des invitations.

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| SubscriptionId | ID d’abonnement Azure du membre à inviter | Oui |
| Role | Nouveau rôle consortium invitation. Les valeurs peuvent être **utilisateur** ou **administrateur**. | Oui |
| Members |  Objet de membres obtenu à partir de l’importation-ConsortiumManagementContracts | Oui |
| Web3Account | Objet Web3Account obtenu à partir de l’importation-Web3Account | Oui |
| Web3Client | Objet Web3Client obtenu à partir de New-Web3Connection | Oui |

#### <a name="example"></a>Exemples

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les nœuds, les membres et les consortiums, consultez :

> [!div class="nextstepaction"]
> [Consortium de Blockchain Service Azure](consortium.md)
