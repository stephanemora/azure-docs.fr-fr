---
title: Mettre à niveau le Stockage Blob Azure avec les fonctionnalités de Azure Data Lake Storage Gen2 | Microsoft Docs
description: Montre comment utiliser des modèles de Gestionnaire des ressources pour effectuer une mise à niveau de Stockage Blob Azure vers Data Lake Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: normesta
ms.openlocfilehash: 3f6b82e447fec4ce4e1c2e9b1f700f9b976d6a3d
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994381"
---
#  <a name="upgrade-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Mettre à niveau le Stockage Blob Azure avec les fonctionnalités de Azure Data Lake Storage Gen2

Cet article vous aide à déverrouiller des fonctionnalités telles que la sécurité au niveau des fichiers et des répertoires et l’accélération des opérations. Ces fonctionnalités sont largement utilisées par les charges de travail d’analyse du Big Data et sont désignées collectivement comme des Azure Data Lake Storage Gen2. 

Pour plus d’informations sur ces fonctionnalités et pour évaluer l’impact de cette mise à niveau sur les charges de travail, les applications, les coûts, les intégrations de service, les outils, les fonctionnalités et la documentation, consultez [Mise à niveau du Stockage Blob Azure avec les fonctionnalités d’Azure Data Lake Storage Gen2](upgrade-to-data-lake-storage-gen2.md).

> [!IMPORTANT]
> La mise à niveau est irréversible. Il n’existe aucun moyen de rétablir la version antérieure de votre compte une fois que vous avez effectué la mise à niveau. Nous vous recommandons de valider votre mise à niveau dans un environnement de non-production.

## <a name="review-feature-support"></a>Vérifier la prise en charge des fonctionnalités

Votre compte est peut-être configuré pour utiliser des fonctionnalités qui ne sont pas encore prises en charge par les comptes Data Lake Storage Gen2. Si votre compte utilise une fonctionnalité qui n’est pas encore prise en charge, la mise à niveau ne réussira pas l’étape de validation. 

Consultez l’article [Prise en charge des fonctionnalités de Stockage Blob dans les comptes de stockage Azure](storage-feature-support-in-storage-accounts.md) pour identifier les fonctionnalités qui ne sont pas prises en charge. Si vous utilisez l’une de ces fonctionnalités non prises en charge dans votre compte, veillez à la désactiver avant de commencer la mise à niveau.

## <a name="perform-the-upgrade"></a>Effectuer la mise à niveau

### <a name="portal"></a>[Portail](#tab/azure-portal)

1. Pour commencer, connectez-vous au [portail Azure](https://portal.azure.com/).

2. Recherchez votre compte de stockage et affichez la vue d’ensemble du compte.

3. Sélectionnez **Migration vers Data Lake Gen2**.

   La page de configuration **Mise à niveau vers un compte de stockage avec des fonctionnalités de Data Lake Storage Gen2** s’affiche.

   > [!div class="mx-imgBorder"]
   > ![Page Configuration](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-page.png)

4. Développez la section **Étape 1 : vérifier les modifications de compte avant la mise à niveau**, puis cliquez sur **Vérifier et accepter les modifications**.

5. Sur la page **Vérifier les modifications de compte**, cochez la case, puis cliquez sur **Accepter les modifications**.

6. Développez la section **Étape 2 : valider le compte avant la mise à niveau**, puis cliquez sur **Démarrer la validation**.

   Si la validation échoue, une erreur s’affiche dans la page. Dans certains cas, un lien **Afficher les erreurs** apparaît. Si ce lien apparaît, sélectionnez-le. 

   > [!div class="mx-imgBorder"]
   > ![Lien Afficher les erreurs](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-errors.png)

   Ensuite, dans le menu contextuel du fichier **error.json**, sélectionnez **Télécharger**.

   > [!div class="mx-imgBorder"]
   > ![Page Erreur JSON](./media/upgrade-to-data-lake-storage-gen2-how-to/error-json.png)

   Ouvrez le fichier téléchargé pour déterminer la raison pour laquelle le compte n’a pas passé l’étape de validation. 

   Le code JSON suivant indique qu’une fonctionnalité incompatible est activée sur le compte. Dans ce cas, vous devez désactiver la fonctionnalité, puis relancer le processus de validation.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

7. Une fois votre compte validé, développez la section **Étape 3 : Mettre à niveau le compte**, puis cliquez sur **Démarrer la mise à niveau**.

   > [!IMPORTANT]
   > Les opérations d’écriture sont désactivées lors de la mise à niveau de votre compte. Les opérations de lecture ne sont pas désactivées, mais nous vous recommandons vivement de suspendre les opérations de lecture, car elles peuvent déstabiliser le processus de mise à niveau.

   Une fois la migration terminée, un message similaire à ce qui suit s’affiche. 

   > [!div class="mx-imgBorder"]
   > ![Page La migration est terminée](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-completed.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Ouvrez une fenêtre Commande Windows PowerShell.

2. Vérifiez que la dernière version du module Azure PowerShell est installée. Consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps).

3. Connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount` et suivez les instructions à l’écran.

   ```powershell
   Connect-AzAccount
   ```

4. Si votre identité est associée à plusieurs abonnements, définissez votre abonnement actif.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Remplacez la valeur d’espace réservé `<subscription-id>` par l’ID de votre abonnement.

6. Validez votre compte de stockage en utilisant la commande suivante.

   ```powershell
   $result = Invoke-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>" -RequestType Validation -AsJob
   ```

   - Remplacez la valeur d’espace réservé `<resource-group-name>` par le nom de votre groupe de ressources.

   - Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage.

   Selon la taille de votre compte, ce processus peut prendre un certain temps. Vous pouvez utiliser le commutateur `asJob` pour exécuter la commande dans un travail en arrière-plan afin que votre client ne soit pas bloqué. La commande s’exécute à distance, mais le travail existe sur votre ordinateur local ou sur la machine virtuelle à partir de laquelle vous exécutez la commande. Les résultats sont transmis à votre ordinateur local ou à la machine virtuelle.

7. Pour vérifier l’état du travail et afficher toutes les propriétés du travail dans une liste, dirigez la variable de retour vers la cmdlet `Format-List`. 

   ```powershell
   $result | Format-List -Property *
   ```

   Si la validation réussit, la propriété **État** sera définie sur **Terminé**.

   Si la validation échoue, la propriété **État** sera définie sur **Échec**, et la propriété **Erreur** indiquera les erreurs de validation. 

   La sortie suivante indique qu’une fonctionnalité incompatible est activée sur le compte. Dans ce cas, vous devez désactiver la fonctionnalité, puis relancer le processus de validation.

   > [!div class="mx-imgBorder"]
   > ![Erreur de validation](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-error-powershell.png)

   Dans certains cas, la propriété **Erreur** vous fournit un chemin d’accès à un fichier nommé **error.json**. Vous pouvez ouvrir ce fichier pour déterminer la raison pour laquelle le compte n’a pas réussi l’étape de validation. 

   Le code JSON suivant indique qu’une fonctionnalité incompatible est activée sur le compte. Dans ce cas, vous devez désactiver la fonctionnalité, puis relancer le processus de validation.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

8. Une fois que votre compte a réussi la validation, démarrez la mise à niveau en exécutant la commande suivante.
   
   ```powershell
   $result = Invoke-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>" -RequestType Upgrade -AsJob -Force
   ```

   À l’instar de l’exemple de validation ci-dessus, cet exemple utilise le commutateur `asJob` pour exécuter la commande dans un travail en arrière-plan. Le commutateur `Force` remplace les invites pour confirmer la mise à niveau.  Si vous n’utilisez pas le commutateur `AsJob`, vous n’êtes pas obligé d’utiliser le commutateur `Force`, car vous pouvez simplement répondre aux invites.

   > [!IMPORTANT]
   > Les opérations d’écriture sont désactivées lors de la mise à niveau de votre compte. Les opérations de lecture ne sont pas désactivées, mais nous vous recommandons vivement de suspendre les opérations de lecture, car elles peuvent déstabiliser le processus de mise à niveau.

   Pour vérifier l’état du travail, utilisez les mêmes techniques que celles décrites dans les étapes précédentes. Pendant l’exécution du processus, la propriété **État** sera définie sur **Exécution en cours**.

   Une fois la migration terminée, la propriété **État** sera définie sur **Terminé** et la propriété **Erreur** n’indiquera aucune erreur.
   
   > [!div class="mx-imgBorder"]
   > ![Sortie d’une opération réussie](./media/upgrade-to-data-lake-storage-gen2-how-to/success-message-powershell.png)


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Commencez par ouvrir [Azure Cloud Shell](../../cloud-shell/overview.md) ou, si vous avez [installé](/cli/azure/install-azure-cli) Azure CLI localement, ouvrez une application console de commandes telle que Windows PowerShell.

2. Vérifiez que la version `2.29.0` d’Azure CLI ou une version supérieure est installée à l’aide de la commande suivante.

   ```azurecli
    az --version
   ```

   Si la version d’Azure CLI est inférieure à `2.29.0`, installez la version la plus récente. Pour plus d’informations, consultez la rubrique [Installation de l’interface de ligne de commande Azure (CLI)](/cli/azure/install-azure-cli).

2. Si votre identité est associée à plusieurs abonnements, définissez votre abonnement actif.

   ```azurecli
      az account set --subscription <subscription-id>
   ```

   Remplacez la valeur d’espace réservé `<subscription-id>` par l’ID de votre abonnement.

3. Validez votre compte de stockage en utilisant la commande suivante.

   ```azurecli
   az storage account hns-migration start --type validation -n <storage-account-name> -g <resource-group-name>
   ```

   - Remplacez la valeur d’espace réservé `<resource-group-name>` par le nom de votre groupe de ressources.

   - Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage.

   Si la validation réussit, le processus se termine et aucune erreur ne s’affiche.
   
   Si la validation échoue, une erreur de validation s’affiche dans la console. Par exemple, l’erreur `(IncompatibleValuesForAccountProperties) Values for account properties are incompatible: Versioning Enabled` indique qu’une fonctionnalité incompatible (Contrôle de version) est activée sur le compte. Dans ce cas, vous devez désactiver la fonctionnalité, puis relancer le processus de validation.

   Dans certains cas, le chemin d’accès à un fichier nommé **error.json** s’affiche dans la console. Vous pouvez ouvrir ce fichier pour déterminer la raison pour laquelle le compte n’a pas réussi l’étape de validation. 

   Le code JSON suivant indique qu’une fonctionnalité incompatible est activée sur le compte. Dans ce cas, vous devez désactiver la fonctionnalité, puis relancer le processus de validation.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

5. Une fois que votre compte a réussi la validation, démarrez la mise à niveau en exécutant la commande suivante.
   
   ```azurecli
   az storage account hns-migration start --type upgrade -n storage-account-name -g <resource-group-name>
   ```

   > [!IMPORTANT]
   > Les opérations d’écriture sont désactivées lors de la mise à niveau de votre compte. Les opérations de lecture ne sont pas désactivées, mais nous vous recommandons vivement de suspendre les opérations de lecture, car elles peuvent déstabiliser le processus de mise à niveau.

   Si la migration réussit, le processus se termine et aucune erreur ne s’affiche.

---

## <a name="stop-the-upgrade"></a>Arrêter la mise à niveau

Vous pouvez arrêter la migration avant qu’elle ne soit terminée. Voici ce qui se passe lorsque vous l’arrêtez.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour arrêter la mise à niveau avant qu’elle ne soit terminée, sélectionnez **Annuler la mise à niveau** pendant que la mise à niveau est en cours.

> [!div class="mx-imgBorder"]
> ![Annuler la mise à niveau](./media/upgrade-to-data-lake-storage-gen2-how-to/cancel-the-upgrade.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour arrêter la mise à niveau avant qu’elle ne soit terminée, utilisez la commande `Stop-AzStorageAccountHierarchicalNamespaceUpgrade`.

```powershell
Stop-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName <resource-group-name> -Name <storage-account-name>
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour arrêter la mise à niveau avant qu’elle ne soit terminée, utilisez la commande `az storage account hns-migration stop`.

```azurecli
az storage account hns-migration stop -n <storage-account-name> -g <resource-group-name>
```

---


## <a name="migrate-data-workloads-and-applications"></a>Migrer les données, les charges de travail et les applications 

1. Configurez [des services dans vos charges de travail](data-lake-storage-integrate-with-azure-services.md) pour qu’ils pointent vers le point de terminaison du **service BLOB** ou le point de terminaison de **Data Lake Storage** .

   > [!div class="mx-imgBorder"]
   > ![Points de terminaison de compte](./media/upgrade-to-data-lake-storage-gen2-how-to/storage-endpoints.png)
  
3. Pour les charges de travail Hadoop qui utilisent le pilote Windows Azure Storage Blob ou le pilote [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html), veillez à les modifier pour utiliser le pilote [Azure Blob File System (ABFS)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html). Contrairement au pilote WASB qui effectue des demandes au point de terminaison du **service BLOB**, le pilote ABFS envoie des demandes au point de terminaison de **Data Lake Storage** de votre compte.

2. Testez les applications personnalisées pour vous assurer qu’elles fonctionnent comme prévu avec votre compte mis à niveau. 

   L’[accès multiprotocole sur Data Lake Storage](data-lake-storage-multi-protocol-access.md) permet à la plupart des applications de continuer à utiliser les API d’objets Blob sans modification. Si vous rencontrez des problèmes ou si vous souhaitez utiliser des API pour travailler avec des ACL et des opérations d’annuaire, envisagez de déplacer une partie de votre code pour utiliser des API Data Lake Storage Gen2. Consultez les guides concernant [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [Node.js](data-lake-storage-acl-javascript.md) et [REST](/rest/api/storageservices/data-lake-storage-gen2). 

3. Testez des scripts personnalisés pour vous assurer qu’elles fonctionnent comme prévu avec votre compte mis à niveau. 

   Comme c’est le cas avec les API d’objets BLOB, un grand nombre de vos scripts seront probablement opérationnels sans que vous ayez à les modifier. Toutefois, si nécessaire, vous pouvez mettre à jour les fichiers de scripts pour qu’ils utilisent les [applets de commande PowerShell](data-lake-storage-directory-file-acl-powershell.md) Data Lake Storage Gen2 et les [commandes Azure CLI](data-lake-storage-directory-file-acl-cli.md).
 

## <a name="see-also"></a>Voir aussi

[Présentation d'Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)