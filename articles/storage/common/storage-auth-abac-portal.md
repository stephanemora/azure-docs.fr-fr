---
title: 'Tutoriel : Ajouter une condition d’attribution de rôle pour restreindre l’accès aux objets blob en utilisant le portail Azure (préversion) - Azure ABAC'
titleSuffix: Azure Storage
description: Ajoutez une condition d’attribution de rôle pour restreindre l’accès aux objets blob avec le portail Azure et le contrôle d’accès en fonction de l’attribut Azure (Azure ABAC).
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 05/06/2021
ms.openlocfilehash: 3e5e46e15a7885eb5e3f4828cb8298355a116fd8
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300486"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-the-azure-portal-preview"></a>Tutoriel : Ajouter une condition d’attribution de rôle pour restreindre l’accès aux objets blob en utilisant le portail Azure (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d’attribution de rôle Azure sont en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dans la plupart des cas, une attribution de rôle accorde les autorisations dont vous avez besoin sur des ressources Azure. Toutefois, dans certains cas, vous souhaiterez peut-être fournir un contrôle d’accès plus précis en ajoutant une condition d’attribution de rôle.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Ajouter une condition à une attribution de rôle
> * Restreindre l’accès aux objets blob en fonction d’une étiquette d’index d’objets blob

## <a name="prerequisites"></a>Prérequis

Pour plus d’informations sur les prérequis à l’ajout ou à la modification des conditions d’attribution de rôle, consultez [Prérequis aux conditions](../../role-based-access-control/conditions-prerequisites.md).

## <a name="condition"></a>Condition

Dans ce tutoriel, vous limitez l’accès aux objets blob dotés d’une étiquette spécifique. Par exemple, vous ajoutez une condition à une attribution de rôle afin que Chandra puisse uniquement lire les fichiers auxquels est associée l’étiquette Project=Cascade.

![Diagramme de l’attribution de rôle avec une condition.](./media/shared/condition-role-assignment-rg.png)

Si Chandra tente de lire un objet blob dépourvu de l’étiquette Project=Cascade, l’accès n’est pas autorisé.

![Diagramme montrant l’accès en lecture aux objets blob dotés de l’étiquette Project=Cascade.](./media/shared/condition-access.png)

Voici à quoi ressemble la condition exprimée sous forme de code :

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEqualsIgnoreCase 'Cascade'
    )
)
```

## <a name="step-1-create-a-user"></a>Étape 1 : Créer un utilisateur

1. Connectez-vous au portail Azure en tant que propriétaire d’un abonnement.

1. Cliquez sur **Azure Active Directory**.
    
1. Créez un utilisateur ou recherchez un utilisateur existant. Ce tutoriel utilise Chandra comme exemple.

## <a name="step-2-set-up-storage"></a>Étape 2 : Configurer le stockage

1. Créez un compte de stockage compatible avec la fonctionnalité d’étiquette d’index d’objets blob, qui est en préversion publique. Pour plus d’informations, consultez [Gérer et rechercher des données d’objets blob Azure avec des étiquettes d’index d’objets blob (préversion)](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support).

1. Créez un conteneur dans le compte de stockage et définissez le niveau d’accès public sur **Privé (pas d’accès anonyme)** .

1. Dans le conteneur, cliquez sur **Charger** pour ouvrir le volet Charger l’objet blob.

1. Recherchez un fichier texte à charger.

1. Cliquez sur **Avancé** pour développer le volet.

1. Dans la section **Étiquettes d’index d’objets blob**, ajoutez l’étiquette d’index d’objet blob suivante au fichier texte.

    Si vous ne voyez pas la section Étiquettes d’index d’objets blob et que vous venez d’inscrire votre abonnement, vous devrez peut-être attendre quelques minutes que les modifications se propagent. Pour plus d’informations, consultez [Utiliser des étiquettes d’index de blob (préversion) pour gérer et rechercher des données sur Stockage Blob Azure](../blobs/storage-blob-index-how-to.md).

    > [!NOTE]
    > Les objets blob prennent également en charge la possibilité de stocker des métadonnées clé/valeur arbitraires définies par l’utilisateur. Bien que les métadonnées soient similaires aux étiquettes d’index d’objet blob, vous devez utiliser des étiquettes d’index d’objet blob avec les conditions. 

    | Clé | Valeur |
    | --- | --- |
    | Project  | Cascade |

   ![Capture d’écran montrant le volet Charger l’objet blob avec la section Étiquettes d’index d’objets blob.](./media/storage-auth-abac-portal/container-upload-blob.png)

1. Cliquez sur le bouton **Charger** pour charger le fichier.

1. Chargez un second fichier texte.

1. Ajoutez l’étiquette d’index d’objets blob suivante au second fichier texte.

    | Clé | Valeur |
    | --- | --- |
    | Project  | Baker |

## <a name="step-3-assign-a-storage-blob-data-role"></a>Étape 3 : Attribuer un rôle de données d’objet blob de stockage

1. Ouvrez le groupe de ressources.

1. Cliquez sur **Contrôle d’accès (IAM)** .

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher les attributions de rôles pour cette étendue.

1. Cliquez sur **Ajouter** > **Ajouter une attribution de rôle (préversion)** .

   ![Capture d’écran du menu Ajouter > Ajouter une attribution de rôle (préversion)](./media/storage-auth-abac-portal/add-role-assignment-menu-preview.png)

    La page Ajouter une attribution de rôle s’ouvre.

1. Sous l’onglet **Rôles**, sélectionnez le rôle [Lecteur des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader).

    ![Capture d’écran de la page Ajouter une attribution de rôle avec l’onglet Rôles.](./media/storage-auth-abac-portal/roles.png)

1. Sous l’onglet **Membres**, sélectionnez l’utilisateur que vous avez créé.

   ![Capture d’écran de la page Ajouter une attribution de rôle avec l’onglet Membres.](./media/storage-auth-abac-portal/members.png)

1. (Facultatif) Dans la zone **Description**, entrez **Accès en lecture aux objets blob avec l’étiquette Project=Cascade**.

1. Cliquez sur **Suivant**.

## <a name="step-4-add-a-condition"></a>Étape 4 : Ajouter une condition

1. Sous l’onglet **Condition**, cliquez sur **Ajouter une condition**.

    ![Capture d’écran de la page Ajouter une condition d’attribution de rôle pour une nouvelle condition.](./media/storage-auth-abac-portal/condition-add-new.png)

    La page Ajouter une condition d’attribution de rôle s’affiche.

1. Dans la section Ajouter une action, cliquez sur **Sélectionner des actions**.

    Le volet Sélectionner une action s’affiche. Ce volet est une liste filtrée d’actions de données basée sur l’attribution de rôle destinée à être la cible de votre condition. 

    ![Capture d’écran du volet Sélectionner une action, avec une action sélectionnée.](./media/storage-auth-abac-portal/condition-actions-select.png)

1. Sous Lire un objet blob, cliquez sur **Lire du contenu à partir d’un objet blob avec des conditions d’étiquette**, puis cliquez sur **Sélectionner**.

1. Dans la section Créer l’expression, cliquez sur **Ajouter une expression**.

    La section Expression se développe.

1. Spécifiez les paramètres d’expression suivants :

    | Paramètre | Value |
    | --- | --- |
    | Source de l’attribut | Ressource |
    | Attribut | Balises d’index de blob [Valeurs dans la clé] |
    | Clé : | Project |
    | Opérateur | StringEqualsIgnoreCase |
    | Value | Cascade |

    ![Capture d’écran de la section Créer l’expression pour les étiquettes d’index d’objets blob.](./media/storage-auth-abac-portal/condition-expressions.png)

1. Faites défiler jusqu’à **Type d’éditeur**, puis cliquez sur **Code**.

    La condition s’affiche sous forme de code. Vous pouvez apporter des modifications à la condition dans cet éditeur de code. Pour revenir à l’éditeur visuel, cliquez sur **Visuel**.

    ![Capture d’écran de la condition affichée dans l’éditeur de code.](./media/storage-auth-abac-portal/condition-code.png)

1. Cliquez sur **Enregistrer** pour ajouter la condition et revenir à la page Ajouter une attribution de rôle.

1. Cliquez sur **Suivant**.

1. Sous l’onglet **Vérifier + attribuer**, cliquez sur **Vérifier + attribuer** pour attribuer le rôle avec une condition.

    Après quelques instants, le principal de sécurité est attribué au rôle dans l’étendue sélectionnée.

    ![Capture d’écran de la liste d’attributions de rôle après l’attribution du rôle.](./media/storage-auth-abac-portal/rg-role-assignments-condition.png)

## <a name="step-5-test-the-condition"></a>Étape 5 : Tester la condition

Pour tester la condition, vous devez utiliser Azure PowerShell.

1. Ouvrez une fenêtre PowerShell.

1. Utilisez [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) pour vous connecter en tant que Chandra.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Initialisez les variables suivantes avec les noms que vous avez utilisés.

    ```azurepowershell
    $storageAccountName = "<storageAccountName>"
    $containerName = "<containerName>"
    $blobNameBaker = "<blobNameBaker>"
    $blobNameCascade = "<blobNameCascade>"
    ```

1. Utilisez [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) afin de créer un contexte spécifique pour accéder à votre compte de stockage plus facilement.

    ```azurepowershell
    $bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
    ```

1. Utilisez [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) pour essayer de lire le fichier du projet Baker.

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameBaker -Context $bearerCtx 
    ```

    Voici un exemple de sortie. Notez que vous **ne pouvez pas** lire le fichier en raison de la condition que vous avez ajoutée.
    
    ```azurepowershell
    Get-AzStorageBlob: This request is not authorized to perform this operation using this permission. HTTP Status Code: 403 - HTTP Error Message: This request is not authorized to perform this operation using this permission.
    ErrorCode: AuthorizationPermissionMismatch
    ErrorMessage: This request is not authorized to perform this operation using this permission.
    RequestId: <requestId>
    Time: Sun, 13 Sep 2020 12:33:42 GMT
    ```
    
1. Lisez le fichier du projet Cascade.

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameCascade -Context $bearerCtx 
    ```

    Voici un exemple de sortie. Notez que vous pouvez lire le fichier, car il a l’étiquette Project=Cascade.
    
    ```azurepowershell
       AccountName: <storageAccountName>, ContainerName: <containerName>
    
    Name                 BlobType  Length          ContentType                    LastModified         AccessTier SnapshotT
                                                                                                                  ime
    ----                 --------  ------          -----------                    ------------         ---------- ---------
    CascadeFile.txt      BlockBlob 7               text/plain                     2021-04-24 05:35:24Z Hot
    ```

## <a name="step-6-clean-up-resources"></a>Étape 6 : Nettoyer les ressources

1. Supprimez l’attribution de rôle que vous avez ajoutée.

1. Supprimez le compte de stockage de test que vous avez créé.

1. Supprimez l’utilisateur que vous avez créé.

## <a name="next-steps"></a>Étapes suivantes

- [Exemples de conditions d’attribution de rôle Azure](storage-auth-abac-examples.md)
- [Actions et attributs pour les conditions d’attribution de rôle Azure dans Stockage Azure (préversion)](storage-auth-abac-attributes.md)
- [Format et syntaxe des conditions d’attribution de rôle Azure](../../role-based-access-control/conditions-format.md)
