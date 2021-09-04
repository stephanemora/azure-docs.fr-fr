---
title: Résolution des problèmes affectant Azure Files dans Azure Virtual Desktop – Azure
description: Guide pratique pour résoudre les problèmes affectant à Azure Files dans Azure Virtual Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 31fe9f55252c00b5475bbb96cef646d0906a05f7
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122564074"
---
# <a name="troubleshoot-azure-files-authorization"></a>Résoudre les problèmes d’autorisations Azure Files

Cet article décrit les problèmes courants liés à l’authentification Azure Files avec Azure Active Directory (AD) et propose des suggestions sur la façon de résoudre ces problèmes.

## <a name="my-group-membership-isnt-working"></a>Mon appartenance à un groupe ne fonctionne pas

Lorsque vous ajoutez une machine virtuelle à un groupe Active Directory Domain Services (AD DS), vous devez redémarrer cette machine virtuelle pour activer son appartenance au service.

## <a name="i-cant-add-my-storage-account-to-my-ad-ds"></a>Je ne peux pas ajouter mon compte de stockage à mon AD DS

Commencez par consulter l’article [Impossible de monter Azure Files avec les informations d’identification AD](../storage/files/storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) pour voir si votre problème y est répertorié.

Voici les principales causes de problèmes rencontrés par les utilisateurs :

- Le fait d’ignorer les messages d’avertissement qui s’affichent lors de la création du compte dans PowerShell. Si vous ignorez des avertissements, cela risque d’entraîner une configuration incorrecte des paramètres du nouveau compte. Pour résoudre ce problème, vous devez supprimer le compte de domaine qui représente le compte de stockage, puis réessayer.

- Le compte utilise une unité d’organisation (UO) incorrecte. Pour résoudre ce problème, entrez à nouveau les informations de l’unité d’organisation en respectant la syntaxe suivante :
    
    ```powershell
    DC=ouname,DC=domainprefix,DC=topleveldomain
    ```

    Par exemple :

    ```powershell
    DC=storageAccounts,DC=wvdcontoso,DC=com
    ```

- Si le compte de stockage n’apparaît pas instantanément dans votre Azure AD, ne vous inquiétez pas. La synchronisation d’un nouveau compte de stockage avec Azure AD prend généralement 30 minutes. Il vous suffit donc d’attendre un peu. Si la synchronisation ne se produit pas au bout de 30 minutes, consultez [la section suivante](#my-ad-ds-group-wont-sync-to-azure-ad).

## <a name="my-ad-ds-group-wont-sync-to-azure-ad"></a>Mon groupe AD DS n’est pas synchronisé avec Azure AD

Si votre compte de stockage ne se synchronise pas automatiquement avec Azure AD au bout de 30 minutes, vous devez forcer la synchronisation en utilisant [ce script](https://github.com/stgeorgi/msixappattach/blob/master/force%20AD%20DS%20to%20Azure%20AD%20sync/force%20sync.ps1).

## <a name="my-storage-account-says-it-needs-additional-permissions"></a>Mon compte de stockage indique qu’il a besoin d’autorisations supplémentaires

Si votre compte de stockage a besoin d’autorisations supplémentaires, vous n’êtes peut-être pas autorisé à accéder à l’attachement d’application MSIX et à FSLogix. Pour résoudre ce problème, assurez-vous d’avoir attribué l’une de ces autorisations à votre compte :

- L’autorisation RBAC **Contributeur de partage SMB de données de fichier de stockage**.

- Les autorisations NTFS **Lire et exécuter** et **List folder content** (Répertorier les contenus des fichiers).

## <a name="next-steps"></a>Étapes suivantes

Si vous devez vous rafraîchir la votre mémoire sur le processus de configuration d’Azure Files, consultez la page [Autoriser un compte pour Azure Files](azure-files-authorization.md).