---
title: Sécuriser votre domaine managé Azure Active Directory Domain Services | Microsoft Docs
description: Sécuriser votre domaine managé
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: 3797c76f1537f86357f7ca68ffed4758eb1bdc9a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416498"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Sécuriser votre domaine managé Azure AD Domain Services
Cet article vous permet de sécuriser votre domaine géré. Vous pouvez désactiver l’utilisation des suites de chiffrement faibles ainsi que la synchronisation de hachage des informations d’identification NTLM.

## <a name="install-the-required-powershell-modules"></a>Installer les modules PowerShell nécessaires

### <a name="install-and-configure-azure-ad-powershell"></a>Installation et configuration d'Azure AD PowerShell
Suivez les instructions de l’article pour [Installer le module PowerShell Azure AD et vous connecter à Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Installation et configuration d'Azure PowerShell
Suivez les instructions de l’article pour [Installer le module Azure PowerShell et vous connecter à votre abonnement Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Désactiver les suites de chiffrement faibles et la synchronisation de hachage des informations d’identification NTLM
Utilisez le script PowerShell suivant pour :
1. Désactiver le support NTLM v1 sur le domaine managé.
2. Désactiver la synchronisation des hachages de mots de passe NTLM à partir de votre instance Active Directory locale.
3. Désactiver TLS v1 sur le domaine managé.

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

## <a name="next-steps"></a>Étapes suivantes
* [Comprendre la synchronisation pour les services de domaine Azure AD](active-directory-ds-synchronization.md)
