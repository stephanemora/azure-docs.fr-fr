---
title: Sécuriser votre domaine managé Azure Active Directory Domain Services | Microsoft Docs
description: Sécuriser votre domaine managé
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: maheshu
ms.openlocfilehash: 20579f7abd6cd815377c3e97d820a3e5490e0f95
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902517"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Sécuriser votre domaine managé Azure AD Domain Services
Cet article vous permet de sécuriser votre domaine géré. Vous pouvez désactiver l’utilisation des suites de chiffrement faibles ainsi que la synchronisation de hachage des informations d’identification NTLM.

## <a name="install-the-required-powershell-modules"></a>Installer les modules PowerShell nécessaires

### <a name="install-and-configure-azure-ad-powershell"></a>Installation et configuration d'Azure AD PowerShell
Suivez les instructions de l’article pour [Installer le module PowerShell Azure AD et vous connecter à Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Installation et configuration d'Azure PowerShell
Suivez les instructions de l’article pour [Installer le module Azure PowerShell et vous connecter à votre abonnement Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Désactiver les suites de chiffrement faibles et la synchronisation de hachage des informations d’identification NTLM
Utilisez le script PowerShell suivant pour :
1. Désactiver le support NTLM v1 sur le domaine managé.
2. Désactiver la synchronisation des hachages de mots de passe NTLM à partir de votre instance Active Directory locale.
3. Désactiver TLS v1 sur le domaine managé.

```powershell
// Login to your Azure AD tenant
Login-AzureRmAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

## <a name="next-steps"></a>Étapes suivantes
* [Fonctionnement de la synchronisation dans Azure Active Directory Domain Services](active-directory-ds-synchronization.md)
