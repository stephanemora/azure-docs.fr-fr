---
title: Accorder à plusieurs applications l’autorisation d’accéder à un coffre de clés Azure - Azure Key Vault | Microsoft Docs
description: Découvrez comment accorder à plusieurs applications l'autorisation d'accéder à un coffre de clés
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: b1d0b0948e089d41f460ac2a54150ee51333f87c
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "64721980"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Accorder l’accès à un coffre de clés à plusieurs applications

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vous pouvez utiliser une stratégie de contrôle d’accès pour accorder l’accès à un coffre de clés à plusieurs applications. Une stratégie de contrôle d’accès peut prendre en charge jusqu'à 1 024 applications et est configurée comme suit :

1. Créez un groupe de sécurité Azure Active Directory. 
2. Ajoutez les principaux de service associés de toutes les applications au groupe de sécurité.
3. Accordez l’accès à votre coffre de clés au groupe de sécurité.

## <a name="prerequisites"></a>Prérequis

Voici les conditions préalables requises :
* [Installez Azure PowerShell](/powershell/azure/overview).
* [Installez le module Azure Active Directory PowerShell V2](https://www.powershellgallery.com/packages/AzureAD).
* Autorisations permettant de créer/modifier des groupes dans le client Azure Active Directory. Si vous ne disposez pas des autorisations, vous devrez peut-être contacter votre administrateur Active Directory Azure. Pour des détails sur les autorisations d’une stratégie d’accès à un coffre de clés, consultez [Présentation des clés, des secrets et des certificats Azure Key Vault](about-keys-secrets-and-certificates.md).

## <a name="granting-key-vault-access-to-applications"></a>Accorder aux applications l'accès à Key Vault

Exécutez les commandes suivantes dans PowerShell :

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Si vous avez besoin d’accorder un autre jeu d’autorisations à un groupe d’applications, créez un groupe de sécurité Active Directory Azure distinct pour ces applications.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur comment [sécuriser votre coffre de clés](key-vault-secure-your-key-vault.md).
