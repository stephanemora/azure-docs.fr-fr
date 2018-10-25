---
title: Accorder à plusieurs applications l'autorisation d'accéder à un coffre de clés Azure | Microsoft Docs
description: Découvrez comment accorder à plusieurs applications l'autorisation d'accéder à un coffre de clés
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: ambapat
ms.openlocfilehash: 4ad6a18f9937fcc7d24bebc3ac197e23990ff59e
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309244"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Accorder l’accès à un coffre de clés à plusieurs applications

Vous pouvez utiliser une stratégie de contrôle d’accès pour accorder l’accès à un coffre de clés à plusieurs applications. Une stratégie de contrôle d’accès peut prendre en charge jusqu'à 1 024 applications et est configurée comme suit :

1. Créez un groupe de sécurité Azure Active Directory. 
2. Ajoutez les principaux de service associés de toutes les applications au groupe de sécurité.
3. Accordez l’accès à votre coffre de clés au groupe de sécurité.

Voici les conditions préalables requises :
* [Installez le module Azure Active Directory V2 PowerShell](https://www.powershellgallery.com/packages/AzureAD).
* [Installez Azure PowerShell](/powershell/azure/overview).
* Pour exécuter les commandes suivantes, vous avez besoin d’autorisations pour créer/modifier des groupes dans le locataire Azure Active Directory. Si vous ne disposez pas des autorisations, vous devrez peut-être contacter votre administrateur Active Directory Azure. Pour des détails sur les autorisations d’une stratégie d’accès à un coffre de clés, consultez [Présentation des clés, des secrets et des certificats Azure Key Vault](about-keys-secrets-and-certificates.md).

Exécutez à présent les commandes suivantes dans PowerShell :

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
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Si vous avez besoin d’accorder un autre jeu d’autorisations à un groupe d’applications, créez un groupe de sécurité Active Directory Azure distinct pour ces applications.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur comment [sécuriser votre coffre de clés](key-vault-secure-your-key-vault.md).
