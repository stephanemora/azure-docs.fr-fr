---
title: Tutoriel pour exporter des données du stockage Blob Azure avec Azure Import/Export | Microsoft Docs
description: Découvrez comment créer des tâches d’exportation dans le Portail Azure pour transférer des données à partir d’objets blob Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.custom: tutorial, devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: ccda14f4046efe32370b206577807dca558d9f25
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709655"
---
# <a name="tutorial-export-data-from-azure-blob-storage-with-azure-importexport"></a>Tutoriel : Exporter des données du stockage Blob Azure avec Azure Import/Export

Cet article fournit des instructions pas à pas sur l’utilisation du service Azure Import/Export pour exporter en toute sécurité de grandes quantités de données à partir du Stockage Blob Azure. Pour pouvoir utiliser ce service, vous devez expédier des lecteurs vides au centre de données Azure. Le service exporte les données de votre compte de stockage vers les lecteurs, puis vous réexpédie ces derniers.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Prérequis pour exporter des données du stockage Blob Azure avec Azure Import/Export
> * Étape 1 : Création d’une tâche d’exportation
> * Étape 2 : Expédier les disques
> * Étape 3 : Mettre à jour la tâche avec les informations de suivi
> * Étape 4 : Recevoir les disques
> * Étape 5 : Déverrouiller les disques

## <a name="prerequisites"></a>Prérequis

Avant de créer une tâche d’exportation pour transférer des données à partir du Stockage Blob Azure, passez en revue et respectez soigneusement la liste des prérequis de ce service.
Vous devez respecter les consignes suivantes :

- Avoir un abonnement Azure actif utilisable avec le service Import/Export
- Avoir au moins un compte de stockage Azure. Consultez la liste des [Comptes de stockage et types de stockage pris en charge pour le service Import/Export](storage-import-export-requirements.md). Pour plus d'informations sur la création d'un compte de stockage, consultez la page [Création d'un compte de stockage](../storage/common/storage-account-create.md).
- Avoir un nombre suffisant de disques correspondant aux [types pris en charge](storage-import-export-requirements.md#supported-disks). Vous pouvez utiliser l’outil Azure Import/Export pour déterminer le nombre de disques à fournir. Pour connaître les étapes, consultez [Déterminer les lecteurs à utiliser](storage-import-export-determine-drives-for-export.md#determine-how-many-drives-you-need).
- Dotez-vous d’un compte FedEx/DHL. Si vous souhaitez faire appel à un autre transporteur que FedEx/DHL, contactez l’équipe des opérations Azure Data Box à l’adresse `adbops@microsoft.com`.
  - Le compte doit être valide, doit avoir un solde et doit offrir des fonctionnalités de réexpédition.
  - Générez un numéro de suivi pour le travail d’exportation.
  - Chaque travail doit avoir un numéro de suivi distinct. Plusieurs travaux portant le même numéro de suivi ne sont pas pris en charge.
  - Si vous n’avez pas de compte de transporteur, accédez à :
    - [Créer un compte FedEx](https://www.fedex.com/en-us/create-account.html), ou
    - [Créer un compte DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Étape 1 : Création d’une tâche d’exportation

### <a name="portal"></a>[Portail](#tab/azure-portal)

Effectuez les étapes suivantes pour créer une tâche d’exportation dans le Portail Azure.

1. Connectez-vous sur <https://portal.azure.com/>.
2. Recherchez les **tâches d’importation/exportation**.

    ![Rechercher des tâches d’importation/exportation](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Sélectionnez **+Nouveau**.

    ![Sélectionner + Nouveau pour en créer une ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. Dans **De base** :

   1. Sélectionnez un abonnement.
   1. Sélectionnez un groupe de ressources ou sélectionnez **Créer**, puis créez-en un.
   1. Indiquez un nom décrivant le travail d’importation. Utilisez le nom pour suivre la progression de vos tâches.
       * Le nom ne peut contenir que des lettres minuscules, des chiffres et des traits d’union.
       * Le nom doit commencer par une lettre et ne doit pas contenir d’espaces.

   1. Sélectionnez **Exporter à partir d’Azure**.

    ![Options de base pour un ordre d’exportation](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    Sélectionnez **Suivant : Détails du travail >** pour continuer.

5. Dans **Détails de la tâche** :

   1. Sélectionnez la région Azure où vos données se trouvent actuellement.
   1. Sélectionnez le compte de stockage à partir duquel vous voulez exporter des données. Utilisez un compte de stockage proche de votre emplacement.

      L’emplacement de remise est automatiquement rempli en fonction de la région du compte de stockage sélectionné.

   1. Indiquez les données BLOB à exporter depuis votre compte de stockage vers le ou les lecteurs vides. Choisissez une des trois méthodes suivantes.

      - Choisissez l’option **Exporter tout** pour exporter l’ensemble des données blob du compte de stockage.

        ![Exporter tout](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - Choisissez **Conteneurs et objets BLOB sélectionnés**, puis précisez les conteneurs et les objets BLOB à exporter. Vous pouvez utiliser plusieurs méthodes de sélection. L’option **Ajouter** ouvre un panneau à droite dans lequel vous pouvez ajouter vos chaînes de sélection.

        |Option|Description|
        |------|-----------|      
        |**Ajouter des conteneurs**|Exportez tous les objets BLOB dans un conteneur.<br>Sélectionnez **Ajouter des conteneurs**, puis entrez le nom de chaque conteneur.|
        |**Ajouter des objets BLOB**|Précisez les objets BLOB à exporter.<br>Sélectionnez **Ajouter des objets BLOB**. Ensuite, indiquez le chemin relatif de l’objet BLOB, en commençant par le nom du conteneur. Utilisez *$root* pour spécifier le conteneur racine.<br>Vous devez indiquer les chemins d’objets blob dans un format valide pour éviter les erreurs de traitement, comme illustré dans cette capture d’écran. Pour plus d’informations, consultez [Exemples de chemins d’objets blob valides](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths).|
        |**Ajouter des préfixes**|Utilisez un préfixe pour sélectionner un ensemble de conteneurs portant le même nom ou des objets BLOB de même nom dans un conteneur. Il peut s’agir du préfixe du nom de conteneur, du nom de conteneur complet, ou du nom de conteneur complet suivi du préfixe du nom de l’objet BLOB. |

        ![Exporter les conteneurs et objets blob sélectionnés](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - Choisissez **Exporter à partir d’un fichier de liste d’objets BLOB (format XML)** , puis sélectionnez un fichier XML qui contient une liste de chemins d’accès et de préfixes pour les objets BLOB à exporter à partir du compte de stockage. Vous devez créer le fichier XML et le stocker dans un conteneur pour le compte de stockage. Le fichier ne peut pas être vide.

      > [!IMPORTANT]
      > Si vous utilisez un fichier XML pour sélectionner les objets BLOB à exporter, assurez-vous que le code XML contient des chemins d’accès et/ou des préfixes valides. Si le fichier n’est pas valide ou si aucune donnée ne correspond aux chemins spécifiés, la commande contient des données partielles ou aucune donnée n’est exportée.

       Pour savoir comment ajouter un fichier XML à un conteneur, consultez [Commande d’exportation à l’aide d’un fichier XML](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file).

      ![Exporter à partir d’un fichier de liste d’objets blob](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > Si l’objet blob à exporter est utilisé pendant la copie de données, le service Azure Import/Export prend une capture de l’objet BLOB, puis copie cette capture.

   Sélectionnez **Suivant : Expédition >** pour continuer.

6. Dans **Expédition** :

    - Sélectionnez le transporteur dans la liste déroulante. Si vous souhaitez utiliser un autre transporteur que FedEx/DHL, choisissez une option existante dans la liste déroulante. Contactez l’équipe des opérations Azure Data Box à l’adresse `adbops@microsoft.com` pour lui indiquer le nom du transporteur auquel vous envisagez de faire appel.
    - Entrez un numéro de compte de transporteur valide que vous avez créé pour ce transporteur. Microsoft utilise ce compte pour renvoyer les lecteurs une fois la tâche d’exportation terminée.
    - Indiquez le nom, le numéro de téléphone, l'e-mail, l'adresse, la ville, le code postal, l'état/la province et le pays/la région d'un contact, puis vérifiez que ces informations sont complètes et valides.

        > [!TIP]
        > Au lieu de spécifier une adresse de messagerie pour un seul utilisateur, fournissez une adresse de groupe. Cela garantit que vous recevrez des notifications même si un administrateur s’en va.

    Sélectionnez **Vérifier + créer** pour continuer.

7. Dans **Vérifier + créer** :

   1. Passez en revue les détails de la tâche.
   1. Notez le nom de la tâche et l’adresse de livraison du centre de données Azure pour l’expédition des disques.

      > [!NOTE]
      > Envoyer toujours les disques au centre de données indiqué dans le portail Azure. Si les disques ne sont pas expédiés au centre de données approprié, le travail ne sera pas exécuté.

   1. Passez en revue les **Conditions** de votre commande pour la confidentialité et la suppression des données sources. Si vous acceptez les conditions générales, cochez la case qui se trouve sous ces conditions. La validation de la commande commence.

   ![Vérifier et créer votre ordre d’exportation](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. Une fois la validation réussie, sélectionnez **Créer**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Suivez les étapes ci-dessous pour créer une tâche d’exportation dans le portail Azure.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Créer un travail

1. Utilisez la commande [az extension add](/cli/azure/extension#az_extension_add) pour ajouter l’extension [az import-export](/cli/azure/import-export) :

    ```azurecli
    az extension add --name import-export
    ```

1. Pour obtenir la liste des emplacements depuis lesquels vous pouvez recevoir des disques, utilisez la commande [az import-export location list](/cli/azure/import-export/location#az_import_export_location_list) :

    ```azurecli
    az import-export location list
    ```

1. Exécutez la commande [az import-export create](/cli/azure/import-export#az_import_export_create) pour créer un travail d’exportation utilisant votre compte de stockage existant :

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > Au lieu de spécifier une adresse de messagerie pour un seul utilisateur, fournissez une adresse de groupe. Cela garantit que vous recevrez des notifications même si un administrateur s’en va.

   Ce travail exporte tous les objets blob dans votre compte de stockage. Vous pouvez spécifier un objet blob à exporter en remplaçant cette valeur par **--export** :

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   Cette valeur de paramètre exporte l’objet blob nommé *logo.bmp* dans le conteneur racine.

   Vous pouvez également sélectionner tous les objets blob d’un conteneur à l’aide d’un préfixe. Remplacez cette valeur pour **--export** :

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   Pour plus d’informations, consultez [Exemples de chemins d’objets blob valides](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths).

   > [!NOTE]
   > Si l’objet blob à exporter est utilisé pendant la copie de données, le service Azure Import/Export prend un instantané de l’objet blob, puis copie cet instantané.

1. Utilisez la commande [az import-export list](/cli/azure/import-export#az_import_export_list) pour afficher toutes les tâches du groupe de ressources myierg :

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Pour mettre à jour votre travail ou l’annuler, exécutez la commande [az import-export update](/cli/azure/import-export#az_import_export_update) :

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Exécutez les étapes suivantes pour créer une tâche d’exportation dans Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Tant que le module PowerShell **Az.ImportExport** est en préversion, vous devez l’installer séparément à l’aide de la cmdlet `Install-Module`. Une fois que ce module PowerShell sera en disponibilité générale, il fera partie intégrante des versions futures du module Az PowerShell et sera disponible par défaut dans Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Créer un travail

1. Pour obtenir la liste des emplacements à partir desquels vous pouvez recevoir des disques, utilisez la cmdlet [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) :

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Exécutez l’exemple [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) suivant pour créer une tâche d’exportation utilisant votre compte de stockage existant :

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > Au lieu de spécifier une adresse de messagerie pour un seul utilisateur, fournissez une adresse de groupe. Cela garantit que vous recevrez des notifications même si un administrateur s’en va.

   Ce travail exporte tous les objets blob dans votre compte de stockage. Vous pouvez spécifier un blob à exporter en remplaçant cette valeur par **-ExportBlobListblobPath** :

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   Cette valeur de paramètre exporte l’objet blob nommé *logo.bmp* dans le conteneur racine.

   Vous pouvez également sélectionner tous les objets blob d’un conteneur à l’aide d’un préfixe. Remplacez cette valeur par **-ExportBlobListblobPath** :

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   Pour plus d’informations, consultez [Exemples de chemins d’objets blob valides](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths).

   > [!NOTE]
   > Si l’objet blob à exporter est utilisé pendant la copie de données, le service Azure Import/Export prend un instantané de l’objet blob, puis copie cet instantané.

1. Utilisez la cmdlet [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) pour afficher toutes les tâches du groupe de ressources myierg :

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Pour mettre à jour votre travail ou l’annuler, exécutez la cmdlet [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) :

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-2-ship-the-drives"></a>Étape 2 : Expédier les disques

Si vous ne connaissez pas le nombre de lecteurs dont vous avez besoin, consultez [Déterminer le nombre de lecteurs dont vous avez besoin](storage-import-export-determine-drives-for-export.md#determine-how-many-drives-you-need). Si vous connaissez le nombre de disques nécessaires, expédiez-les.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Étape 3 : Mettre à jour la tâche avec les informations de suivi

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Étape 4 : Recevoir les disques

Quand le tableau de bord indique que la tâche a été effectuée, les disques vous sont expédiés. Le numéro de suivi de l’envoi est disponible sur le portail.

1. Une fois que vous avez reçu les disques contenant les données exportées, vous devez obtenir les clés BitLocker pour les déverrouiller. Accédez à la tâche d’exportation dans le Portail Azure. Cliquez sur l’onglet **Import/Export**.
2. Dans la liste, sélectionnez votre tâche d’exportation, puis cliquez sur celle-ci. Accédez à **Chiffrement**, puis copiez les clés.

   ![Afficher les clés BitLocker pour une tâche d'exportation](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. Utilisez les clés BitLocker pour déverrouiller les disques.

L’exportation est effectuée.

## <a name="step-5-unlock-the-disks"></a>Étape 5 : Déverrouiller les disques

Utilisez la commande suivante pour déverrouiller le disque :

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

Voici un exemple de ces données en entrée.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

À ce stade, vous pouvez supprimer la tâche ou la conserver. Les tâches sont automatiquement supprimées au bout de 90 jours.

## <a name="next-steps"></a>Étapes suivantes

* [Voir l’état de la tâche et des disques](storage-import-export-view-drive-status.md)
* [Passer en revue les journaux de copie Import/Export](storage-import-export-tool-reviewing-job-status-v1.md)
