---
title: 'Azure Active Directory Domain Services : activer la synchronisation de hachage de mot de passe | Microsoft Docs'
description: Prise en main des services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/02/2018
ms.author: maheshu
ms.openlocfilehash: 4122052e48e57a27492433d01ee93536a9bbcf35
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
---
# <a name="enable-password-hash-synchronization-to-azure-active-directory-domain-services"></a>Activer la synchronisation de hachage de mot de passe pour Azure Active Directory Domain Services
Dans les tâches précédentes, vous avez activé Azure Active Directory Domain Services pour votre locataire Azure Active Directory (Azure AD). Dans la tâche suivante, vous allez activer la synchronisation des hachages de mot de passe requise pour l’authentification NT LAN Manager (NTLM) et Kerberos avec Azure AD Domain Services. Une fois la synchronisation des informations de hachage de mot de passe configurée, les utilisateurs peuvent se connecter au domaine managé à l’aide de leurs informations d’identification d’entreprise.

Les étapes nécessaires sont différentes pour les comptes d’utilisateurs uniquement dans le cloud par rapport aux comptes d’utilisateurs qui sont synchronisés à partir de votre répertoire local à l’aide d’Azure AD Connect. 

<br>
| **Type de compte d’utilisateur** | **Étapes à effectuer** |
| --- |---|
| **Comptes d’utilisateur cloud créés dans Azure AD** |**&#x2713;** [Suivez les instructions de cet article](active-directory-ds-getting-started-password-sync.md#task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts) |
| **Comptes d’utilisateur synchronisés à partir d’un répertoire local** |**&#x2713;** [Synchroniser les hachages de mot de passe des comptes d’utilisateur synchronisés à partir de votre répertoire AD local vers votre domaine managé](active-directory-ds-getting-started-password-sync-synced-tenant.md) | 

<br>

> [!TIP]
> **Vous devrez peut-être effectuer les deux ensembles d’étapes.**
> Si votre locataire Azure AD utilise une combinaison d’utilisateurs dans le cloud uniquement et d’utilisateurs provenant de votre répertoire Active Directory local, vous devez effectuer ces deux ensembles d’étapes.
>

## <a name="task-5-enable-password-hash-synchronization-to-your-managed-domain-for-cloud-only-user-accounts"></a>Tâche 5 : activer la synchronisation de hachage de mot de passe avec votre domaine managé pour les comptes d’utilisateurs uniquement dans le cloud
Pour authentifier les utilisateurs sur le domaine managé, Azure Active Directory Domain Services exige que les hachages de mot de passe soient dans un format adapté à l’authentification NTLM et Kerberos. Azure AD ne génère pas et ne stocke pas les hachages de mot de passe au format requis pour l’authentification NTLM ou Kerberos, à moins que vous n’activiez Azure Active Directory Domain Services pour votre locataire. Pour des raisons évidentes de sécurité, Azure AD ne stocke pas non plus d’informations de mot de passe dans un format en texte clair. Par conséquent, Azure AD n’a pas la capacité de générer automatiquement ces hachages de mot de passe NTLM ou Kerberos en fonction des informations d’identification existantes des utilisateurs.

> [!NOTE]
> **Si votre organisation utilise des comptes d’utilisateur dans le cloud uniquement, tous les utilisateurs ayant besoin d’Azure Active Directory Domain Services doivent modifier leur mot de passe.** Un compte d’utilisateur uniquement dans le cloud est un compte qui a été créé dans votre répertoire Azure AD à l’aide du portail Azure ou d’applets de commande PowerShell Azure AD. Ces comptes d’utilisateurs ne sont pas synchronisés à partir d’un répertoire local.
>
>

Le processus de modification du mot de passe entraîne la génération, dans Azure AD, des hachages de mot de passe requis par Azure Active Directory Domain Services pour l’authentification Kerberos et NTLM. Vous pouvez faire expirer les mots de passe de tous les utilisateurs du locataire qui doivent recourir à Azure AD DS, ou demander à ces utilisateurs de modifier leur mot de passe.

### <a name="enable-ntlm-and-kerberos-password-hash-generation-for-a-cloud-only-user-account"></a>Activer la génération du hachage de mot de passe NTLM et Kerberos pour un compte d’utilisateur dans le cloud uniquement
Voici les instructions que vous devez fournir aux utilisateurs pour qu’ils puissent modifier leur mot de passe :

1. Accédez à la [page du volet d’accès Azure AD](http://myapps.microsoft.com) de votre organisation.

    ![Ouvrez le panneau d’accès Azure AD](./media/active-directory-domain-services-getting-started/access-panel.png)

2. Dans l’angle supérieur droit, cliquez sur votre nom, puis sélectionnez **Profil** dans le menu.

    ![Sélectionner un profil](./media/active-directory-domain-services-getting-started/select-profile.png)

3. Sur la page **profil**, cliquez sur **Modifier le mot de passe**.

    ![Cliquez sur « Modifier le mot de passe »](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!TIP]
   > Si l’option **Modifier le mot de passe** ne s’affiche pas sur la fenêtre du volet d’accès, vérifiez que votre organisation a configuré la [gestion des mots de passe dans Azure AD](../active-directory/authentication/quickstart-sspr.md).
   >
   >
4. Sur la page **Modifier le mot de passe**, saisissez votre ancien mot de passe, puis tapez un nouveau mot de passe et confirmez-le.

    ![L’utilisateur change le mot de passe](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Cliquez sur **Envoyer**.

Quelques minutes après cette modification, le nouveau mot de passe est utilisable Azure AD DS. Au bout de 20 minutes environ, vous pouvez vous connecter aux ordinateurs joints au domaine managé à l’aide du nouveau mot de passe.

## <a name="related-content"></a>Contenu connexe
* [Comment mettre à jour votre mot de passe](../active-directory/active-directory-passwords-update-your-own-password.md)
* [Prise en main de la gestion de mot de passe dans Azure AD](../active-directory/authentication/quickstart-sspr.md)
* [Activer la synchronisation du mot de passe pour Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Administrer un domaine managé par Azure Active Directory Domain Services ](active-directory-ds-admin-guide-administer-domain.md)
* [Joindre une machine virtuelle Windows à un domaine managé par Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Joindre une machine virtuelle Red Hat Enterprise Linux à un domaine managé par Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
