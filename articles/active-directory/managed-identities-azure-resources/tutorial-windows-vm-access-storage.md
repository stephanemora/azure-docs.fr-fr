---
title: Tutoriel `:` Utiliser une identité managée pour accéder au Stockage Azure - Windows - Azure AD
description: Ce didacticiel vous guide tout au long de l’utilisation d’une identité managée affectée par le système de machine virtuelle Windows pour accéder au Stockage Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/24/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2449307936839d50fe0d48a0536ca4dd9c8d85c3
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74181915"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>Didacticiel : Utiliser une identité managée de machine virtuelle Windows attribuée par le système pour accéder au Stockage Azure par une clé d’accès

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


> [!IMPORTANT] 
> Stockage Azure prend désormais en charge l’authentification Azure AD. En guise de bonne pratique, utilisez l’[authentification Azure AD](tutorial-vm-windows-access-storage.md) plutôt que des clés d’accès. 


Ce didacticiel vous indique comment utiliser une identité managée attribuée par le système pour une machine virtuelle Windows afin de récupérer les clés d’accès d’un compte de stockage. Vous pouvez utiliser les clés d’accès de stockage comme d’habitude lors de l’exécution d’opérations de stockage, par exemple lors de l’utilisation du SDK Stockage. Pour ce didacticiel, nous chargeons et téléchargeons des objets blob à l’aide du PowerShell de Stockage Azure. Vous apprendrez à :


> [!div class="checklist"]
> * Créez un compte de stockage.
> * Autoriser votre machine virtuelle à accéder aux clés d’accès de stockage dans le Gestionnaire des ressources 
> * Obtenir un jeton d’accès à l’aide de l’identité de votre machine virtuelle et l’utiliser pour récupérer les clés d’accès de stockage à partir du Gestionnaire des ressources 

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>Créez un compte de stockage. 

Si vous n’en avez pas déjà un, vous allez maintenant créer un compte de stockage. Vous pouvez également ignorer cette étape, et autoriser votre identité managée attribuée par le système de la machine virtuelle à accéder aux clés d’un compte de stockage existant. 

1. Cliquez sur le bouton **+/Créer un service** dans l’angle supérieur gauche du portail Azure.
2. Cliquez sur **Stockage**, puis **Compte de stockage**, et un nouveau panneau « Créer un compte de stockage » s’affiche.
3. Saisissez un nom pour le compte de stockage, vous l’utiliserez ultérieurement.  
4. **Modèle de déploiement** et **Type de compte** doivent être respectivement définis sur « Gestionnaire de ressources » et « Usage général ». 
5. Assurez-vous que les champs **Abonnement** et **Groupe de ressources** correspondent à ceux que vous avez spécifiés lorsque vous avez créé votre machine virtuelle à l’étape précédente.
6. Cliquez sur **Créer**.

    ![Créer un nouveau compte de stockage](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Création d’un conteneur d’objets blob dans le compte de stockage

Plus tard, nous chargerons et téléchargerons un fichier vers le nouveau compte de stockage. Étant donné que les fichiers nécessitent un stockage d’objets blob, nous devons créer un conteneur d’objets blob dans lequel stocker le fichier.

1. Revenez à votre compte de stockage nouvellement créé.
2. Cliquez sur le lien **Conteneurs** sur la gauche, sous « Service blob ».
3. Cliquez sur **+ Conteneur** en haut de la page et un panneau « Nouveau conteneur » apparait.
4. Nommez le conteneur, sélectionnez un niveau d’accès, puis cliquez sur **OK**. Le nom spécifié sera utilisé plus loin dans le didacticiel. 

    ![Créer un conteneur de stockage](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-storage-account-access-keys"></a>Autoriser votre identité managée attribuée par le système de la machine virtuelle à utiliser les clés d’accès d’un compte de stockage 

Le stockage Azure ne prend pas en charge l’authentification Azure AD en mode natif.  Cependant, vous pouvez utiliser l’identité managée attribuée par le système de la machine virtuelle pour récupérer les clés d’accès d’un compte de stockage depuis le gestionnaire des ressources, puis utiliser une clé pour accéder au stockage.  Dans cette étape, vous autorisez votre identité managée attribuée par le système de la machine virtuelle à accéder aux clés de votre compte de stockage.   

1. Revenez à votre compte de stockage nouvellement créé.  
2. Cliquez sur le lien **(IAM) de contrôle d’accès** dans le panneau de gauche.  
3. Cliquez sur **+ Ajouter une attribution de rôle** en haut de la page pour ajouter une nouvelle attribution de rôle à votre machine virtuelle.
4. Définissez le **Rôle** sur « Rôle de service d’opérateur de clé de compte de stockage », sur le côté droit de la page. 
5. Dans la liste déroulante suivante, définissez **Attribuer l’accès à** sur la ressource « Machine virtuelle ».  
6. Ensuite, assurez-vous que l’abonnement approprié est répertorié dans la liste déroulante **Abonnement**, puis définissez **Groupe de ressources** sur « Tous les groupes de ressources ».  
7. Enfin, sous **Sélectionner**, choisissez votre machine virtuelle Windows dans la liste déroulante, puis cliquez sur **Enregistrer**. 

    ![Texte de remplacement d’image](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-to-call-azure-resource-manager"></a>Obtenir un jeton d’accès à l’aide de l’identité managée attribuée par le système de la machine virtuelle pour appeler Azure Resource Manager 

Pour la suite de ce didacticiel, nous allons utiliser la machine virtuelle que nous avons créée précédemment. 

Dans cette partie, vous devez utiliser les applets de commande PowerShell d’Azure Resource Manager.  Si vous ne l’avez pas installé, [téléchargez la dernière version](https://docs.microsoft.com/powershell/azure/overview) avant de continuer.

1. Dans le portail Azure, accédez à **Machines virtuelles**, accédez à votre machine virtuelle Windows, puis, en haut de la page **Vue d’ensemble**, cliquez sur **Se connecter**. 
2. Entrez le **Nom d’utilisateur** et le **Mot de passe** que vous avez ajoutés lorsque vous avez créé la machine virtuelle Windows. 
3. Maintenant que vous avez créé une **Connexion au Bureau à distance** avec la machine virtuelle, ouvrez PowerShell dans la session à distance.
4. À l’aide d’Invoke-WebRequest de Powershell, envoyez une requête à l’identité managée locale pour le point de terminaison de ressources Azure afin d’obtenir un jeton d’accès pour Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > La valeur du paramètre « ressource » doit correspondre exactement à ce qui est attendu par Azure AD. Lorsque vous utilisez l’ID de ressource Azure Resource Manager, vous devez inclure la barre oblique de fin à l’URI.
    
    Ensuite, extrayez l’élément « Content » qui est stocké sous forme de chaîne au format JSON (JavaScript Objet Notation) dans l’objet $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Ensuite, extrayez le jeton d’accès de la réponse.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Obtenir les clés d’accès du compte de stockage à partir d’Azure Resource Manager pour effectuer des appels de stockage  

Maintenant, utilisez PowerShell pour appeler le Gestionnaire des ressources à l’aide du jeton d’accès récupéré dans la section précédente, afin de récupérer la clé d’accès de stockage. Une fois la clé d’accès de stockage récupérée, nous pouvons appeler des opérations de chargement/téléchargement de stockage.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> L’URL respecte la casse, par conséquent, assurez-vous que la casse est identique à celle utilisée précédemment lorsque vous avez nommé le groupe de ressources, et que la majuscule « G » est correcte dans « resourceGroup. » 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

Nous créons ensuite un fichier appelé « test.txt ». Ensuite, utilisez la clé d’accès de stockage pour vous authentifier à l’aide de l’applet de commande `New-AzStorageContent`, puis chargez le fichier dans notre conteneur d’objets blob et téléchargez-le.

```bash
echo "This is a test text file." > test.txt
```

Veillez à installer les applets de commande du stockage Azure en premier, à l’aide de `Install-Module Az.Storage`. Vous pouvez charger l’objet blob que vous venez de créer à l’aide de l’applet de commande PowerShell `Set-AzStorageBlobContent`:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Réponse :

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Vous pouvez également télécharger l’objet blob que vous venez de charger, à l’aide de l’applet de commande PowerShell `Get-AzStorageBlobContent` :

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Réponse :

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à créer une identité managée attribuée par le système pour accéder au Stockage Azure à l’aide d’une clé d’accès.  Pour en savoir plus sur les clés d’accès du stockage Azure, consultez :

> [!div class="nextstepaction"]
>[Gérer vos clés d’accès de stockage](/azure/storage/common/storage-create-storage-account)

