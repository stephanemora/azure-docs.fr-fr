---
title: Résoudre les problèmes d’échec de connexion de partage lors de la copie de données vers Azure Data Box | Microsoft Docs
description: Décrit comment identifier des problèmes réseau empêchant les connexions de partage SMB lors de la copie de données vers un appareil Azure Data Box.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 08/23/2021
ms.author: alkohli
ms.openlocfilehash: afc76602b610488fd2ce4cf7f17e547821fc406c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773089"
---
# <a name="troubleshoot-share-connection-failure-during-data-copy-to-azure-data-box"></a>Résoudre les problèmes d’échec de connexion de partage lors de la copie de données vers Azure Data Box

Cet article décrit la procédure à suivre lorsque vous ne pouvez pas vous connecter à un partage SMB sur votre appareil Azure Data Box en raison d’un problème réseau.

Les raisons les plus courantes de l’impossibilité de se connecter à un partage sur votre appareil sont les suivantes :

- [problème de domaine](#check-for-a-domain-issue)
- [compte verrouillé en dehors du partage](#account-locked-out-of-share)
- [stratégie de groupe empêchant une connexion](#check-for-a-blocking-group-policy)
- [Problème d’autorisation](#check-for-permissions-issues)

## <a name="check-for-a-domain-issue"></a>Rechercher un problème de domaine

Pour déterminer si un problème de domaine empêche une connexion de partage :

- Essayez de vous connecter à l’appareil et d’utiliser l’un des formats suivants pour entrer votre nom d’utilisateur :

    - `<device IP address>\<user name>`
    - `\<user name>`

Si vous pouvez vous connecter à l’appareil, un problème de domaine n’empêche pas votre connexion de partage.

## <a name="account-locked-out-of-share"></a>Compte verrouillé en dehors du partage

Après cinq tentatives infructueuses de connexion à un partage avec un mot de passe incorrect, le partage est verrouillé et vous ne pouvez pas vous connecter pendant 15 minutes.
 
Les tentatives de connexion infructueuses peuvent inclure des processus en arrière-plan, telles des nouvelles tentatives, dont vous n’avez peut-être pas connaissance.

> [!NOTE]
> Si vous avez un appareil plus ancien avec Data Box version 4.0 ou antérieure, le compte est verrouillé pendant 30 minutes après 3 échecs de tentatives de connexion.

**Description de l’erreur.** Vous verrez l’une des erreurs suivantes, en fonction de la façon dont vous accédez au partage :

- Si vous essayez de vous connecter à partir de votre ordinateur hôte via SMB, le message d’erreur suivant s’affiche : « le compte référencé est actuellement verrouillé et n’est peut-être pas connecté ».

  L’exemple suivant montre la sortie d’une telle tentative de connexion.

  ```
  C:\Users\Databoxuser>net use \\10.100.100.10\mydbresources_BlockBlob /u:10.100.100.10\mydbresources
  Enter the password for '10.100.100.10\mydbresources' to connect to '10.100.100.10':
  System error 1909 has occurred.
  
  The referenced account is currently locked out and may not be logged on to.
  ```

- Si vous utilisez le service de copie de données, la notification suivante s’affiche dans l’interface utilisateur web locale de votre appareil :

  ![Capture d’écran du volet notifications dans l’interface utilisateur web locale pour une Data Box. Une notification pour un compte de partage verrouillé est mise en évidence.](media/data-box-troubleshoot-share-access/share-lock-01.png)


**Résolution suggérée.** Pour vous connecter à un partage SMB après le verrouillage d’un compte de partage, procédez comme suit :

1. Vérifiez les informations d’identification SMB pour le partage. Dans l’interface utilisateur web locale de votre appareil, accédez à **Connexion et copie**, puis sélectionnez **SMB** pour le partage. Vous voyez la boîte de dialogue suivante.

    ![Capture d’écran de l’écran Accéder au partage et copier les données pour un partage SMB sur une Data Box. Les icônes de copie pour le compte, le nom d’utilisateur et le mot de passe sont mises en évidence.](media/data-box-troubleshoot-share-access/get-share-credentials-01.png)

1. Une fois la période de verrouillage terminée (15 ou 30 minutes), le verrou est supprimé. Vous pouvez maintenant vous connecter au partage.

   - Pour vous connecter au partage à partir de votre ordinateur hôte via SMB, exécutez la commande suivante. Pour la procédure, consultez [Copier des données vers une Data Box via SMB](data-box-deploy-copy-data.md#connect-to-data-box).
  
     `net use \\<IP address of the device>\<share name> /u:<IP address of the device>\<user name for the share>`

   - Pour vous connecter à un partage à l’aide du service de copie de données, recherchez une notification indiquant que le compte d’utilisateur a été déverrouillé, comme ci-dessous. Sous l’onglet **Copier des données**, vous pouvez à présent [copier des données vers la Data Box](data-box-deploy-copy-data-via-copy-service.md#copy-data-to-data-box).

     ![Capture d’écran du volet de Copier des données dans l’interface utilisateur web locale de Data Box. Notification que le compte d’utilisateur de partage a été déverrouillé et que l’option Copier des données mise en évidence.](media/data-box-troubleshoot-share-access/share-lock-02.png)


## <a name="check-for-a-blocking-group-policy"></a>Rechercher une stratégie de groupe bloquante

Vérifiez si une stratégie de groupe sur votre ordinateur client/hôte vous empêche de vous connecter au partage. Si possible, déplacez votre ordinateur client/hôte vers une unité d’organisation (UO) à laquelle aucun objet stratégie de groupe (GPO) n’est appliqué.

Pour vous assurer qu’aucune stratégie de groupe n’empêche votre accès aux partages sur la Data Box :

* Assurez-vous que votre ordinateur client/hôte est dans sa propre unité d’organisation pour Active Directory.

* Assurez-vous qu’aucun GPO n’est appliqué à votre ordinateur client/hôte. Vous pouvez bloquer l’héritage pour vous assurer que l’ordinateur client/hôte (nœud enfant) n’hérite pas automatiquement des GPO du parent. Pour plus d’informations, consultez [Bloquer l’héritage](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731076(v=ws.11)).

## <a name="check-for-permissions-issues"></a>Rechercher des problèmes d’autorisations

S’il n’existe aucun problème de domaine et qu’aucune stratégie de groupe ne bloque votre accès au partage, recherchez d’éventuels problèmes d’autorisations sur votre appareil en consultant les journaux d’audit et les journaux des événements de sécurité.

### <a name="review-security-event-logs"></a>Examiner les journaux des événements de sécurité

Examinez les journaux des événements de sécurité Windows sur l’appareil pour identifier des erreurs indiquant un échec d’authentification.

Vous pouvez examiner les journaux des événements `Smbserver.Security` dans le dossier `etw` ou afficher les erreurs de sécurité dans l’observateur d’événements.

Pour consulter les journaux des événements de sécurité Windows dans l’observateur d’événements, procédez comme suit :

1. Pour ouvrir l’observateur d’événements Windows, dans l’**écran Démarrer**, entrez **Observateur d’événements**, puis appuyez sur Entrée.

1. Dans le volet de navigation Observateur d’événements, développez **Journaux Windows**, puis sélectionnez le dossier **Sécurité** .

    ![Capture d’écran de l’observateur d’événements Windows avec des événements de sécurité affichés. Le dossier Windows et le sous-dossier Sécurité sont mis en évidence.](media/data-box-troubleshoot-share-access/event-viewer-01.png)

3. Recherchez l’une des erreurs suivantes :

    Erreur 1 :

    ```xml
    SMB Session Authentication Failure
    Client Name: \\<ClientIP>
    Client Address: <ClientIP:Port>
    User Name:
    Session ID: 0x100000000021
    Status: The attempted logon is invalid. This is either due to a bad username or authentication information. (0xC000006D)
    SPN: session setup failed before the SPN could be queried
    SPN Validation Policy: SPN optional / no validation
    ```
      
    Erreur 2 :
    ```xml
     LmCompatibilityLevel value is different from the default.
    Configured LM Compatibility Level: 5
    Default LM Compatibility Level: 3   
    ```

    Chaque erreur indique que vous devez modifier le niveau d’authentification LAN Manager sur votre appareil.
 
### <a name="change-lan-manager-authentication-level"></a>Modifier le niveau d’authentification de LAN Manager
 
Pour modifier le niveau d’authentification LAN Manager sur votre appareil, vous pouvez soit [utiliser la Stratégie de sécurité locale](#use-local-security-policy), soit [mettre à jour le Registre directement](#update-the-registry).

#### <a name="use-local-security-policy"></a>Utiliser la Stratégie de sécurité locale

Pour modifier le niveau d’authentification LAN Manager à l’aide d’une stratégie de sécurité locale, procédez comme suit :
 
1. Pour ouvrir la Stratégie de sécurité locale, sur l’écran **Démarrer**, tapez `secpol.msc`, puis appuyez sur Entrée.

1. Accédez à **Stratégies locales** > **Options de sécurité**, puis ouvrez **Sécurité réseau : niveau d’authentification LAN Manager**.

    ![Capture d’écran montrant les Options de sécurité dans l’éditeur de Stratégie de sécurité locale. La stratégie « Sécurité réseau : niveau d’authentification LAN Manager » est mise en évidence.](media/data-box-troubleshoot-share-access/security-policy-01.png)

1. Modifiez le paramètre pour **Envoyer uniquement les réponses NTLMv2 et refuser LM & NTLM**.

    ![Capture d’écran montrant la stratégie « Sécurité réseau : niveau d’authentification LAN Manager » dans l’éditeur de Stratégie de sécurité locale. L’option « Envoyer uniquement les réponses NTLMv2 et refuser LM & NTLM » est mise en évidence.](media/data-box-troubleshoot-share-access/security-policy-02.png)

#### <a name="update-the-registry"></a>Mettre à jour le registre

Si vous ne pouvez pas modifier le niveau d’authentification LAN Manager dans la Stratégie de sécurité locale, mettez à jour le Registre directement.

Pour mettre à jour le Registre directement, procédez comme suit :

1. Pour ouvrir l’Éditeur du Registre (regedit32.exe), sur l’écran **Démarrer**, tapez `regedt32`, puis appuyez sur Entrée.

1. Accédez à : HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Contrôle > LSA.

    ![Capture d’écran montrant l’Éditeur du Registre avec le dossier LSA mis en évidence.](media/data-box-troubleshoot-share-access/security-policy-03.png)

1. Dans le dossier LSA, ouvrez la clé de Registre LMCompatibilityLevel et modifiez sa valeur en 5.

    ![Capture d’écran de la boîte de dialogue utilisée pour modifier la clé LmcompatibilityLevel dans le Registre. Le champ Données de valeur est mis en évidence.](media/data-box-troubleshoot-share-access/security-policy-04.png)

1. Redémarrez votre ordinateur afin que les modifications du Registre prennent effet.

## <a name="next-steps"></a>Étapes suivantes

- [Copier des données via SMB](data-box-deploy-copy-data.md).
- [Résoudre les problèmes de copie de données rencontrés dans Data Box](data-box-troubleshoot.md).
- [Contacter le support Microsoft](data-box-disk-contact-microsoft-support.md).