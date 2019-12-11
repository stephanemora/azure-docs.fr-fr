---
title: Déployer un module de stockage Blob sur votre appareil - Azure IoT Edge
description: Déployez un module de stockage Blob Azure sur votre appareil IoT Edge pour stocker des données en périphérie.
author: arduppal
ms.author: arduppal
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
manager: mchad
ms.openlocfilehash: b89532038b00e28eb7c43232683349652af6bc3f
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665863"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Déployer le module de stockage Blob Azure sur IoT Edge vers votre appareil

Il existe plusieurs façons de déployer des modules sur un appareil IoT Edge, et toutes fonctionnent pour les modules de stockage Blob Azure sur IoT Edge. Les deux méthodes les plus simples consistent à utiliser les modèles de Visual Studio Code ou du portail Azure.

## <a name="prerequisites"></a>Prérequis

- Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) dans votre abonnement Azure.
- Un [appareil IoT Edge](how-to-register-device.md) avec le runtime IoT Edge installé.
- [Visual Studio Code](https://code.visualstudio.com/) et [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) si vous effectuez le déploiement à partir de Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Effectuer un déploiement à partir du portail Azure

Le Portail Azure vous aide à créer un manifeste de déploiement et à étendre le déploiement à un appareil IoT Edge.

### <a name="select-your-device"></a>Sélectionner votre appareil

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.
1. Sélectionnez **IoT Edge** dans le menu.
1. Cliquez sur l’ID de l’appareil cible dans la liste des appareils.
1. Sélectionnez **Définir modules**.

### <a name="configure-a-deployment-manifest"></a>Configurer un manifeste de déploiement

Un manifeste de déploiement est un document JSON qui décrit les modules à déployer, le flux des données entre les modules et les propriétés souhaitées des jumeaux de module. Le Portail Azure comprend un Assistant qui vous guide à travers la création du manifeste de déploiement, vous évitant de générer le document JSON manuellement. Il comporte trois étapes : **Ajouter des modules**, **Spécifier des routes** et **Vérifier le déploiement**.

#### <a name="add-modules"></a>Ajouter des modules

1. Dans la section **Modules de déploiement** de la page, sélectionnez **Ajouter**.

1. Dans les types de modules de la liste déroulante, sélectionnez **Module IoT Edge**.

1. Indiquez le nom du module, puis spécifiez l’image conteneur :

   - **Nom** : azureblobstorageoniotedge
   - **URI de l’image** : mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > Azure IoT Edge respecte la casse lorsque vous effectuez des appels de modules, et le SDK de stockage utilise également des minuscules par défaut. Même si le nom du module dans la [place de marché Azure](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) est **AzureBlobStorageonIoTEdge**, le passage de ce dernier en minuscules permet de s’assurer que vos connexions au module de stockage Blob Azure sur IoT Edge ne sont pas interrompues.

1. Les valeurs des **Options de création de conteneur** par défaut définissent les liaisons de port dont votre conteneur a besoin. Cependant, vous devez également ajouter les informations de votre compte de stockage et un montage pour le stockage sur votre appareil. Remplacez le code JSON par défaut dans le portail par le code JSON ci-dessous :

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Mettez à jour JSON que vous avez copié avec les informations suivantes :

   - Remplacez `<your storage account name>` par un nom que vous pouvez mémoriser. Les noms de compte doivent être compris entre 3 et 24 caractères, et inclure des lettres minuscules et des chiffres. Pas d’espace.

   - Remplacez `<your storage account key>` par une clé en base64 de 64 octets. Vous pouvez générer une clé avec des outils tels que [GeneratePlus](https://generate.plus/en/base64). Vous utilisez ces informations d’identification pour accéder au stockage d’objets blob à partir d’autres modules.

   - Remplacez `<storage mount>` en fonction du système d’exploitation de votre conteneur. Indiquez le nom d’un [volume](https://docs.docker.com/storage/volumes/) ou le chemin absolu à un répertoire sur votre appareil IoT Edge où vous souhaitez que le module d’objets blob stocke ses données. Le montage associe un emplacement sur votre appareil que vous fournissez à un emplacement défini dans le module.

     - Pour les conteneurs Linux, le format est *\<chemin de stockage ou volume>:/blobroot*. Par exemple :
         - utilisez le [montage de volume](https://docs.docker.com/storage/volumes/) : **my-volume:/blobroot** 
         - utilisez le [montage de liaison](https://docs.docker.com/storage/bind-mounts/) : **/srv/containerdata:/blobroot**. Veillez à suivre les étapes pour [octroyer l’accès à l’annuaire à l’utilisateur du conteneur](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Pour les conteneurs Windows, le format est le suivant : *\<chemin de stockage ou volume>:C:/BlobRoot*. Par exemple :
         - utilisez le [montage de volume](https://docs.docker.com/storage/volumes/) : **my-volume:C:/blobroot**. 
         - utiliser [le montage de liaison](https://docs.docker.com/storage/bind-mounts/) : **C:/ContainerData:C:/BlobRoot**.
         - Au lieu d’utiliser votre lecteur local, vous pouvez mapper votre emplacement réseau SMB. Pour en savoir plus, consultez la section relative à [l’utilisation du partage SMB en tant que stockage local.](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Ne modifiez pas la deuxième moitié de la valeur de montage de stockage, qui pointe vers un emplacement spécifique dans le module. Le montage de stockage doit toujours se terminer par **:/blobroot** pour les conteneurs Linux et **:C:/BlobRoot** pour les conteneurs Windows.

1. Définissez les propriétés [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) et [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) de votre module en copiant le code JSON suivant et en le collant dans la zone **Définir les propriétés souhaitées du jumeau de module**. Configurez chaque propriété avec une valeur appropriée, enregistrez vos choix et poursuivez le déploiement. Si vous utilisez le simulateur IoT Edge, définissez les valeurs sur les variables d’environnement associées pour ces propriétés, que vous trouverez dans la section Explication de [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) et [ deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading":<true,false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload":<true,false>
       }
     }
   }

      ```

   ![définir les options de création de conteneur, propriétés deviceAutoDeleteProperties et deviceToCloudUploadProperties](./media/how-to-deploy-blob/iotedge-custom-module.png)

   Pour plus d’informations sur la configuration des propriétés deviceToCloudUploadProperties et deviceAutoDeleteProperties après le déploiement de votre module, consultez [Modifier le jumeau de module](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Pour plus d’informations sur les propriétés souhaitées, consultez [Définir ou mettre à jour les propriétés souhaitées](module-composition.md#define-or-update-desired-properties).

1. Sélectionnez **Enregistrer**.

1. Sélectionnez **Suivant** pour passer à la section des itinéraires.

#### <a name="specify-routes"></a>Spécifier des routes

Conservez les itinéraires par défaut, puis sélectionnez **Suivant** pour passer à la section de vérification.

#### <a name="review-deployment"></a>Vérifier le déploiement

La section de vérification vous montre le manifeste de déploiement JSON qui a été créé en fonction de vos sélections dans les deux sections précédentes. Il existe également deux modules déclarés que vous n’avez pas ajoutés : **$edgeAgent** et **$edgeHub**. Ces deux modules composent le [runtime IoT Edge](iot-edge-runtime.md) et sont des valeurs par défaut requises dans chaque déploiement.

Passez en revue les informations de votre déploiement, puis sélectionnez **Envoyer**.

### <a name="verify-your-deployment"></a>Vérifier votre déploiement

Une fois que vous envoyez le déploiement, la page **IoT Edge** de votre hub IoT s’affiche à nouveau.

1. Sélectionnez le périphérique IoT Edge que vous avez ciblé avec le déploiement pour accéder à ses informations détaillées.
1. Dans les détails de l’appareil, vérifiez que le module de stockage d’objets blob figure dans **Spécifié dans le déploiement** et **Signalé par l’appareil**.

Il peut s’écouler quelques instants avant que le module ne démarre sur l’appareil et qu’il ne soit renvoyé à IoT Hub. Actualisez la page pour afficher un état mis à jour.

## <a name="deploy-from-visual-studio-code"></a>Déployer à partir de Visual Studio Code

Azure IoT Edge fournit des modèles dans Visual Studio Code pour vous aider à développer des solutions de périphérie. Effectuez les étapes suivantes pour créer une solution IoT Edge avec un module de stockage d’objets blob, et pour configurer le manifeste de déploiement.

1. Sélectionnez **Affichage** > **Palette de commandes**.

1. Dans la palette de commandes, entrez et exécutez la commande **Azure IoT Edge: New IoT Edge solution**.

   ![Exécuter la nouvelle solution IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Suivez les invites de la palette de commandes pour créer votre solution.

   | Champ | Valeur |
   | ----- | ----- |
   | Sélectionner le dossier | Choisissez l’emplacement sur votre machine de développement pour que Visual Studio Code crée les fichiers de la solution. |
   | Provide a solution name (Nommer la solution) | Entrez un nom descriptif pour votre solution ou acceptez le nom par défaut (**EdgeSolution**). |
   | Select module template (Sélectionner un modèle de module) | Choisissez **Module existant (Entrez l’URL de l’image complète)** . |
   | Provide a module name (Nommer le module) | Entrez un nom en minuscules pour votre module, par exemple **azureblobstorageoniotedge**.<br /><br />Il est important d’utiliser un nom en minuscules pour le stockage Blob Azure sur le module IoT Edge. IoT Edge respecte la casse lorsque vous faites référence aux modules, et le SDK de stockage figure par défaut en minuscules. |
   | Indiquer une image Docker pour le module | Fournissez l’URI de l’image : **mcr.microsoft.com/azure-blob-storage:latest** |

   À partir des informations que vous avez fournies, Visual Studio Code crée une solution IoT Edge, puis la charge dans une nouvelle fenêtre. Le modèle de solution crée un modèle de manifeste de déploiement qui inclut votre image du module de stockage d’objets blob, à condition pour cela de configurer les options de création du module.

1. Ouvrez *deployment.template.json* dans l’espace de travail de votre nouvelle solution et recherchez la section **modules**. Modifiez la configuration comme suit :

   1. Supprimez le module **SimulatedTemperatureSensor**, car il est inutile pour ce déploiement.

   1. Copiez et collez le code suivant dans le champ `createOptions` :

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Mettre à jour le paramètre createOptions du module - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Remplacez `<your storage account name>` par un nom que vous pouvez mémoriser. Les noms de compte doivent être compris entre 3 et 24 caractères, et inclure des lettres minuscules et des chiffres. Pas d’espace.

1. Remplacez `<your storage account key>` par une clé en base64 de 64 octets. Vous pouvez générer une clé avec des outils tels que [GeneratePlus](https://generate.plus/en/base64). Vous utilisez ces informations d’identification pour accéder au stockage d’objets blob à partir d’autres modules.

1. Remplacez `<storage mount>` en fonction du système d’exploitation de votre conteneur. Indiquez le nom d’un [volume](https://docs.docker.com/storage/volumes/) ou le chemin absolu à un répertoire sur votre appareil IoT Edge où vous souhaitez que le module d’objets blob stocke ses données. Le montage associe un emplacement sur votre appareil que vous fournissez à un emplacement défini dans le module.  

      
     - Pour les conteneurs Linux, le format est *\<chemin de stockage ou volume>:/blobroot*. Par exemple :
         - utilisez le [montage de volume](https://docs.docker.com/storage/volumes/) : **my-volume:/blobroot** 
         - utilisez le [montage de liaison](https://docs.docker.com/storage/bind-mounts/) : **/srv/containerdata:/blobroot**. Veillez à suivre les étapes pour [octroyer l’accès à l’annuaire à l’utilisateur du conteneur](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Pour les conteneurs Windows, le format est le suivant : *\<chemin de stockage ou volume>:C:/BlobRoot*. Par exemple :
         - utilisez le [montage de volume](https://docs.docker.com/storage/volumes/) : **my-volume:C:/blobroot**. 
         - utiliser [le montage de liaison](https://docs.docker.com/storage/bind-mounts/) : **C:/ContainerData:C:/BlobRoot**.
         - Au lieu d’utiliser votre lecteur local, vous pouvez mapper votre emplacement réseau SMB. Pour en savoir plus, consultez la section relative à [l’utilisation du partage SMB en tant que stockage local.](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Ne modifiez pas la deuxième moitié de la valeur de montage de stockage, qui pointe vers un emplacement spécifique dans le module. Le montage de stockage doit toujours se terminer par **:/blobroot** pour les conteneurs Linux et **:C:/BlobRoot** pour les conteneurs Windows.

1. Configurez les propriétés [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) et [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) de votre module en ajoutant le code JSON suivant au fichier *deployment.template.json*. Configurez chaque propriété avec une valeur appropriée et enregistrez le fichier. Si vous utilisez le simulateur IoT Edge, définissez les valeurs sur les variables d’environnement associées pour ces propriétés, que vous trouverez dans la section Explication de [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) et [ deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![Définir les propriétés souhaitées pour azureblobstorageoniotedge - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Pour plus d’informations sur la configuration des propriétés deviceToCloudUploadProperties et deviceAutoDeleteProperties après le déploiement de votre module, consultez [Modifier le jumeau de module](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Pour plus d’informations sur les options de création de conteneur, la stratégie de redémarrage et l’état souhaité, consultez [Propriétés souhaitées pour EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Enregistrez le fichier *deployment.template.json*.

1. Cliquez avec le bouton droit sur **deployment.template.json** et sélectionnez **Générer le manifeste de déploiement IoT Edge**.

1. Visual Studio Code récupère les informations que vous avez fournies dans le fichier *deployment.template.json* et les utilise pour créer un fichier manifeste de déploiement. Le manifeste de déploiement est créé dans un nouveau dossier **config** dans l’espace de travail de votre solution. Dès que vous disposez de ce fichier, vous pouvez suivre les étapes décrites dans [Déployer des modules Azure IoT Edge à partir de Visual Studio Code](how-to-deploy-modules-vscode.md) ou dans [Déployer des modules Azure IoT Edge avec Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Déployer plusieurs instances de module

Si vous souhaitez déployer plusieurs instances du module de stockage Blob Azure sur IoT Edge, indiquez un chemin d’accès de stockage différent et modifiez la valeur `HostPort` à laquelle le module est lié. Les modules de stockage d’objets blob exposent toujours le port 11002 dans le conteneur, mais vous pouvez déclarer le port auquel il est lié sur l’hôte.

Modifiez les **Options de création de conteneur** (dans le Portail Azure) ou le champ **createOptions** champ (dans le fichier *deployment.template.json* dans Visual Studio Code) pour changer la valeur `HostPort` :

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Lorsque vous vous connectez à des modules de stockage d’objets blob supplémentaires, modifiez le point de terminaison pour qu’il pointe vers le port hôte mis à jour.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur le [stockage Blob Azure sur IoT Edge](how-to-store-data-blob.md)

Pour plus d’informations sur le fonctionnement et la création des manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).
