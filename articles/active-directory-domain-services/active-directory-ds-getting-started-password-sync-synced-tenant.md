---
title: 'Azure AD Domain Services : Activer la synchronisation de mot de passe | Microsoft Docs'
description: Prise en main des services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 0b38bdba0d88da9296106411737c280dcf6d5df1
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69613173"
---
# <a name="enable-password-hash-synchronization-to-azure-active-directory-domain-services"></a>Activer la synchronisation de hachage de mot de passe pour Azure Active Directory Domain Services

Un client Azure AD connecté est défini pour se synchroniser avec le répertoire local de votre organisation à l’aide d’Azure AD Connect. Par défaut, Azure AD Connect ne synchronise pas les hachages des informations d’identification NTLM et Kerberos avec Azure AD. Pour utiliser les Services de domaine Azure AD, vous devez configurer Azure AD Connect pour synchroniser les hachages d’informations d’identification requis pour l’authentification NTLM et Kerberos. Les étapes suivantes permettent la synchronisation des hachages d’informations d’identification avec votre local Azure AD à partir de votre répertoire local.

> [!NOTE]
> **Si votre organisation utilise des comptes d’utilisateur qui sont synchronisés à partir de votre répertoire local, vous devez activer la synchronisation des hachages NTLM et Kerberos afin d’utiliser le domaine géré.** Un compte d’utilisateur synchronisé est un compte qui a été créé dans votre répertoire local puis synchronisé avec votre locataire Azure AD à l’aide d’Azure AD Connect. La synchronisation de hachage de mot de passe est un processus différent de la synchronisation utilisateur/objet. Vous devez désactiver/activer la synchronisation de hachage de mot de passe pour une synchronisation complète du mot de passe, après quoi, les mises à jour de hachage de mot de passe par lot s'affichent dans le journal des événements de l’application.

Dans cet article, vous allez activer la synchronisation des hachages d’informations d’identification requis pour l’authentification NT LAN Manager (NTLM) et Kerberos avec Services de domaine Azure AD. Une fois la synchronisation des informations d’identification configurée, les utilisateurs peuvent se connecter au domaine managé à l’aide de leurs informations d’identification d’entreprise.

Les étapes nécessaires sont différentes pour les comptes d’utilisateurs uniquement dans le cloud par rapport aux comptes d’utilisateurs qui sont synchronisés à partir de votre répertoire local à l’aide d’Azure AD Connect.

| **Type de compte d’utilisateur** | **Étapes à effectuer** |
| --- | --- |
| **Comptes d’utilisateur synchronisés à partir d’un répertoire local** |**&#x2713;** [Suivez les instructions de cet article](active-directory-ds-getting-started-password-sync-synced-tenant.md#install-or-update-azure-ad-connect) |
| **Comptes d’utilisateur cloud créés dans Azure AD** |**&#x2713;** [Synchroniser les mots de passe des comptes d’utilisateur dans le cloud uniquement avec votre domaine managé](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) |

> [!TIP]
> **Vous devrez peut-être effectuer les deux ensembles d’étapes.**
> Si votre locataire Azure AD utilise une combinaison d’utilisateurs dans le cloud uniquement et d’utilisateurs provenant de votre répertoire Active Directory local, vous devez effectuer ces deux ensembles d’étapes.

## <a name="install-or-update-azure-ad-connect"></a>Installer ou mettre à jour Azure AD Connect

Installer la dernière version recommandée d’Azure AD Connect sur un ordinateur joint à un domaine. Si une instance d’Azure AD Connect est déjà configurée, vous devez la mettre à jour pour utiliser la dernière version d’Azure AD Connect. Utilisez toujours la dernière version d’Azure AD Connect afin d’éviter les problèmes/bogues connus déjà corrigés.

**[Télécharger Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)**

Version recommandée : **1.1.614.0** - publiée le 5 septembre 2017.

> [!WARNING]
> Vous DEVEZ installer la dernière version recommandée d’Azure AD Connect pour que les informations d’identification de mot de passe héritées (nécessaires pour l’authentification NTLM et Kerberos) puissent être synchronisées avec votre client Azure AD. Cette fonctionnalité n’est pas disponible dans les versions antérieures d’Azure AD Connect ou avec l’outil DirSync hérité.

Les instructions d’installation d’Azure AD Connect sont disponibles dans l’article suivant : [Prise en main d’Azure AD Connect](../active-directory/hybrid/whatis-hybrid-identity.md)

## <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Activez la synchronisation des hachages des informations d’identification NTLM et Kerberos avec Azure AD

Exécutez le script PowerShell suivant sur chaque forêt AD. Le script permet la synchronisation des hachages de mot de passe NTLM et Kerberos de l’ensemble des utilisateurs locaux avec votre locataire Azure AD. Il lance également une synchronisation complète dans Azure AD Connect.

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
```

En fonction de la taille de votre répertoire (nombre d’utilisateurs, de groupes, etc.), la synchronisation des hachages d’informations d’identification avec Azure AD peut prendre du temps. Les mots de passe seront utilisables sur le domaine géré de Services de domaine Azure AD peu après la synchronisation des hachages d'informations d'identification avec Azure AD.

## <a name="next-steps"></a>Étapes suivantes

> [!NOTE]
> La **synchronisation de hachage de mot de passe** est un processus différent de la synchronisation utilisateur/objet. Vous devez désactiver/activer la synchronisation de hachage de mot de passe pour une synchronisation complète du mot de passe, après quoi, les mises à jour de hachage de mot de passe par lot s'affichent dans le journal des événements de l’application.

* [Activer la synchronisation de mot de passe pour les services de domaine AAD pour un répertoire Azure AD uniquement dans le cloud](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)
* [Gérer un domaine Azure AD Domain Services](tutorial-create-management-vm.md)
* [Joindre une machine virtuelle Windows à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Joindre une machine virtuelle Linux Red Hat Enterprise à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
