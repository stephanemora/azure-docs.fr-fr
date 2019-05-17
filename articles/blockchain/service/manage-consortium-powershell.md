---
title: Azure Blockchain Service consortium management à l’aide de PowerShell
description: Comment gérer les membres du consortium Azure Blockchain Service à l’aide de PowerShell
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: f15fa3b4972a2ac54d1d9bce916fdd42c2951d2f
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550878"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Gérer les membres du consortium dans Azure Blockchain Service à l’aide de PowerShell

Vous pouvez utiliser PowerShell pour gérer les membres du consortium blockchain pour votre Service de Blockchain Azure. Membres avec des privilèges d’administrateur peuvent inviter, ajouter, supprimer et modifie des rôles pour tous les participants dans le consortium blockchain. Les membres avec des privilèges d’utilisateur peuvent afficher tous les participants dans le consortium blockchain et peuvent modifier leur nom complet du membre.

## <a name="prerequisites"></a>Conditions préalables

* [Créer un membre de blockchain à l’aide du portail Azure](create-member.md)
* Pour plus d’informations sur les nœuds, les membres et les consortiums, consultez [consortium de Azure Blockchain Service](consortium.md)

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte.

Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.

## <a name="install-powershell-module"></a>Installer le module PowerShell

Installer le package Microsoft.AzureBlockchainService.ConsortiumManagement.PS à partir de PowerShell Gallery.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-information-preference"></a>Préférences de jeu

Vous pouvez obtenir plus d’informations lors de l’exécution d’applets de commande en définissant une variable de préférence d’informations. Par défaut, *$InformationPreference* a la valeur *SilentlyContinue*.

Pour obtenir des informations plus détaillées des applets de commande, définissez la préférence dans PowerShell comme suit :

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Établir une connexion Web3

Pour gérer les membres du consortium, vous devez établir une connexion Web3 à votre point de terminaison Azure Blockchain Service membre. Vous pouvez utiliser ce script pour définir des variables globales qui peuvent être utilisés lors de l’appel les applets de commande de gestion consortium.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Remplacez \<mot de passe de compte membre\> avec le mot de passe de compte de membre utilisé lorsque vous avez créé le membre.

Rechercher les autres valeurs dans le portail Azure :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à votre membre de Azure Blockchain Service par défaut **vue d’ensemble** page.

    ![Vue d’ensemble de membres](./media/manage-consortium-powershell/member-overview.png)

    Remplacez \<compte membre\>, et \<RootContract adresse\> avec les valeurs à partir du portail.

1. Pour l’adresse de point de terminaison, sélectionnez **des nœuds de Transaction** et sélectionnez le **par défaut** nœud de la transaction. Le nœud de transaction par défaut a le même nom que le membre de la fonctionnalité « blockchain ».
1. Sélectionnez **Chaînes de connexion**.

    ![Chaînes de connexion](./media/manage-consortium-powershell/connection-strings.png)

    Remplacez \<adresse de point de terminaison\> avec la valeur de **HTTPS (clé d’accès 1)** ou **HTTPS (clé d’accès 2)**.

## <a name="network-and-smart-contract-management"></a>Réseau et gestion des contrats intelligents

Utiliser le réseau et les applets de commande de contrat intelligents pour établir une connexion à votre blockchain point de terminaison contrats intelligents responsables de la gestion du consortium.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Se connecte à des contrats intelligents gestion consortium servent à gérer et appliquer des membres au sein du consortium.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| RootContractAddress | L’adresse racine de contrat des contrats intelligents consortium gestion | Oui |
| Web3Client | Objet Web3Client obtenu à partir de New-Web3Connection | Oui |

**Exemple**

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Web3Account d’importation

Utilisez cette applet de commande pour créer un objet pour contenir le compte de gestion des informations nœud distant.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Adresse de compte de membre Blockchain | Oui |
| ManagedAccountPassword | Mot de passe de compte adresse | Oui |

**Exemple**

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Nouvelle Web3Connection

Établit une connexion au point de terminaison RPC d’un nœud de la transaction.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Adresse de point de terminaison membre Blockchain | Oui |

**Exemple**

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="consortium-member-management"></a>Gestion des membres Consortium

Utiliser les applets de commande consortium membre management pour gérer des membres au sein du consortium. Les actions disponibles varient selon votre rôle consortium.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Obtient les détails de membre ou de la liste des membres du consortium.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| Nom | Le nom du membre Azure Blockchain Service que vous souhaitez récupérer les détails. Si vous fournissez un nom de membre, les détails du membre sont retournés. Si le nom est omis, une liste de tous les membres du consortium est retournée. | Non |
| Membres | Objet de membres obtenu à partir de l’importation-ConsortiumManagementContracts | Oui |
| Web3Client | Objet Web3Client obtenu à partir de New-Web3Connection | Oui |

**Exemple**

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

**Exemple de sortie**

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Supprime un membre de la fonctionnalité « blockchain ».

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| Nom | Nom de membre à supprimer | Oui |
| Membres | Objet de membres obtenu à partir de l’importation-ConsortiumManagementContracts | Oui |
| Web3Account | Objet Web3Account obtenu à partir de l’importation-Web3Account | Oui |
| Web3Client | Objet Web3Client obtenu à partir de New-Web3Connection | Oui |

**Exemple**

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Définit les attributs de membre, y compris le rôle de nom et le consortium affichage de la blockchain.

Les administrateurs de consortium peuvent définir **DisplayName** et **rôle** pour tous les membres. Membre Consortium avec le rôle d’utilisateur peut modifier uniquement les nom d’affichage de leur propre membre.

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| Nom | Nom du membre blockchain | Oui |
| Nom complet | Nouveau nom d’affichage | Non |
| AccountAddress | Adresse du compte | Non |
| Membres | Objet de membres obtenu à partir de l’importation-ConsortiumManagementContracts | Oui |
| Web3Account | Objet Web3Account obtenu à partir de l’importation-Web3Account | Oui |
| Web3Client |  Objet Web3Client obtenu à partir de New-Web3Connection| Oui |

**Exemple**

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="consortium-member-invitation-management"></a>Gestion d’invitation Consortium membre

Applets de commande consortium membre invitation management permet de gérer les invitations de membre consortium. Les actions disponibles varient selon votre rôle consortium.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Inviter de nouveaux membres pour le consortium.

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| SubscriptionId | ID d’abonnement Azure de membre invité | Oui |
| Rôle | Rôle du consortium. Valeurs peuvent être administrateur ou utilisateur. ADMIN est le rôle d’administrateur consortium. UTILISATEUR est le rôle de membre consortium. | Oui |
| Membres | Objet de membres obtenu à partir de l’importation-ConsortiumManagementContracts | Oui |
| Web3Account | Objet Web3Account obtenu à partir de l’importation-Web3Account | Oui |
| Web3Client | Objet Web3Client obtenu à partir de New-Web3Connection | Oui |

**Exemple**

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Récupère ou répertorie le statut de l’invitation consortium membre.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| SubscriptionId | ID d’abonnement Azure de membre invité. Si l’ID d’abonnement est fourni, les détails de l’invitation de l’ID d’abonnement sont retournés. Si l’ID d’abonnement est omis, une liste de toutes les invitations de membre sont retournés. | Non |
| Membres | Objet de membres obtenu à partir de l’importation-ConsortiumManagementContracts | Oui |
| Web3Client | Objet Web3Client obtenu à partir de New-Web3Connection | Oui |

**Exemple**

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

**Exemple de sortie**

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Révoque une invitation de membres du consortium.

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| SubscriptionId | ID d’abonnement Azure du membre à révoquer | Oui |
| Membres | Objet de membres obtenu à partir de l’importation-ConsortiumManagementContracts | Oui |
| Web3Account | Objet Web3Account obtenu à partir de l’importation-Web3Account | Oui |
| Web3Client | Objet Web3Client obtenu à partir de New-Web3Connection | Oui |

**Exemple**

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Définit le **rôle** une invitation existant. Seuls les administrateurs consortium peuvent modifier des invitations.

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Paramètre | Description | Obligatoire |
|-----------|-------------|:--------:|
| SubscriptionId | ID d’abonnement Azure de membre invité | Oui |
| Rôle | Nouveau rôle consortium invitation. Les valeurs peuvent être **utilisateur** ou **ADMIN** | Oui |
| Membres |  Objet de membres obtenu à partir de l’importation-ConsortiumManagementContracts | Oui |
| Web3Account | Objet Web3Account obtenu à partir de l’importation-Web3Account | Oui |
| Web3Client | Objet Web3Client obtenu à partir de New-Web3Connection | Oui |

**Exemple**

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les nœuds, les membres et les consortiums, consultez :

> [!div class="nextstepaction"]
> [Consortium de Blockchain Service Azure](consortium.md)