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
ms.topic: article
ms.date: 12/01/2016
ms.author: ambapat
ms.openlocfilehash: 14e6e8bb723eb236f8fb315454b8697a3bd947ef
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286425"
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Accorder à plusieurs applications l'autorisation d'accéder à un coffre de clés

## <a name="q-i-have-several-applications-that-need-to-access-a-key-vault-how-can-i-give-these-applications-up-to-1024-access-to-key-vault"></a>Q : J’ai plusieurs applications qui doivent accéder à un coffre de clés. Comment permettre à ces applications (jusqu’à 1 024) d’accéder au coffre de clés ?

La stratégie de contrôle d’accès d’Azure Key Vault prend en charge jusqu’à 1 024 entrées. Mais vous pouvez créer un groupe de sécurité Azure Active Directory. Ajoutez tous les principaux du service associés à ce groupe de sécurité et accordez à ce groupe de sécurité l'accès à Key Vault.

Voici les conditions préalables requises :
* [Installez le module Azure Active Directory V2 PowerShell](https://www.powershellgallery.com/packages/AzureAD).
* [Installez Azure PowerShell](/powershell/azure/overview).
* Pour exécuter les commandes suivantes, vous avez besoin d’autorisations pour créer/modifier des groupes dans le locataire Azure Active Directory. Si vous ne disposez pas des autorisations, vous devrez peut-être contacter votre administrateur Active Directory Azure.

Exécutez à présent les commandes suivantes dans PowerShell.

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
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionsToKeys all –PermissionsToSecrets all –PermissionsToCertificates all 
 
# Of course you can adjust the permissions as required 
```

Si vous avez besoin d’accorder un autre jeu d’autorisations à un groupe d’applications, créez un groupe de sécurité Active Directory Azure distinct pour ces applications.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur comment [sécuriser votre coffre de clés](key-vault-secure-your-key-vault.md).
