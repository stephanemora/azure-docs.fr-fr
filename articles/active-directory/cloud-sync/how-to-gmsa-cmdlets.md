---
title: Cmdlets PowerShell gMSA de l’agent de provisionnement cloud Azure AD Connect
description: Découvrez comment utiliser les cmdlets PowerShell gMSA de l’agent de provisionnement cloud Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46b5c94c84bdbd0e5bb0d7b08ba9296ebfcd1838
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306056"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Cmdlets PowerShell gMSA de l’agent de provisionnement cloud Azure AD Connect

Ce document vise à décrire les cmdlets PowerShell gMSA de l’agent de provisionnement cloud Azure AD Connect. Ces cmdlets vous permettent de bénéficier d’une plus grande granularité sur les autorisations appliquées au compte de service (gMSA). Par défaut, la synchronisation cloud Azure AD Connect applique toutes les autorisations similaires à Azure AD Connect sur le gMSA par défaut ou sur un gMSA personnalisé. 

Ce document couvre les cmdlets suivantes :  

`Set-AADCloudSyncRestrictedPermissions`

`Set-AADCloudSyncPermissions` 

## <a name="how-to-use-the-cmdlets"></a>Utilisation des cmdlets  

Les prérequis suivants sont nécessaires pour utiliser ces cmdlets.

1. Installez l’agent de provisionnement. 
2. Importez le module PS de l’agent de provisionnement dans une session PowerShell. 

 ```PowerShell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
 ```
3. Supprimez les autorisations existantes.  Pour supprimer toutes les autorisations existantes sur le compte de service, à l’exception de SELF, utilisez `Set-AADCloudSyncRestrictedPermission`.  

    Cette cmdlet exige un paramètre appelé `Credential` qui peut être transmis. Si elle est appelée sans, une invite s’ouvre.

    Pour créer une variable, utilisez la commande suivante :  

   `$credential = Get-Credential` 

   L’utilisateur est invité à entrer son nom d’utilisateur et son mot de passe. Les informations d’identification doivent correspondre au minimum à un administrateur de domaine (sur lequel l’agent est installé). Un administrateur d’entreprise est également possible. 

4.  Vous pouvez ensuite appeler la cmdlet permettant de supprimer les autorisations supplémentaires : 
   ```PowerShell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```
5. Autre possibilité, vous pouvez appeler simplement la commande suivante : 

   `Set-AADCloudSyncRestrictedPermissions`, qui vous invite à entrer vos informations d’identification. 

 6.  Ajoutez un type d’autorisation spécifique.  Les autorisations ajoutées sont identiques à celles d’Azure AD Connect.  Pour obtenir des exemples de définition d’autorisations, consultez [Utilisation de Set-AADCloudSyncPermissions](#using-set-aadcloudsyncpermissions) ci-dessous.

## <a name="using-set-aadcloudsyncpermissions"></a>Utilisation de Set-AADCloudSyncPermissions 
`Set-AADCloudSyncPermissions` prend en charge les mêmes types d’autorisations que ceux qu’utilise Azure AD Connect, à savoir : 

|Type d'autorisation|Description|
|-----|-----|
|BasicRead| Cf. autorisations [BasicRead](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions) pour Azure AD Connect|
|PasswordHashSync|Cf. autorisations [PasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization) pour Azure AD Connect|
|PasswordWriteBack|Cf. autorisations [PasswordWriteBack](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback) pour Azure AD Connect|
|HybridExchangePermissions|Cf. autorisations [HybridExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment) pour Azure AD Connect| 
|ExchangeMailPublicFolderPermissions| Cf. autorisations [ExchangeMailPublicFolderPermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders-preview) pour Azure AD Connect| 
|CloudHR| Applique « Contrôle total » aux « Objets utilisateur descendants » et « Créer/supprimer objets utilisateur » à « Cet objet et tous les objets descendants »| 
|Tous|Ajoute toutes les autorisations ci-dessus.| 



AADCloudSyncPermissions peut être utilisé de deux manières :
- [Accorder une certaine autorisation à tous les domaines configurés](#grant-a-certain-permission-to-all-configured-domains) 
- [Accorder une certaine autorisation à un domaine spécifique](#grant-a-certain-permission-to-a-specific-domain) 
## <a name="grant-a-certain-permission-to-all-configured-domains"></a>Octroi d’une certaine autorisation à tous les domaines configurés 
Pour pouvoir accorder certaines autorisations à tous les domaines configurés, vous devez utiliser un compte Administrateur d’entreprise.


 ```PowerShell
Set-AADCloudSyncPermissions -PermissionType “Any mentioned above” -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>Octroi d’une certaine autorisation à un domaine spécifique 
Pour pouvoir accorder certaines autorisations à un domaine spécifique, vous devez utiliser au minimum un compte Administrateur du domaine que vous cherchez à ajouter.


 ```PowerShell
Set-AADCloidSyncPermissions -PermissionType “Any mentioned above” -TargetDomain “FQDN of domain” (has to be already configured through wizard) -TargetDomaincredential $credential(same as above) 
```
 

Remarque : Dans le premier cas, les informations d’identification doivent être au minimum Administrateur d’entreprise. 

Pour 2. Les informations d’identification peuvent être Administrateur de domaine ou d’entreprise. 

  

