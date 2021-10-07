---
title: Résoudre les problèmes de sauvegarde Azure Database pour PostgreSQL
description: Informations sur la résolution des problèmes de sauvegarde Azure Database pour PostgreSQL.
ms.topic: troubleshooting
ms.date: 09/22/2021
ms.openlocfilehash: 0dcb5b3a85fd1364d90327648274194d3bc30364
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700509"
---
# <a name="troubleshoot-postgresql-database-backup-by-using-azure-backup-preview"></a>Résoudre les problèmes de sauvegarde de bases de données PostgreSQL avec Sauvegarde Azure (préversion)

Cet article fournit des informations sur la résolution des problèmes de sauvegarde de bases de données Azure PostgreSQL avec Sauvegarde Azure.

## <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Donnez à l’identité MSI du coffre de sauvegarde un accès en **Lecture** sur le serveur Postgres que vous voulez sauvegarder ou restaurer.

Pour établir une connexion sécurisée à la base de données PostgreSQL, Sauvegarde Azure utilise le modèle d’authentification [ Managed Service Identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md). Cela signifie que le coffre de sauvegarde aura accès seulement aux ressources auxquelles l’utilisateur a explicitement accordé une autorisation.

Une identité MSI système est automatiquement affectée au coffre au moment de la création. Vous devez donner à cette identité MSI du coffre l’accès aux serveurs PostgreSQL dont vous prévoyez de sauvegarder des bases de données.

Étapes :

1. Sur le serveur Postgres, accédez au volet **Contrôle d’accès (IAM)** .

    ![Volet Contrôle d’accès](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Sélectionnez **Ajouter des attributions de rôle**.

    ![Capture d’écran montrant comment ajouter une attribution de rôle.](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. Dans le volet contextuel droit qui s’ouvre, entrez les informations suivantes :<br>

   - **Rôle :** Sélectionnez le rôle **Lecteur** dans la liste déroulante.<br>
   - **Attribuer l’accès à :** Choisissez l’option **Utilisateur, groupe ou principal du service** dans la liste déroulante.<br>
   - **Sélectionnez :** Entrez le nom du coffre de sauvegarde où vous voulez sauvegarder ce serveur et ses bases de données.<br>

    ![Capture d’écran montrant comment sélectionner un rôle.](./media/backup-azure-database-postgresql/select-role-and-enter-backup-vault-name.png)

## <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Créez un utilisateur de sauvegarde de base de données qui peut s’authentifier auprès d’Azure Active Directory :

Cette erreur peut provenir de l’absence d’un administrateur Azure Active Directory pour le serveur PostgreSQL ou de l’absence d’un utilisateur de sauvegarde pouvant s’authentifier avec Azure Active Directory.

Étapes :

Ajoutez un administrateur Active Directory au serveur OSS :

Cette étape est nécessaire pour se connecter à la base de données via un utilisateur qui peut s’authentifier avec Azure Active Directory au lieu d’un mot de passe. L’utilisateur Administrateur Azure AD dans Azure Database pour PostgreSQL aura le rôle **azure_ad_admin**. Seul un rôle **azure_ad_admin** peut créer des utilisateurs de base de données qui peuvent s’authentifier avec Azure AD.

1. Accédez à l’onglet Administrateur Active Directory dans le volet de navigation gauche de la vue du serveur, puis ajoutez-vous (ou une autre personne) en tant qu’administrateur Active Directory.

    ![Capture d’écran montrant comment définir un administrateur Active Directory.](./media/backup-azure-database-postgresql/set-admin.png)

1. Veillez à **Enregistrer** la définition de l’utilisateur Administrateur Active Directory.

    ![Capture d’écran montrant comment enregistrer la définition de l’utilisateur Administrateur Active Directory.](./media/backup-azure-database-postgresql/save-admin-setting.png)

Reportez-vous à [ce document](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) pour obtenir la liste des étapes à suivre pour effectuer les étapes d’octroi des autorisations.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Établissez une visibilité du réseau en activant l’indicateur **Autoriser l’accès aux services Azure** dans la vue du serveur. Dans la vue du serveur, sous le volet **Sécurité de la connexion**, définissez l’indicateur **Autoriser l’accès aux services Azure** sur **Oui**.

>[!Note]
>Avant d’activer cet indicateur, veillez à définir **Refuser l’accès au réseau public** avec la valeur **Non**.

![Capture d’écran montrant comment autoriser l’accès aux services Azure.](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

## <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Autorisation de restaurer sur un conteneur d’un compte de stockage lors de la restauration en tant que fichiers

1. Donnez à l’identité MSI du coffre de sauvegarde l’autorisation d’accéder aux conteneurs du compte de stockage en utilisant le portail Azure.
    1. Accédez à **Compte de stockage** -> **Contrôle d’accès** -> **Ajouter une attribution de rôle**.
    1. Affectez le rôle **Contributeur aux données Blob du stockage** à l’identité MSI du coffre de sauvegarde.

    ![Capture d’écran montrant le processus d’affectation du rôle Contributeur aux données Blob du stockage.](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. Vous pouvez aussi accorder des autorisations précises sur le conteneur spécifique vers lequel vous effectuez la restauration en utilisant la commande Azure CLI [az role assignment create](/cli/azure/role/assignment).

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Remplacez le paramètre du destinataire de l’affectation par l’**ID d’application** de l’identité MSI du coffre et le paramètre d’étendue de façon à référencer votre conteneur spécifique.
    1. Pour obtenir l’**ID d’application** de l’identité MSI du coffre, sélectionnez **Toutes les applications** sous **Type d’application** :

        ![Capture d’écran montrant comment sélectionner toutes les applications.](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Recherchez le nom du coffre et copiez l’ID d’application :

        ![Capture d’écran montrant comment rechercher le nom du coffre.](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Étapes suivantes

[À propos de la sauvegarde d’Azure Database pour PostgreSQL (préversion)](backup-azure-database-postgresql-overview.md)
