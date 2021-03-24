---
title: Utilisation d’Azure Import/Export pour exporter des données à partir d’objets blob Azure | Microsoft Docs
description: Découvrez comment créer des tâches d’exportation dans le Portail Azure pour transférer des données à partir d’objets blob Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: e878be5351362923e163c0a6f617b96ab72a36d8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177556"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Utilisation du service Azure Import/Export pour exporter des données à partir du Stockage Blob Azure

Cet article fournit des instructions pas à pas sur l’utilisation du service Azure Import/Export pour exporter en toute sécurité de grandes quantités de données à partir du Stockage Blob Azure. Pour pouvoir utiliser ce service, vous devez expédier des lecteurs vides au centre de données Azure. Le service exporte les données de votre compte de stockage vers les lecteurs, puis vous réexpédie ces derniers.

## <a name="prerequisites"></a>Prérequis

Avant de créer une tâche d’exportation pour transférer des données à partir du Stockage Blob Azure, passez en revue et respectez soigneusement la liste des prérequis de ce service.
Vous devez respecter les consignes suivantes :

- Avoir un abonnement Azure actif utilisable avec le service Import/Export
- Avoir au moins un compte de stockage Azure. Consultez la liste des [Comptes de stockage et types de stockage pris en charge pour le service Import/Export](storage-import-export-requirements.md). Pour plus d'informations sur la création d'un compte de stockage, consultez la page [Création d'un compte de stockage](../storage/common/storage-account-create.md).
- Avoir un nombre suffisant de disques correspondant aux [types pris en charge](storage-import-export-requirements.md#supported-disks).
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
        |**Ajouter des objets BLOB**|Précisez les objets BLOB à exporter.<br>Sélectionnez **Ajouter des objets BLOB**. Ensuite, indiquez le chemin relatif de l’objet BLOB, en commençant par le nom du conteneur. Utilisez *$root* pour spécifier le conteneur racine.<br>Vous devez indiquer les chemins d’objets blob dans un format valide pour éviter les erreurs de traitement, comme illustré dans cette capture d’écran. Pour plus d’informations, consultez [Exemples de chemins d’objets blob valides](#examples-of-valid-blob-paths).|
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

<!--Replaced text: Steps 4 - end of "Create an export job." Wizard design changes required both screen and text updates.

4. In **Basics**:

    - Select **Export from Azure**.
    - Enter a descriptive name for the export job. Use the name you choose to track the progress of your jobs.
        - The name may contain only lowercase letters, numbers, hyphens, and underscores.
        - The name must start with a letter, and may not contain spaces.
    - Select a subscription.
    - Enter or select a resource group.

        ![Basics](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

5. In **Job details**:

    - Select the storage account where the data to be exported resides. Use a storage account close to where you are located.
    - The dropoff location is automatically populated based on the region of the storage account selected.
    - Specify the blob data you wish to export from your storage account to your blank drive or drives.
    - Choose to **Export all** blob data in the storage account.

         ![Export all](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

    - You can specify which containers and blobs to export.
        - **To specify a blob to export**: Use the **Equal To** selector. Specify the relative path to the blob, beginning with the container name. Use *$root* to specify the root container.
        - **To specify all blobs starting with a prefix**: Use the **Starts With** selector. Specify the prefix, beginning with a forward slash '/'. The prefix may be the prefix of the container name, the complete container name, or the complete container name followed by the prefix of the blob name. You must provide the blob paths in valid format to avoid errors during processing, as shown in this screenshot. For more information, see [Examples of valid blob paths](#examples-of-valid-blob-paths).

           ![Export selected containers and blobs](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - You can export from  the blob list file.

        ![Export from blob list file](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > If the blob to be exported is in use during data copy, Azure Import/Export service takes a snapshot of the blob and copies the snapshot.

6. In **Return shipping info**:

    - Select the carrier from the dropdown list. If you want to use a carrier other than FedEx/DHL, choose an existing option from the dropdown. Contact Azure Data Box Operations team at `adbops@microsoft.com`  with the information regarding the carrier you plan to use.
    - Enter a valid carrier account number that you have created with that carrier. Microsoft uses this account to ship the drives back to you once your export job is complete.
    - Provide a complete and valid contact name, phone, email, street address, city, zip, state/province, and country/region.

        > [!TIP]
        > Instead of specifying an email address for a single user, provide a group email. This ensures that you receive notifications even if an admin leaves.

7. In **Summary**:

    - Review the details of the job.
    - Make a note of the job name and provided Azure datacenter shipping address for shipping disks to Azure.

        > [!NOTE]
        > Always send the disks to the datacenter noted in the Azure portal. If the disks are shipped to the wrong datacenter, the job will not be processed.

    - Click **OK** to complete export job creation.
-->

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Suivez les étapes ci-dessous pour créer une tâche d’exportation dans le portail Azure.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Créer un travail

1. Utilisez la commande [az extension add](/cli/azure/extension#az_extension_add) pour ajouter l’extension [az import-export](/cli/azure/ext/import-export/import-export) :

    ```azurecli
    az extension add --name import-export
    ```

1. Pour obtenir la liste des emplacements depuis lesquels vous pouvez recevoir des disques, utilisez la commande [az import-export location list](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) :

    ```azurecli
    az import-export location list
    ```

1. Exécutez la commande [az import-export create](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) pour créer un travail d’exportation utilisant votre compte de stockage existant :

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

   Pour plus d’informations, consultez [Exemples de chemins d’objets blob valides](#examples-of-valid-blob-paths).

   > [!NOTE]
   > Si l’objet blob à exporter est utilisé pendant la copie de données, le service Azure Import/Export prend un instantané de l’objet blob, puis copie cet instantané.

1. Utilisez la commande [az import-export list](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) pour afficher toutes les tâches du groupe de ressources myierg :

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Pour mettre à jour votre travail ou l’annuler, exécutez la commande [az import-export update](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) :

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

   Pour plus d’informations, consultez [Exemples de chemins d’objets blob valides](#examples-of-valid-blob-paths).

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

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>Étape 2 : Expédier les disques

Si vous ignorez le nombre de disques dont vous avez besoin, accédez à l’étape [Vérifier le nombre de disques](#check-the-number-of-drives). Si vous connaissez le nombre de disques nécessaires, expédiez-les.

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

## <a name="check-the-number-of-drives"></a>Vérifier le nombre de disques

Cette étape *facultative* vous permet de déterminer le nombre de lecteurs nécessaires pour la tâche d’exportation. Effectuez cette étape sur un système Windows exécutant une [version prise en charge du système d’exploitation](storage-import-export-requirements.md#supported-operating-systems).

1. [Téléchargez la version 1 de WAImportExport](https://www.microsoft.com/download/details.aspx?id=42659) sur le système Windows.
2. Décompressez le package dans le dossier par défaut : `waimportexportv1`. Par exemple : `C:\WaImportExportV1`.
3. Ouvrez une fenêtre PowerShell ou de ligne de commande avec des privilèges Administrateur. Pour accéder au répertoire du dossier décompressé, exécutez la commande suivante :

   `cd C:\WaImportExportV1`

4. Pour vérifier le nombre de disques nécessaires pour les objets blob sélectionnés, exécutez la commande suivante :

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Les paramètres sont décrits dans le tableau suivant :

    |Paramètre de ligne de commande|Description|
    |--------------------------|-----------------|
    |**/logdir:**|facultatif. Répertoire du journal. Les fichiers journaux détaillés sont écrits dans ce répertoire. Si ce paramètre n’est pas spécifié, le répertoire actif est utilisé en tant que répertoire de journaux.|
    |**/sn:**|Obligatoire. Nom du compte de stockage du travail d’exportation.|
    |**/sk:**|Obligatoire uniquement si aucun jeton SAP de conteneur n’est spécifié. Clé du compte de stockage du travail d’exportation.|
    |**/csas:**|Obligatoire uniquement si aucune clé de compte de stockage n’est spécifiée. SAP du conteneur pour lister les objets blob à exporter dans le travail d’exportation.|
    |**/ExportBlobListFile:**|Obligatoire. Chemin d’accès au fichier XML contenant la liste des chemins d’accès ou des préfixes de chemin d’accès aux objets blob à exporter. Format du fichier utilisé dans l’élément `BlobListBlobPath` dans l’opération [Put Job](/rest/api/storageimportexport/jobs) de l’API REST du service Import/Export.|
    |**/DriveSize:**|Obligatoire. Taille des lecteurs à utiliser pour une tâche d’exportation, *par exemple*, 500 Go, 1,5 To.|

    Consultez un [exemple de commande PreviewExport](#example-of-previewexport-command).

5. Vérifiez que vous pouvez accéder en lecture/écriture aux disques qui vont être expédiés pour la tâche d’exportation.

### <a name="example-of-previewexport-command"></a>Exemple de commande PreviewExport

L’exemple suivant illustre la commande `PreviewExport` :

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

Le fichier de liste d’objets blob à exporter peut contenir des noms et des préfixes d’objets blob, comme l’illustre ce code :

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

L’outil Azure Import/Export liste tous les objets blob à exporter et calcule leur répartition sur des lecteurs de la taille spécifiée, en prenant en compte les éventuelles surcharges nécessaires, puis évalue le nombre de disques requis pour contenir les objets blob et les informations sur l’utilisation des lecteurs.

Voici un exemple de sortie, les journaux d’activité d’information étant omis :

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="examples-of-valid-blob-paths"></a>Exemples de chemins d’objets blob valides

Le tableau suivant présente des exemples de chemins d’accès d’objet blob valides :

   | Sélecteur | Chemin d'accès d'objet blob | Description |
   | --- | --- | --- |
   | Starts With |/ |Exporte tous les objets blob présents dans le compte de stockage. |
   | Starts With |/$root/ |Exporte tous les objets blob présents dans le conteneur racine. |
   | Starts With |/book |Exporte tous les objets blob présents dans un conteneur commençant par le préfixe **book** |
   | Starts With |/music/ |Exporte tous les objets blob présents dans le conteneur **music** |
   | Starts With |/music/love |Exporte tous les objets blob présents dans le conteneur **music** qui commencent par le préfixe **love**. |
   | Equal To |$root/logo.bmp |Exporte l'objet blob **logo.bmp** présent dans le conteneur racine. |
   | Equal To |videos/story.mp4 |Exporte l’objet blob **story.mp4** présent dans le conteneur **videos**. |

## <a name="next-steps"></a>Étapes suivantes

- [Voir l’état de la tâche et des disques](storage-import-export-view-drive-status.md)
- [Passer en revue les exigences d’importation/exportation](storage-import-export-requirements.md)
