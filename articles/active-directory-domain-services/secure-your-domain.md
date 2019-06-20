---
title: Sécuriser votre domaine managé Azure Active Directory Domain Services | Microsoft Docs
description: Sécuriser votre domaine managé
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: ab371553a96f3a8d393c8b773c4024d04fd171a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246733"
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
* [Comprendre la synchronisation pour les services de domaine Azure AD](synchronization.md)
