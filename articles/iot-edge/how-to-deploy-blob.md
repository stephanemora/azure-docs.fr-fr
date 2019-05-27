---
title: Déployer le module de stockage d’objets Blob Azure sur des appareils - Azure IoT Edge | Microsoft Docs
description: Déployez un module de stockage Blob Azure sur votre appareil IoT Edge pour stocker des données en périphérie.
author: kgremban
ms.author: kgremban
ms.date: 05/21/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: philmea
ms.openlocfilehash: d844e81de9cfb556e91ab5c0d5a8074c822cce0a
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990465"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Déployer le stockage d’objets Blob Azure sur le module IoT Edge sur votre appareil

Il existe plusieurs façons de déployer des modules sur un appareil IoT Edge et tous les utiliser pour le stockage Blob Azure sur les modules IoT Edge. Les deux méthodes les plus simples consistent à utiliser les modèles de Visual Studio Code ou du portail Azure.

## <a name="prerequisites"></a>Conditions préalables

- Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) dans votre abonnement Azure.
- Un [appareil IoT Edge](how-to-register-device-portal.md) avec le runtime IoT Edge installé.
- [Visual Studio Code](https://code.visualstudio.com/) et [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) si vous déployez à partir de Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Déployer à partir du portail Azure

Le portail Azure vous guide à travers la création d’un manifeste de déploiement et à l’envoi du déploiement sur un appareil IoT Edge.

### <a name="select-your-device"></a>Sélectionner votre appareil

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.
1. Sélectionnez **IoT Edge** dans le menu.
1. Cliquez sur l’ID de l’appareil cible dans la liste des appareils.
1. Sélectionnez **Définir modules**.

### <a name="configure-a-deployment-manifest"></a>Configurer un manifeste de déploiement

Un manifeste de déploiement est un document JSON qui décrit les modules à déployer, le flux des données entre les modules et les propriétés souhaitées des jumeaux de module. Le portail Azure possède un Assistant qui vous guide à travers la création d’un manifeste de déploiement, au lieu de créer le document JSON manuellement. Il comporte trois étapes : **Ajouter des modules**, **Spécifier des routes** et **Vérifier le déploiement**.

#### <a name="add-modules"></a>Ajouter des modules

1. Dans la section **Modules de déploiement** de la page, sélectionnez **Ajouter**.

1. Dans les types de modules dans la liste déroulante, sélectionnez **Module IoT Edge**.

1. Fournissez un nom pour le module, puis spécifiez l’image de conteneur :

   - **Nom** -azureblobstorageoniotedge
   - **URI de l’image** -mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > Azure IoT Edge respecte la casse lorsque vous appelez des modules et le SDK de stockage par défaut est également en minuscules. Bien que le nom du module dans le [place de marché Azure](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) est **AzureBlobStorageonIoTEdge**, la modification du nom en minuscules permet de s’assurer que vos connexions vers le stockage d’objets Blob Azure sur le module IoT Edge ne sont pas interrompues.

1. La valeur par défaut **Options de création de conteneur** valeurs définissent les liaisons de port qui a besoin de votre conteneur, mais vous devez également ajouter des informations de votre compte de stockage et une liaison pour le répertoire de stockage sur votre appareil. Remplacez la valeur par défaut JSON dans le portail avec le code JSON ci-dessous :

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage directory bind>"
       ],
     "PortBindings":{
       "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Mettez à jour JSON que vous avez copié avec les informations suivantes :

   - Remplacez `<your storage account name>` par un nom que vous pouvez mémoriser. Les noms de compte doivent être 3 et 24 caractères, avec des lettres minuscules et chiffres. Aucun espace.

   - Remplacez `<your storage account key>` par une clé en base64 de 64 octets. Vous pouvez générer une clé avec des outils tels que [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Vous utilisez ces informations d’identification pour accéder au stockage d’objets blob à partir d’autres modules.

   - Remplacez `<storage directory bind>` indiquée par votre système d’exploitation de conteneur. Indiquez le nom d’un [volume](https://docs.docker.com/storage/volumes/) ou le chemin absolu à un répertoire sur votre appareil IoT Edge où vous souhaitez que le module d’objets blob stocke ses données. Le répertoire de stockage associe un emplacement sur votre appareil que vous fournissez à un emplacement défini dans le module.

     - Pour des conteneurs Linux, le format est  *\<chemin d’accès de stockage > : / blobroot*. Par exemple, **/srv/containerdata : / blobroot** ou **Mon volume : / blobroot**.
     - Pour les conteneurs Windows, le format est  *\<chemin d’accès de stockage > : C : / BlobRoot*. Par exemple, **C: / ContainerData:C : / BlobRoot** ou **Mon-volume : C : / blobroot**.

     > [!IMPORTANT]
     > Ne modifiez pas la deuxième moitié de la valeur de liaison du répertoire de stockage, qui pointe vers un emplacement spécifique dans le module. La liaison de répertoire de stockage doit se terminer toujours par **:/blobroot** pour les conteneurs Linux et **:C:/BlobRoot** pour les conteneurs Windows.

    ![Options de création de conteneur de module de mise à jour - portail](./media/how-to-store-data-blob/edit-module.png)

1. Définissez [la hiérarchisation](how-to-store-data-blob.md#tiering-properties) et [time-to-live](how-to-store-data-blob.md#time-to-live-properties) propriétés pour votre module en copiant le code JSON suivant et en collant dans la **propriétés souhaitées des représentations de module de jeu** boîte. Configurer chaque propriété avec une valeur appropriée, enregistrez-le et poursuivre le déploiement.

   ```json
   {
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>,
         "timeToLiveInMinutes": <timeToLiveInMinutes>
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

      ```

   ![définir les propriétés de la hiérarchisation et time-to-live](./media/how-to-store-data-blob/iotedge_custom_module.png)

   Pour plus d’informations sur la configuration de la hiérarchisation et la durée de vie après avoir déployé votre module, consultez [modifier la représentation de Module](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Pour plus d’informations sur les propriétés souhaitées, consultez [mise à jour ou définir les propriétés souhaitées](module-composition.md#define-or-update-desired-properties).

1. Sélectionnez **Enregistrer**.

1. Sélectionnez **Suivant** pour passer à la section des itinéraires.

#### <a name="specify-routes"></a>Spécifier des routes

Conserver les itinéraires par défaut, puis sélectionnez **suivant** à passer à la section de révision.

#### <a name="review-deployment"></a>Vérifier le déploiement

La section de vérification vous montre le manifeste de déploiement JSON qui a été créé en fonction de vos sélections dans les deux sections précédentes. Il existe également deux modules déclaré que vous n’avez pas ajouté : **$edgeAgent** et **$edgeHub**. Ces deux modules composent le [runtime IoT Edge](iot-edge-runtime.md) et sont des valeurs par défaut requises dans chaque déploiement.

Passez en revue les informations de votre déploiement, puis sélectionnez **Envoyer**.

### <a name="verify-your-deployment"></a>Vérification de votre déploiement

Une fois que vous envoyez le déploiement, la page **IoT Edge** de votre hub IoT s’affiche à nouveau.

1. Sélectionnez le périphérique IoT Edge que vous avez ciblé avec le déploiement pour accéder à ses informations détaillées.
1. Dans les détails de l’appareil, vérifiez que le module de stockage d’objets blob figure dans **Spécifié dans le déploiement** et **Signalé par l’appareil**.

Il peut s’écouler quelques instants avant que le module ne démarre sur l’appareil et qu’il ne soit renvoyé à IoT Hub. Actualisez la page pour afficher un état mis à jour.

## <a name="deploy-from-visual-studio-code"></a>Déployer à partir de Visual Studio Code

Azure IoT Edge fournit des modèles dans Visual Studio Code pour vous aider à développer des solutions de périphérie. Procédez comme suit pour créer une nouvelle solution IoT Edge avec un module de stockage d’objets blob et à configurer le manifeste de déploiement.

1. Sélectionnez **Affichage** > **Palette de commandes**.

1. Dans la palette de commandes, entrez et exécutez la commande **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge : Nouvelle solution IoT Edge).

   ![Exécuter la nouvelle solution IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Suivez les invites de la palette de commandes pour créer votre solution.

   | Champ | Valeur |
   | ----- | ----- |
   | Sélectionner le dossier | Choisissez l’emplacement sur votre ordinateur de développement pour Visual Studio Code créer les fichiers de solution. |
   | Provide a solution name (Nommer la solution) | Entrez un nom descriptif pour votre solution ou acceptez le nom par défaut (**EdgeSolution**). |
   | Select module template (Sélectionner un modèle de module) | Choisissez **Module existant (Entrez l’URL de l’image complète)**. |
   | Provide a module name (Nommer le module) | Entrez un nom en minuscules pour votre module, par exemple **azureblobstorage**.<br /><br />Il est important d’utiliser un nom en minuscules pour le stockage Blob Azure sur le module IoT Edge. IoT Edge respecte la casse lorsque vous faites référence aux modules, et le SDK de stockage figure par défaut en minuscules. |
   | Indiquer une image Docker pour le module | Fournissez l’URI de l’image : **mcr.microsoft.com/azure-blob-storage:latest** |

   À partir des informations que vous avez fournies, Visual Studio Code crée une solution IoT Edge, puis la charge dans une nouvelle fenêtre. Le modèle de solution crée un modèle de manifeste de déploiement qui inclut votre image du module de stockage d’objets blob, à condition pour cela de configurer les options de création du module.

1. Ouvrez *deployment.template.json* dans l’espace de travail de votre nouvelle solution et recherchez la section **modules**. Modifiez la configuration comme suit :

   1. Supprimez le module **tempSensor**, car il est inutile pour ce déploiement.

   1. Copiez et collez le code suivant dans le `createOptions` champ :

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Mettre à jour du module createOptions - Visual Studio Code](./media/how-to-store-data-blob/create-options.png)

1. Remplacez `<your storage account name>` par un nom que vous pouvez mémoriser. Les noms de compte doivent être 3 et 24 caractères, avec des lettres minuscules et chiffres. Aucun espace.

1. Remplacez `<your storage account key>` par une clé en base64 de 64 octets. Vous pouvez générer une clé avec des outils tels que [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Vous utilisez ces informations d’identification pour accéder au stockage d’objets blob à partir d’autres modules.

1. Remplacez `<storage directory bind>` indiquée par votre système d’exploitation de conteneur. Indiquez le nom d’un [volume](https://docs.docker.com/storage/volumes/) ou le chemin absolu à un répertoire sur votre appareil IoT Edge où vous souhaitez que le module d’objets blob stocke ses données. Le répertoire de stockage associe un emplacement sur votre appareil que vous fournissez à un emplacement défini dans le module.  

      - Pour des conteneurs Linux, le format est  *\<chemin d’accès de stockage > : / blobroot*. Par exemple, **/srv/containerdata : / blobroot** ou **Mon volume : / blobroot**.
      - Pour les conteneurs Windows, le format est  *\<chemin d’accès de stockage > : C : / BlobRoot*. Par exemple, **C: / ContainerData:C : / BlobRoot** ou **Mon-volume : C : / blobroot**.

      > [!IMPORTANT]
      > Ne modifiez pas la deuxième moitié de la valeur de liaison du répertoire de stockage, qui pointe vers un emplacement spécifique dans le module. La liaison de répertoire de stockage doit se terminer toujours par **:/blobroot** pour les conteneurs Linux et **:C:/BlobRoot** pour les conteneurs Windows.

1. Configurer [la hiérarchisation](how-to-store-data-blob.md#tiering-properties) et [time-to-live](how-to-store-data-blob.md#time-to-live-properties) propriétés pour votre module en ajoutant le code JSON suivant à la *deployment.template.json* fichier. Configurez chaque propriété avec une valeur appropriée et enregistrez le fichier.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>,
         "timeToLiveInMinutes": <timeToLiveInMinutes>
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }
   ```

   ![définir les propriétés souhaitées pour azureblobstorageoniotedge - Visual Studio Code](./media/how-to-store-data-blob/tiering_ttl.png)

   Pour plus d’informations sur la configuration de la hiérarchisation et la durée de vie après avoir déployé votre module, consultez [modifier la représentation de Module](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Pour plus d’informations sur le conteneur créer options, redémarrez la stratégie et état souhaité, consultez [EdgeAgent les propriétés souhaitées](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Enregistrez le fichier *deployment.template.json*.

1. Cliquez avec le bouton droit sur **deployment.template.json** et sélectionnez **Générer le manifeste de déploiement IoT Edge**.

1. Visual Studio Code prend les informations que vous avez fourni dans *deployment.template.json* et l’utilise pour créer un nouveau fichier de manifeste de déploiement. Le manifeste de déploiement est créé dans un nouveau dossier **config** dans l’espace de travail de votre solution. Dès que vous disposez de ce fichier, vous pouvez suivre les étapes décrites dans [Déployer des modules Azure IoT Edge à partir de Visual Studio Code](how-to-deploy-modules-vscode.md) ou dans [Déployer des modules Azure IoT Edge avec Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Déployer plusieurs instances de module

Si vous souhaitez déployer plusieurs instances du stockage Blob Azure sur le module IoT Edge, vous devez fournir un chemin d’accès de stockage différent et modifier le `HostPort` valeur auquel le module est lié. Les modules de stockage d’objets blob exposent toujours le port 11002 dans le conteneur, mais vous pouvez déclarer le port auquel il est lié sur l’hôte.

Modifier **Options de création de conteneur** (dans le portail Azure) ou le **createOptions** champ (dans le *deployment.template.json* fichier dans Visual Studio Code) pour modifier le `HostPort` valeur :

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Lorsque vous vous connectez à des modules de stockage d’objets blob supplémentaires, modifiez le point de terminaison pour qu’il pointe vers le port hôte mis à jour.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le fonctionnement et la création des manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).
