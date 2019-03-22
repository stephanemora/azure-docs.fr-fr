---
title: Stocker des objets blob de blocs sur des appareils - Azure IoT Edge | Microsoft Docs
description: Déployez un module de stockage Blob Azure sur votre appareil IoT Edge pour stocker des données en périphérie.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 03/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0fc34c913453abd174009213233a54e30b9346d3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57881382"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Stocker des données à la périphérie avec le Stockage Blob Azure sur IoT Edge (préversion)

Le Stockage Blob Azure sur IoT Edge fournit une solution de stockage des [objets blob de blocs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) en périphérie. Un module de stockage d’objets blob sur votre appareil IoT Edge se comporte comme un service d’objets blob de blocs Azure, à ceci près que ces derniers sont stockés localement sur votre appareil IoT Edge. Vous pouvez accéder à vos objets blob avec les mêmes méthodes que celles du kit SDK de stockage Azure, ou aux appels d’API objets blob de blocs auxquels vous êtes déjà habitué. 

Ce module est fourni avec **la hiérarchisation automatique** et **expiration automatique** fonctionnalités.

> [!NOTE]
> Actuellement la hiérarchisation automatique et les fonctionnalités de l’expiration automatique sont uniquement disponibles dans Linux AMD64 et Linux ARM32.

**La hiérarchisation automatique** est une fonctionnalité configurable, ce qui vous permet de télécharger automatiquement les données à partir de votre stockage blob local vers Azure avec prise en charge de connectivité internet intermittentes. Il vous permet de :
- On/off activer la fonctionnalité de hiérarchisation
- Choisissez l’ordre dans lequel les données seront copiés vers Azure comme NewestFirst ou OldestFirst
- Spécifiez le compte de stockage Azure auquel vous souhaitez que vos données sont téléchargées.
- Spécifiez les conteneurs que vous voulez télécharger dans Azure. Ce module vous permet de spécifier des noms de conteneur source et cible.
- Complète la hiérarchisation des objets blob (à l’aide de `Put Blob` opération) et bloquer la hiérarchisation au niveau (à l’aide de `Put Block` et `Put Block List` operations).

Ce module utilise bloc niveau la hiérarchisation, lorsque votre objet blob se compose de blocs. Voici quelques-uns des scénarios courants :
- Votre application met à jour des blocs d’un objet blob précédemment téléchargé, ce module téléchargera uniquement les blocs de mise à jour et pas l’objet blob entier.
- Le module est téléchargement d’objet blob et de connexion internet disparaît, lorsque la connectivité est de retour à nouveau qu'il télécharge uniquement les blocs restants et pas l’objet blob entier.

Si un arrêt inattendu du processus (par exemple, la panne de courant) se produit pendant le chargement d’un objet blob, tous les blocs qui ont été dus pour le téléchargement seront téléchargés à nouveau, lorsque le module repasse en ligne.

**Expiration automatique** est une fonctionnalité configurable dans lequel ce module supprime automatiquement vos objets BLOB à partir du stockage local lorsque la durée de vie (TTL) expire. Elle est mesurée en quelques minutes. Il vous permet de :
- On/off activer la fonctionnalité d’expiration automatique
- Spécifiez la durée de vie en minutes

Les scénarios où les données comme des vidéos, des images, des données de finance, données de l’hôpital ou toutes les données qui doivent être stockées localement, plus loin et qui peut être traitées localement ou transférées vers le cloud sont de bons exemples d’utiliser ce module.

Cet article fournit des instructions pour le déploiement d’un stockage Blob Azure sur le conteneur IoT Edge qui exécute un service d’objets blob sur votre appareil IoT Edge. 

>[!NOTE]
>Le Stockage Blob Azure sur IoT Edge est disponible en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Regardez la vidéo de présentation rapide
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>Conditions préalables

Un appareil Azure IoT Edge :

* Vous pouvez utiliser votre ordinateur de développement ou une machine virtuelle comme un appareil Edge, en suivant les étapes décrites dans le Guide de démarrage rapide pour [Linux](quickstart-linux.md) ou pour les [Appareils Windows](quickstart.md).
* Le module de stockage Blob Azure sur IoT Edge prend en charge les configurations d’appareil suivantes :

   | Système d’exploitation | Architecture |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT Standard (mise à jour d’octobre) | AMD64 |
   | Windows 10 IoT Enterprise (mise à jour d’octobre) | AMD64 |
   | Windows Server 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

Ressources cloud :

* Un niveau standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure. 


## <a name="deploy-blob-storage-to-your-device"></a>Déployer le stockage d’objets blob sur votre appareil

Il existe plusieurs façons de déployer des modules sur un appareil IoT Edge, et toutes fonctionnent pour les modules de stockage Blob Azure sur IoT Edge. Les deux méthodes les plus simples consistent à utiliser les modèles de Visual Studio Code ou du portail Azure. 

### <a name="azure-portal"></a>Portail Azure

Azure Marketplace fournit des modules IoT Edge qui peuvent être déployés directement sur vos appareils IoT Edge, notamment Stockage Blob Azure sur IoT Edge. Suivez ces étapes pour déployer le module à partir du portail Azure.

1. Dans le [portail Azure](https://portal.azure.com), recherchez « stockage Blob Azure sur IoT Edge ». **Sélectionnez** ensuite le résultat de la recherche sur la Place de marché Microsoft Azure.

   ![Créer un module à partir de la recherche Place de marché](./media/how-to-store-data-blob/marketplace-module.png)

2. Choisissez l’appareil IoT Edge qui va recevoir le module. Dans la page **Appareils cibles pour le module IoT Edge**, fournissez les informations suivantes :

   1. Sélectionnez l’**abonnement** qui contient le hub IoT utilisé.

   2. Sélectionnez votre **IoT Hub**.

   3. Si vous connaissez le **Nom d’appareil IoT Edge**, saisissez-le dans la zone de texte. Ou sélectionnez **Rechercher un appareil** et faites votre choix dans la liste d’appareils IoT Edge de votre hub IoT. 
   
   4. Sélectionnez **Créer**.

   Maintenant que vous avez choisi un module IoT Edge à partir de la place de marché Microsoft Azure, ainsi qu’un appareil IoT Edge pour recevoir le module, vous êtes redirigé vers un Assistant en trois étapes qui vous aide à définir exactement la façon dont le module sera déployé.

3. À l’étape **Ajouter des modules** de l’assistant des modules définis, notez que le module **AzureBlobStorageonIoTEdge** est déjà répertorié sous **Modules de déploiement**. 

2. Sélectionnez le module de stockage d’objets blob dans la liste des modules de déploiement afin d’accéder aux détails du module. 

   ![Sélectionner le nom du module pour ouvrir les détails du module](./media/how-to-store-data-blob/open-module-details.png)

3. Dans la page **Modules personnalisés IoT Edge**, mettez à jour le module Stockage d’objets Blob Azure sur IoT Edge comme suit :

   1. Saisissez le **nom** du module en minuscules. Vous pouvez le renommer si vous le souhaitez ou utiliser `azureblobstorageoniotedge`. 

      >[!IMPORTANT]
      >Azure IoT Edge respecte la casse lorsque vous effectuez des appels de modules, et le SDK de stockage figure par défaut en minuscules. Pour éviter que vos connexions au module Stockage d’objets blob Azure sur IoT Edge soient interrompues, donnez-lui un nom en minuscules. 

   2. Les **Options de création de conteneur** par défaut définissent les liaisons de port dont votre conteneur a besoin. Cependant, vous devez également ajouter les informations de votre compte de stockage et une liaison pour le répertoire de stockage sur votre appareil. Remplacez le fichier JSON dans le portail par le code JSON ci-dessous :
    
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
   3. Mettez à jour JSON que vous avez copié avec les informations suivantes : 

      * Remplacez `<your storage account name>` par un nom que vous pouvez mémoriser. Les noms de compte doivent être compris entre trois et vingt-quatre caractères, et inclure des lettres minuscules et des chiffres.
      * Remplacez `<your storage account key>` par une clé en base64 de 64 octets. Vous pouvez générer une clé avec des outils tels que [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Vous utilisez ces informations d’identification pour accéder au stockage d’objets blob à partir d’autres modules.
      * Remplacez `<storage directory bind>` en fonction du système d’exploitation de votre conteneur. Indiquez le nom d’un [volume](https://docs.docker.com/storage/volumes/) ou le chemin absolu à un répertoire sur votre appareil IoT Edge où vous souhaitez que le module d’objets blob stocke ses données. Le répertoire de stockage associe un emplacement sur votre appareil que vous fournissez à un emplacement défini dans le module. 

         * Conteneurs Linux : **\<chemin de stockage>:/blobroot**. Par exemple, /srv/containerdata:/blobroot. Ou, my-volume:/blobroot. 
         * Conteneurs Windows : **\<chemin de stockage>:C:/BlobRoot**. Par exemple, C:/ContainerData:C:/BlobRoot. Ou, my-volume:C:/blobroot.
   
      > [!IMPORTANT]
      > Ne modifiez pas la deuxième moitié de la valeur de liaison du répertoire de stockage, qui pointe vers un emplacement spécifique dans le module. La liaison de répertoire de stockage doit se terminer toujours par **:/blobroot** pour les conteneurs Linux et **:C:/BlobRoot** pour les conteneurs Windows.

      ![Options de création de conteneur de module de mise à jour - portail](./media/how-to-store-data-blob/edit-module.png)

   4. Définissez [la hiérarchisation automatique et expiration automatique](#configure-auto-tiering-and-auto-expiration-via-azure-portal) dans les propriétés souhaitées. Liste de [la hiérarchisation automatique](#auto-tiering-properties) et [expiration automatique](#auto-expiration-properties) propriétés et leurs valeurs possibles. 

   5. Sélectionnez **Enregistrer**. 

4. Sélectionnez **Suivant** pour accéder à l’étape suivante de l’Assistant.
5. À l’étape **Spécifier des itinéraires** de l’assistant, sélectionnez **Suivant**.
6. À l’étape **Passer en revue le déploiement** de l’Assistant, sélectionnez **Envoyer**.
7. Une fois que vous envoyez le déploiement, la page **IoT Edge** de votre hub IoT s’affiche à nouveau. Sélectionnez le périphérique IoT Edge que vous avez ciblé avec le déploiement pour accéder à ses informations détaillées. 
8. Dans les détails de l’appareil, vérifiez que le module de stockage d’objets blob figure dans **Spécifié dans le déploiement** et **Signalé par l’appareil**. Il peut s’écouler quelques instants avant que le module ne démarre sur l’appareil et qu’il ne soit renvoyé à IoT Hub. Actualisez la page pour afficher un état mis à jour. 

### <a name="visual-studio-code-templates"></a>Modèles Visual Studio Code

Azure IoT Edge fournit des modèles dans Visual Studio Code pour vous aider à développer des solutions de périphérie. Ces étapes nécessitent l’installation de [Visual Studio Code](https://code.visualstudio.com/) sur votre machine de développement, et sa configuration au moyen d’[Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Effectuez les étapes suivantes pour créer une solution IoT Edge avec un module de stockage d’objets blob, et configurer le manifeste de déploiement. 

1. Sélectionnez **Affichage** > **Palette de commandes**. 

2. Dans la palette de commandes, entrez et exécutez la commande **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge : Nouvelle solution IoT Edge). Suivez les invites de la palette de commandes pour créer votre solution.

   | Champ | Valeur |
   | ----- | ----- |
   | Sélectionner le dossier | Choisissez l’emplacement sur votre machine de développement pour que VS Code crée les fichiers de la solution. |
   | Provide a solution name (Nommer la solution) | Entrez un nom descriptif pour votre solution ou acceptez le nom par défaut (**EdgeSolution**). |
   | Select module template (Sélectionner un modèle de module) | Choisissez **Module existant (Entrez l’URL de l’image complète)**. |
   | Provide a module name (Nommer le module) | Entrez un nom en minuscules pour votre module, par exemple **azureblobstorage**.<br><br>Il est important d’utiliser un nom en minuscules pour le stockage Blob Azure sur le module IoT Edge. IoT Edge respecte la casse lorsque vous faites référence aux modules, et le SDK de stockage figure par défaut en minuscules. |
   | Indiquer une image Docker pour le module | Fournissez l’URI de l’image : **mcr.microsoft.com/azure-blob-storage:latest** |

   À partir des informations que vous avez fournies, VS Code crée une solution IoT Edge, puis la charge dans une nouvelle fenêtre. Le modèle de solution crée un modèle de manifeste de déploiement qui inclut votre image du module de stockage d’objets blob, à condition pour cela de configurer les options de création du module. 

3. Ouvrez **deployment.template.json** dans l’espace de travail de votre nouvelle solution et recherchez la section **modules**. Modifiez la configuration comme suit :

   1. Supprimez le module **tempSensor**, car il est inutile pour ce déploiement. 

   2. Copiez et collez le code suivant dans le champ **createOptions** de votre module de stockage d’objets blob : 

      ```json
      "Env": [
        "LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME","LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Options de création de module de mise à jour - VS Code](./media/how-to-store-data-blob/create-options.png)

4. Dans le fichier JSON des options de création, mettez à jour `<storage directory bind>` en fonction de votre système d’exploitation conteneur. Indiquez le nom d’un [volume](https://docs.docker.com/storage/volumes/) ou le chemin absolu à un répertoire sur votre appareil IoT Edge où vous souhaitez que le module d’objets blob stocke ses données. Le répertoire de stockage associe un emplacement sur votre appareil que vous fournissez à un emplacement défini dans le module.  

   * Conteneurs Linux : **\<chemin de stockage>:/blobroot**. Par exemple, /srv/containerdata:/blobroot. Ou, my-volume:/blobroot.
   * Conteneurs Windows : **\<chemin de stockage>:C:/BlobRoot**. Par exemple, C:/ContainerData:C:/BlobRoot. Ou, my-volume:C:/blobroot.
   
   > [!IMPORTANT]
   > Ne modifiez pas la deuxième moitié de la valeur de liaison du répertoire de stockage, qui pointe vers un emplacement spécifique dans le module. La liaison de répertoire de stockage doit se terminer toujours par **:/blobroot** pour les conteneurs Linux et **:C:/BlobRoot** pour les conteneurs Windows.

5. Configurer [la hiérarchisation automatique et expiration automatique](#configure-auto-tiering-and-auto-expiration-via-vscode). Liste de [la hiérarchisation automatique](#auto-tiering-properties) et [expiration automatique](#auto-expiration-properties) propriétés

6. Enregistrez le fichier **deployment.template.json**.

7. Ouvrez le fichier **.env** dans l’espace de travail de votre solution. 

8. Le fichier .env est configuré pour recevoir les informations d’identification du registre de conteneurs, mais vous n’en avez pas besoin pour l’image de stockage blob car elle est accessible au public. Au lieu de cela, remplacez le fichier par deux nouvelles variables d’environnement : 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

9. Indiquez une valeur pour `STORAGE_ACCOUNT_NAME`. Les noms de compte doivent être compris entre trois et vingt-quatre caractères, et inclure des lettres minuscules et des chiffres. Fournissez une clé base64 de 64 octets pour `STORAGE_ACCOUNT_KEY`. Vous pouvez générer une clé avec des outils tels que [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Vous utilisez ces informations d’identification pour accéder au stockage d’objets blob à partir d’autres modules. 

   N’insérez pas d’espaces ou de guillemets autour des valeurs que vous fournissez. 

10. Enregistrez le fichier **.env**. 

11. Cliquez avec le bouton droit sur **deployment.template.json** et sélectionnez **Générer le manifeste de déploiement IoT Edge**. 

12. Visual Studio Code récupère les informations que vous avez fournies dans les fichiers deployment.template.json et .env, et les utilise pour créer un fichier manifeste de déploiement. Le manifeste de déploiement est créé dans un nouveau dossier **config** dans l’espace de travail de votre solution. Dès que vous disposez de ce fichier, vous pouvez suivre les étapes décrites dans [Déployer des modules Azure IoT Edge à partir de Visual Studio Code](how-to-deploy-modules-vscode.md) ou dans [Déployer des modules Azure IoT Edge avec Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="auto-tiering-and-auto-expiration-properties-and-configuration"></a>La hiérarchisation automatique et les propriétés d’expiration automatique et la Configuration

Utiliser des propriétés souhaitées pour définir les propriétés de l’expiration automatique et de la hiérarchisation automatique. Ils peuvent être définies pendant le déploiement ou modifiées ultérieurement en modifiant la représentation de module sans avoir à redéployer. Nous recommandons de vérifier la représentation de « Module » pour `reported configuration` et `configurationValidation` s’assurer que les valeurs sont correctement propagées.

### <a name="auto-tiering-properties"></a>Propriétés de la hiérarchisation automatique 
Le nom de ce paramètre est `tieringSettings`

| Champ | Valeurs possibles | Explication |
| ----- | ----- | ---- |
| tieringOn | true, false | Par défaut, il est défini sur `false`, si vous souhaitez l’activer sur affectez-lui la valeur `true`|
| backlogPolicy | NewestFirst, OldestFirst | Vous permet de choisir l’ordre dans lequel les données seront copiés vers Azure. Par défaut, il est défini sur `OldestFirst`. L’ordre est déterminé par l’heure de dernière modification d’objet Blob |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` une chaîne de connexion qui vous permet de spécifier le compte de stockage Azure auquel vous souhaitez que vos données est téléchargée. Spécifiez `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Ajouter EndpointSuffix approprié de Azure où les données sont chargées, elle varie selon globale d’Azure Government Azure et Microsoft Azure Stack. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}` <br><br> `"<source container name1>": {"target": "%d-%c"}` | Vous permet de spécifier les noms de conteneur que vous voulez télécharger dans Azure. Ce module vous permet de spécifier des noms de conteneur source et cible. Si vous ne spécifiez pas le nom du conteneur cible, il affecte automatiquement le nom du conteneur en tant que `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`. Vous pouvez créer des chaînes de modèle pour le nom du conteneur cible, consultez la colonne de valeurs possibles. <br>* %h -> nom du Hub IoT (3 et 50 caractères). <br>* %d -> IoT device Id (1 à 129 caractères). <br>* %m -> nom du module (1 à 64 caractères). <br>* %c -> nom du conteneur source (3 à 63 caractères). <br><br>Taille maximale du nom du conteneur est 63 caractères, tout en attribuer automatiquement le nom du conteneur cible si la taille du conteneur dépasse 63 caractères, qu'il va réduire chaque section (IoTHubName, IotEdgeDeviceName, ModuleName, ContainerName) et 15 caractères. |

### <a name="auto-expiration-properties"></a>Propriétés d’expiration automatique
Le nom de ce paramètre est `ttlSettings`

| Champ | Valeurs possibles | Explication |
| ----- | ----- | ---- |
| ttlOn | true, false | Par défaut, il est défini sur `false`, si vous souhaitez l’activer sur affectez-lui la valeur `true`|
| timeToLiveInMinutes | `<minutes>` | Spécifiez la durée de vie en minutes. Le module supprime automatiquement vos objets BLOB à partir du stockage local lors de la durée de vie expire |

### <a name="configure-auto-tiering-and-auto-expiration-via-azure-portal"></a>Configurer la hiérarchisation automatique et expiration automatique via le portail Azure

Définir les propriétés souhaitées pour activer la hiérarchisation automatique et expiration automatique, vous pouvez définir ces valeurs :

- **Lors du déploiement initial**: Copiez le JSON dans **propriétés souhaitées des représentations de module de jeu** boîte. Configurer chaque propriété avec la valeur appropriée, enregistrez-le et poursuivre le déploiement.

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

  ![définir les propriétés de la hiérarchisation automatique et d’expiration automatique](./media/how-to-store-data-blob/iotedge_custom_module.png)

- **Une fois que le module est déployé via la fonctionnalité de « Jumeau de Module identité »**: Accédez à « Jumeau de Module identité » de ce module, copiez le JSON dans les propriétés que vous le souhaitez, configurez chaque propriété avec la valeur appropriée, enregistrer. Au format Json « Jumeau de Module identité », assurez-vous que chaque fois que vous ajoutez ou mettez à jour souhaitée propriété, le `reported configuration` section reflète les modifications et le `configurationValidation` section signale une réussite pour chaque propriété.

   ```json 
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

   ```

![tiering+ttl module_identity_twin](./media/how-to-store-data-blob/module_identity_twin.png) 

### <a name="configure-auto-tiering-and-auto-expiration-via-vscode"></a>Configurer la hiérarchisation automatique et expiration automatique par le biais de VSCode

- **Lors du déploiement initial**: Ajouter le format JSON dans votre deployment.template.json pour définir les propriétés souhaitées pour ce module ci-dessous. Configurer chaque propriété avec la valeur appropriée et enregistrez-le.

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

Voici un exemple des propriétés souhaitées pour ce module : ![, définissez les propriétés souhaitées pour azureblobstorageoniotedge - VS Code](./media/how-to-store-data-blob/tiering_ttl.png)

- **Une fois que le module est déployé via « Jumeau de Module »**: [Modifier la représentation de Module](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin) de ce module, copiez le JSON dans les propriétés que vous le souhaitez, configurez chaque propriété avec la valeur appropriée et enregistrer. Au format Json « Jumeau de Module » Assurez-vous que chaque fois que vous ajoutez ou mettez à jour souhaitée de propriété, le `reported configuration` section reflète les modifications et le `configurationValidation` section signale une réussite pour chaque propriété.

   ```json 
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

   ```
  ## <a name="logs"></a>Journaux

Suivez les instructions à [configurer vos journaux de docker pour les modules IoT Edge](production-checklist.md#set-up-logs-and-diagnostics)

## <a name="connect-to-your-blob-storage-module"></a>Se connecter au module de stockage d’objets blob

Vous pouvez utiliser le nom du compte et la clé de compte que vous avez configurés pour que votre module accède au stockage d’objets blob sur votre appareil IoT Edge. 

Spécifiez votre appareil IoT Edge en tant que point de terminaison d’objets blob pour toutes les demandes de stockage que vous lui adressez. Vous pouvez [Créer une chaîne de connexion pour un point de terminaison de stockage explicite](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) en utilisant les informations de l’appareil IoT Edge et le nom du compte que vous avez configuré. 

1. Pour les modules, qui sont déployés sur le même appareil edge, où « Azure Blob Storage sur IoT Edge » est en cours d’exécution, le point de terminaison d’objet blob est : `http://<module name>:11002/<account name>`. 
2. Pour les modules, qui sont déployés sur un appareil edge différent, que l’appareil de périphérie, où « Azure Blob Storage sur IoT Edge » est en cours d’exécution, selon votre configuration de point de terminaison blob est : `http://<device IP >:11002/<account name>` ou `http://<IoT Edge device hostname>:11002/<account name>` ou `http://<FQDN>:11002/<account name>`

## <a name="deploy-multiple-instances"></a>Déployer plusieurs instances

Si vous souhaitez déployer plusieurs instances de stockage d’objets Blob Azure IoT Edge, vous devez fournir le chemin d’accès de stockage différent et modifier HostPort auquel le module est lié. Les modules de stockage d’objets blob exposent toujours le port 11002 dans le conteneur, mais vous pouvez déclarer le port auquel il est lié sur l’hôte. 

Modifiez les options de création du module pour changer la valeur de HostPort :

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

Lorsque vous vous connectez à des modules de stockage d’objets blob supplémentaires, modifiez le point de terminaison pour qu’il pointe vers le port hôte mis à jour. 

## <a name="try-it-out"></a>Faites un essai

### <a name="azure-blob-storage-quickstart-samples"></a>Exemples de démarrage rapide du stockage Blob Azure
La documentation du stockage Blob Azure comprend des guides de démarrage rapide qui fournissent des exemples de code dans différents langages. Vous pouvez exécuter ces exemples pour tester le stockage Blob Azure sur IoT Edge en changeant le point de terminaison des objets blob pour qu’il pointe vers votre module de stockage d’objets blob. Suivez les étapes pour [se connecter à votre module de stockage d’objets blob](#connect-to-your-blob-storage-module)

Les guides de démarrage rapide suivants utilisent des langages qui sont également pris en charge par IoT Edge, vous pouvez donc les déployer en tant que modules IoT Edge en même temps que le module de stockage d’objets blob :

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.JS](../storage/blobs/storage-quickstart-blobs-nodejs.md) 

### <a name="azure-storage-explorer"></a>Explorateur de stockage Azure
Vous pouvez également essayer [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) pour vous connecter à votre compte de stockage local. Nous avons essayé avec [précédente version 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0) de l’Explorateur Azure.
> [!NOTE]
> Vous pouvez rencontrer des erreurs lors de l’exécution des étapes ci-dessous, ignorer et actualiser. 

1. Téléchargez et installez l’Explorateur de stockage Azure
2. Se connecter au stockage Azure à l’aide d’une chaîne de connexion
3. Fournir la chaîne de connexion : `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`
4. Parcourez les étapes pour vous connecter.
5. Créer le conteneur à l’intérieur de votre compte de stockage local
6. Démarrer le téléchargement de fichiers en tant qu’objets BLOB de blocs.
   > [!NOTE]
   > Décochez la case à cocher pour télécharger sous la forme d’objets BLOB de pages. Ce module ne prend pas en charge les objets BLOB de pages. Vous obtiendrez cette invite lors du téléchargement de fichiers tels que le fichier .iso, .vhd, .vhdx ou tous les fichiers volumineux.

7. Vous pouvez choisir de se connecter à vos comptes de stockage Azure où vous chargez les données. Il vous donne une vue unique pour votre compte de stockage local et le compte de stockage Azure

## <a name="supported-storage-operations"></a>Opérations de stockage prises en charge

Modules de stockage d’objets BLOB sur IoT Edge utilisent les mêmes SDK de stockage Azure et sont cohérentes avec la version 2017-04-17 de l’API de stockage Azure pour les points de terminaison blob de bloc. Les versions ultérieures dépendent des besoins des clients.

Les opérations de stockage Blob Azure ne sont pas toutes prises en charge par le stockage Blob Azure sur IoT Edge. La section suivante répertorie les opérations prises en charge et non pris en charge.

### <a name="account"></a>Compte

Prises en charge : 
* Répertorier les conteneurs

Non prises en charge : 
* Obtenir et définir les propriétés du service BLOB
* Demande d’objet blob préliminaire
* Obtenir les statistiques du service BLOB
* Obtenir les informations de compte

### <a name="containers"></a>Containers

Prises en charge : 
* Créer et supprimer un conteneur
* Obtenir les métadonnées et les propriétés de conteneur
* Liste des objets blob
* Obtenir et définir une ACL de conteneur
* Définir les métadonnées de conteneur

Non prises en charge :
* Louer à bail un conteneur

### <a name="blobs"></a>Objets blob

Prises en charge : 
* Placer, obtenir et supprimer un objet blob
* Obtenir et définir les propriétés d’un objet blob
* Obtenir et définir les métadonnées d’un objet blob

Non prises en charge : 
* Louer à bail un objet blob
* Faire une capture instantanée d’un objet blob
* Copier et abandonner la copie d’un objet blob
* Annuler la suppression d’un objet blob
* Définir un niveau d’objet blob

### <a name="block-blobs"></a>Objets blob de blocs

Prises en charge : 
* Placer un bloc
* Placer et obtenir une liste de blocs

Non prises en charge :
* Placer un bloc à partir d’une URL

## <a name="feedback"></a>Commentaires :
Vos commentaires sont très importants pour nous, pour que ce module et ses fonctionnalités utile et facile à utiliser. Partagez vos commentaires et dites-nous comment nous pouvons améliorer.

Vous pouvez nous contacter au absiotfeedback@microsoft.com 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [Stockage Blob Azure](../storage/blobs/storage-blobs-introduction.md)

