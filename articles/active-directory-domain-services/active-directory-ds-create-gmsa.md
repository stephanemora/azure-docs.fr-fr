---
title: 'Azure Active Directory Domain Services : Créer un compte de service administré de groupe | Microsoft Docs'
description: Administrer les domaines gérés par les services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: ergreenl
ms.openlocfilehash: e477bd310f2d5ea2e216ba2e7630a375ea81c48c
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856738"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Créer un compte de service administré de groupe dans un domaine managé Azure AD Domain Services
Cet article montre comment créer des comptes de service administrés dans un domaine managé Azure AD Domain Services.

## <a name="managed-service-accounts"></a>Comptes de service administrés
Un compte de service administré autonome est un compte de domaine managé dont le mot de passe est managé automatiquement. Il simplifie la gestion des noms de principal du service (SPN) et permet de déléguer la gestion à d’autres administrateurs. Ce type de compte de service administré est apparu pour la première fois dans Windows Server 2008 R2 et Windows 7.

Le compte de service administré de groupe fournit les mêmes avantages pour de nombreux serveurs du domaine. Toutes les instances d’un service hébergé sur une batterie de serveurs doivent utiliser le même principal de service pour que les protocoles d’authentification mutuelle fonctionnent. Lorsqu’un compte de service administré de groupe est utilisé comme principal de service, le système d’exploitation Windows gère le mot de passe du compte au lieu de compter sur l’administrateur.

**Plus d’informations :**
- [Présentation des comptes de service administrés de groupe](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Bien démarrer avec les comptes de service administrés de groupe](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Utilisation des comptes de service dans Azure AD Domain Services
Les domaines managés Azure AD Domain Services sont verrouillés et gérés par Microsoft. Quelques points sont à prendre en considération lors de l’utilisation de comptes de service avec Azure AD Domain Services.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Créer des comptes de service dans les unités d’organisation personnalisées d’un domaine managé
Vous ne pouvez pas créer de compte de service dans les unités d’organisation intégrées « Utilisateurs AADDC » et « Ordinateurs AADDC ». [Créez une unité d’organisation personnalisée](active-directory-ds-admin-guide-create-ou.md) dans votre domaine managé, puis créez des comptes de service dans cette unité d’organisation.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>La clé racine des services de distribution de clés a déjà été créée.
La clé racine des services de distribution de clés est précréée dans un domaine managé Azure AD Domain Services. Vous n’avez pas besoin de créer cette clé racine, et n’avez pas non plus les privilèges nécessaires pour le faire. En outre, vous ne pouvez pas voir la clé racine dans le domaine managé.

## <a name="sample---create-a-gmsa-using-powershell"></a>Exemple : Créer un compte de service administré de groupe à l’aide de PowerShell
L’exemple suivant montre comment créer une unité d’organisation personnalisée à l’aide de PowerShell. Vous pouvez ensuite créer un compte de service administré de groupe dans cette unité d’organisation à l’aide du paramètre ```-Path``` pour spécifier l’unité d’organisation.

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=CONTOSO100,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso100.com  `
-Path "OU=MYNEWOU,DC=CONTOSO100,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso100.com/contoso100.com, `
http/WebFarmSvc.contoso100.com/contoso100,  `
http/WebFarmSvc/contoso100.com, http/WebFarmSvc/contoso100  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**Documentation sur les applets de commande PowerShell :**
- [Applet de commande New-ADOrganizationalUnit](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [Applet de commande New-ADServiceAccount](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>Étapes suivantes
- [Créer une unité d’organisation personnalisée dans un domaine managé](active-directory-ds-admin-guide-create-ou.md)
- [Présentation des comptes de service administrés de groupe](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Bien démarrer avec les comptes de service administrés de groupe](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
