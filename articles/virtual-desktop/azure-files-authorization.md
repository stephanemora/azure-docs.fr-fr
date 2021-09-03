---
title: Comment autoriser un pool d’hôtes Azure Virtual Desktop pour Azure Files - Azure
description: Comment autoriser un pool d’hôtes Azure Virtual Desktop pour utiliser Azure Files.
author: Heidilohr
ms.topic: how-to
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 2593552fbad04ada5e903ba1eb6a2613e3ee55a0
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122535318"
---
# <a name="authorize-an-account-for-azure-files"></a>Autoriser un compte pour Azure Files

Cet article explique comment autoriser un pool d’hôtes Azure Virtual Desktop pour utiliser Azure Files.

## <a name="requirements"></a>Configuration requise

Avant de commencer, vous avez besoin des éléments suivants :

- un compte Active Directory Domain Services (AD DS) synchronisé avec Azure Active Directory (Azure AD) ;
- des autorisations permettant de créer un groupe dans AD DS ;
- un compte de stockage et les autorisations nécessaires à la création d’un nouveau compte de stockage, si nécessaire ;
- une machine virtuelle (VM) ou une machine physique jointe à AD DS à laquelle vous avez l’autorisation d’accéder ;
- un pool d’hôtes Azure Virtual Desktop dans lequel tous les hôtes de la session ont été joints à un domaine.

## <a name="create-a-security-group-in-active-directory-domain-services"></a>Créer un groupe de sécurité dans Active Directory Domain Services

Tout d’abord, vous devez créer un groupe de sécurité dans AD DS. Ce groupe de sécurité est utilisé dans les prochaines étapes pour accorder des autorisations de partage de fichiers au niveau des partages et de NTFS (New Technology File System).

>[!NOTE]
>Si vous avez un groupe de sécurité existant que vous préférez utiliser, sélectionnez le nom de ce groupe au lieu d’en créer un nouveau.

Pour créer un groupe de sécurité :

1. Ouvrez une session à distance avec la machine virtuelle ou la machine physique jointe à AD DS que vous souhaitez ajouter au groupe de sécurité.

2. Ouvrez **Utilisateurs et ordinateurs Active Directory**.

3. Sous le nœud de domaine, cliquez avec le bouton droit sur le nom de votre machine. Dans le menu déroulant, sélectionnez **Nouveau** > **Groupe**.

4. Dans la fenêtre **Nouvel objet - Groupe**, entrez le nom du nouveau groupe, puis sélectionnez les valeurs suivantes :

    - Pour **Étendue du groupe**, sélectionnez **Global**
    - Pour **Type de groupe**, sélectionnez **Sécurité**

5. Cliquez avec le bouton droit sur le nouveau groupe et sélectionnez **Propriétés**.

6. Dans la fenêtre **Propriétés**, sélectionnez l’onglet **Membres**.

7. Sélectionnez **Ajouter...** .

8. Dans la fenêtre **Sélectionner des utilisateurs, des contacts, des ordinateurs, des comptes de service ou des groupes**,sélectionnez **Types d’objets...** . > **Ordinateurs**. Lorsque vous avez terminé, sélectionnez **OK**.

9. Dans la fenêtre **Entrez les noms des objets à sélectionner**, entrez les noms de tous les hôtes de la session que vous souhaitez inclure dans le groupe de sécurité.

10. Sélectionnez **Vérifier les noms**, puis sélectionnez le nom de l’hôte de la session que vous souhaitez utiliser à partir de la liste qui s’affiche.

11. Sélectionnez **OK**, puis cliquez sur **Appliquer**.

>[!NOTE]
>La synchronisation des nouveaux groupes de sécurité peut prendre jusqu’à 1 heure avec Azure AD.

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Si vous n’avez pas encore créé de compte de stockage, suivez d’abord les instructions dans [Créer un compte de stockage](../storage/common/storage-account-create.md). Lorsque vous créez un nouveau compte de stockage, assurez-vous de créer également un nouveau partage de fichiers.

>[!NOTE]
>Si vous créez un compte de stockage **Premium**, assurez-vous que **Type de compte** est défini sur **FileStorage**.

## <a name="get-rbac-permissions"></a>Obtenir des autorisations de contrôle d’accès en fonction du rôle

Pour obtenir des autorisations de contrôle d’accès en fonction du rôle :

1. Sélectionnez le compte de stockage que vous souhaitez utiliser.

2. Sélectionnez **Contrôle d’accès (IAM)** , puis cliquez sur **Ajouter**. Sélectionnez ensuite **Ajouter des attributions de rôle** à partir du menu déroulant.

3. Dans l’écran **Ajouter une attribution de rôle**, sélectionnez les valeurs suivantes :

    - pour **Rôle**, sélectionnez **Contributeur de partage SMB de données de fichier de stockage** ;
    - pour **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service** ;
    - pour **Abonnement**, sélectionnez **En fonction de votre environnement** ;
    - pour **Sélectionner**, sélectionnez le nom du groupe Active Directory qui contient vos hôtes de la session.

4. Sélectionnez **Enregistrer**.

## <a name="join-your-storage-account-to-ad-ds"></a>Joindre votre compte de stockage à AD DS

Ensuite, vous devez joindre le compte de stockage à AD DS. Pour joindre votre compte à AD DS :

1. Ouvrez une session à distance sur une machine virtuelle ou une machine physique jointe à AD DS.

      >[!NOTE]
      > Exécutez le script à l’aide des informations d’identification AD DS en local synchronisées à votre Azure AD. Les informations d’identification AD DS locales doivent disposer des autorisations du rôle Azure du propriétaire du compte de stockage ou du contributeur.

2. Téléchargez et décompressez [la dernière version sur AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases).

3. Ouvrez **PowerShell** en mode d’exécution élevé.

4. Exécutez la cmdlet suivante pour définir la stratégie d’exécution :
    
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

5. Accédez ensuite au dossier dans lequel vous avez décompressé AzfileHybrid et exécutez la commande suivante :

    ```powershell
    .\\CopyToPSPath.ps1
    ```

6. Après cela, importez le module AzFilesHybrid en exécutant cette cmdlet :
   
   ```powershell
   Import-Module -Name AzFilesHybrid
   ```

7. Puis exécutez cette cmdlet pour vous connecter à Azure AD :
   
   ```powershell
   Connect-AzAccount
   ```

8. Définissez les paramètres suivants en veillant à remplacer les espaces réservés par les valeurs s’appliquant à votre scénario :

    ```powershell
    $SubscriptionId = "<your-subscription-id-here>"

    $ResourceGroupName = "<resource-group-name-here>"

    $StorageAccountName = "<storage-account-name-here>"
    ```

9.  Enfin, exécutez cette commande :

    ```powershell
    Join-AzStorageAccountForAuth `

    -ResourceGroupName $ResourceGroupName `

    -StorageAccountName $StorageAccountName `

    -DomainAccountType "ComputerAccount" `

    -OrganizationalUnitDistinguishedName "<ou-here>" `

    -EncryptionType "'RC4','AES256'"
    ```

## <a name="get-ntfs-level-permissions"></a>Obtenir les autorisations de niveau NTFS

Pour vous authentifier avec des comptes d’ordinateur AD DS sur un compte de stockage Azure Files, nous devons également attribuer des autorisations de niveau NTFS en plus de l’autorisation RBAC (contrôle d’accès en fonction du rôle) que nous avons configurée précédemment.

Pour attribuer des autorisations NTFS :

1. Ouvrez le portail Azure et accédez au compte de stockage que nous avons ajouté à AD DS.

2. Sélectionnez **Clés d’accès**, puis copiez la valeur dans le champ **Clé1**.

3. Démarrez une session à distance sur la machine virtuelle ou machine physique jointe à AD DS.

4. Ouvrez une invite de commande en mode élevé.

5. Exécutez la commande suivante, en remplaçant les espaces réservés par les valeurs s’appliquant à votre déploiement :

    ```cmd
    net use <desired-drive-letter>:
    \\<storage-account-name>.file.core.windows.net\<share-name>
    /user:Azure\<storage-account-name> <storage-account-key>
    ```

    >[!NOTE]
    >Lorsque vous exécutez cette commande, la sortie doit indiquer « La commande s’est correctement terminée. » Si ce n’est pas le cas, vérifiez votre entrée et réessayez.

6. Ouvrez **Explorateur de fichiers** et trouvez la lettre de lecteur que vous avez utilisée dans la commande de l’étape 5.

7. Cliquez avec le bouton droit sur la lettre de lecteur, puis sélectionnez **Propriétés** > **Sécurité** à partir du menu déroulant.

8. Sélectionnez **Modifier**, puis cliquez sur **Ajouter...** .

    >[!NOTE]
    >Assurez-vous que le nom de domaine correspond à votre nom de domaine AD DS. Si ce n’est pas le cas, cela signifie que le compte de stockage n’a pas été joint au domaine. Pour continuer, vous devez utiliser un compte joint à un domaine.

9. Si vous y êtes invité, entrez vos informations d’identification de l’administrateur.

10. Dans la fenêtre **Sélectionner les utilisateurs, les ordinateurs, les comptes de service ou les groupes**, entrez le nom du groupe à partir de [Créer un groupe de sécurité dans Active Directory Domain Services](#create-a-security-group-in-active-directory-domain-services).

11. Sélectionnez **OK**. Confirmez ensuite que le groupe dispose de l’autorisation **Lire et exécuter**. Si le groupe possède des autorisations, la case « Autoriser » doit être cochée, comme illustré dans l’image suivante :

    > [!div class="mx-imgBorder"]
    > ![Une capture d’écran de la fenêtre Sécurité. Sous une liste « Autorisations », l’autorisation « Lire et exécuter » a une coche verte sous la colonne « Autoriser ».](media/read-and-execute.png)

12. Ajoutez le groupe Active Directory avec les comptes d’ordinateur disposant d’autorisations **Lire et exécuter** au groupe de sécurité.

13. Sélectionnez **Appliquer**. Si une invite Sécurité Windows s’affiche, sélectionnez **Oui** pour confirmer vos modifications.

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes après l’installation, consultez notre [Article sur la résolution des problèmes Azure Files](troubleshoot-authorization.md).