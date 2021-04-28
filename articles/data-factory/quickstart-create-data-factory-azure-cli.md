---
title: 'Démarrage rapide : Créer une fabrique de données Azure avec Azure CLI'
description: Ce guide de démarrage rapide crée une fabrique de données Azure, dont un service lié, des jeux de données et un pipeline. Vous pouvez exécuter le pipeline pour effectuer une action de copie de fichiers.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: b40407f4c4fb81bbf76bd0b552f3c9f2c827232a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871504"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Démarrage rapide : Créer une fabrique de données Azure avec Azure CLI

Ce guide de démarrage rapide explique comment utiliser Azure CLI pour créer une fabrique de données Azure. Le pipeline que vous créez dans cette fabrique de données copie les données d’un dossier vers un autre dans un stockage Blob Azure. Pour plus d’informations sur la façon de transformer des données à l’aide d’Azure Data Factory, consultez [Transformer des données dans Azure Data Factory](transform-data.md).

Pour une présentation du service Azure Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Pour créer des instances Data Factory, le compte d’utilisateur que vous utilisez pour vous connecter à Azure doit être membre des rôles Contributeur ou Propriétaire, ou administrateur de l’abonnement Azure. Pour plus d’informations, consultez [Rôles Azure](quickstart-create-data-factory-powershell.md#azure-roles).

## <a name="prepare-a-container-and-test-file"></a>Préparer un conteneur et un fichier de test

Ce guide de démarrage rapide utilise un compte de stockage Azure, qui comprend un conteneur avec un fichier.

1. Pour créer un groupe de ressources nommé `ADFQuickStartRG`, utilisez la commande [az group create](/cli/azure/group#az_group_create) :

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Pour créer un compte de stockage, utilisez la commande [az storage account create](/cli/azure/storage/container#az_storage_container_create) :

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. Pour créer un conteneur nommé `adftutorial`, utilisez la commande [az storage container create](/cli/azure/storage/container#az_storage_container_create) :

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. Dans le répertoire local, créez un fichier nommé `emp.txt` à charger. Si vous utilisez Azure Cloud Shell, vous pouvez trouver le répertoire de travail actuel à l’aide de la commande Bash `echo $PWD`. Vous pouvez utiliser des commandes Bash standard, telles que `cat`, pour créer un fichier :

   ```console
   cat > emp.txt
   This is text.
   ```

   Utilisez **Ctrl+D** pour enregistrer votre nouveau fichier.

1. Pour charger le nouveau fichier sur votre conteneur de stockage Azure, utilisez la commande [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) :

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   Cette commande effectue le chargement sur un nouveau dossier nommé `input`.

## <a name="create-a-data-factory"></a>Créer une fabrique de données

Pour créer une fabrique de données Azure, exécutez la commande [az datafactory factory create](/cli/azure/datafactory/factory#az_datafactory_factory_create) :

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Remplacez `ADFTutorialFactory` par un nom de fabrique de données global unique, par exemple ADFTutorialFactorySP1127.

Vous pouvez voir la fabrique de données que vous avez créée à l’aide de la commande [az datafactory factory show](/cli/azure/datafactory/factory#az_datafactory_factory_show) :

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Créer un service lié et des jeux de données

Ensuite, créez un service lié et deux jeux de données.

1. Obtenez la chaîne de connexion pour le compte de stockage avec la commande [az storage account show-connection-string](/cli/azure/datafactory/factory#az_datafactory_factory_show) :

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. Dans votre répertoire de travail, créez un fichier JSON avec ce contenu, qui comprend votre propre chaîne de connexion de l’étape précédente. Nommez le fichier `AzureStorageLinkedService.json` :

   ```json
   {
       "type":"AzureStorage",
           "typeProperties":{
           "connectionString":{
           "type": "SecureString",
           "value":"DefaultEndpointsProtocol=https;AccountName=adfquickstartstorage;AccountKey=K9F4Xk/EhYrMBIR98rtgJ0HRSIDU4eWQILLh2iXo05Xnr145+syIKNczQfORkQ3QIOZAd/eSDsvED19dAwW/tw==;EndpointSuffix=core.windows.net"
           }
       }
   }
   ```

1. Créez un service lié nommé `AzureStorageLinkedService` à l’aide de la commande [az datafactory linked-service create](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_create) :

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. Dans votre répertoire de travail, créez un fichier JSON avec ce contenu, nommé `InputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "input",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Créez un jeu de données d’entrée nommé `InputDataset` à l’aide de la commande [az datafactory dataset create](/cli/azure/datafactory/dataset#az_datafactory_dataset_create) :

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. Dans votre répertoire de travail, créez un fichier JSON avec ce contenu, nommé `OutputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "output",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Créez un jeu de données de sortie nommé `OutputDataset` à l’aide de la commande [az datafactory dataset create](/cli/azure/datafactory/dataset#az_datafactory_dataset_create) :

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>Créer et exécuter le pipeline

Enfin, créez et exécutez le pipeline.

1. Dans votre répertoire de travail, créez un fichier JSON avec ce contenu nommé `Adfv2QuickStartPipeline.json` :

   ```json
   {
       "name": "Adfv2QuickStartPipeline",
       "properties": {
           "activities": [
               {
                   "name": "CopyFromBlobToBlob",
                   "type": "Copy",
                   "dependsOn": [],
                   "policy": {
                       "timeout": "7.00:00:00",
                       "retry": 0,
                       "retryIntervalInSeconds": 30,
                       "secureOutput": false,
                       "secureInput": false
                   },
                   "userProperties": [],
                   "typeProperties": {
                          "source": {
                           "type": "BinarySource",
                           "storeSettings": {
                               "type": "AzureBlobStorageReadSettings",
                               "recursive": true
                           }
                       },
                       "sink": {
                           "type": "BinarySink",
                           "storeSettings": {
                               "type": "AzureBlobStorageWriteSettings"
                           }
                       },
                       "enableStaging": false
                   },
                   "inputs": [
                       {
                           "referenceName": "InputDataset",
                           "type": "DatasetReference"
                       }
                   ],
                   "outputs": [
                       {
                           "referenceName": "OutputDataset",
                           "type": "DatasetReference"
                       }
                   ]
               }
           ],
           "annotations": []
       }
   }
   ```

1. Créez un pipeline nommé `Adfv2QuickStartPipeline` à l’aide de la commande [az datafactory pipeline create](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create) :

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Exécutez le pipeline à l’aide de la commande [az datafactory pipeline create-run](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create_run) :

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   Cette commande retourne un ID d’exécution. Copiez-le pour l’utiliser dans la commande suivante.

1. Vérifiez que l’exécution du pipeline a réussi à l’aide de la commande [az datafactory pipeline-run show](/cli/azure/datafactory/pipeline-run#az_datafactory_pipeline_run_show) :

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

Vous pouvez également vérifier que votre pipeline s’est exécuté comme prévu avec le [portail Azure](https://portal.azure.com/). Pour plus d’informations, consultez [Vérifier les ressources déployées](quickstart-create-data-factory-powershell.md#review-deployed-resources).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Toutes les ressources de ce guide de démarrage rapide font partie du même groupe de ressources. Pour les supprimer toutes, utilisez la commande [az group delete](/cli/azure/group#az_group_delete) :

```azurecli
az group delete --name ADFQuickStartRG
```

Si vous utilisez ce groupe de ressources pour autre chose, supprimez plutôt des ressources individuelles. Par exemple, pour supprimer le service lié, utilisez la commande [az datafactory linked-service delete](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_delete).

Dans ce guide de démarrage rapide, vous avez créé les fichiers JSON suivants :

- AzureStorageLinkedService.json
- InputDataset.json
- OutputDataset.json
- Adfv2QuickStartPipeline.json

Supprimez-les en utilisant des commandes Bash standard.

## <a name="next-steps"></a>Étapes suivantes

- [Pipelines et activités dans Azure Data Factory](concepts-pipelines-activities.md)
- [Services liés dans Azure Data Factory](concepts-linked-services.md)
- [Jeux de données dans Azure Data Factory](concepts-datasets-linked-services.md)
- [Transformer des données dans Azure Data Factory](transform-data.md)
