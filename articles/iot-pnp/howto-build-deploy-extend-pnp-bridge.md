---
title: Comment créer et déployer le pont IoT Plug-and-Play | Microsoft Docs
description: Identifiez les composants du pont IoT Plug-and-Play. Découvrez comment l’exécuter sur des appareils IoT, sur des passerelles et en tant que module IoT Edge.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c89427f83600d3b8091d5293b1757fa6f1a15ef1
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202482"
---
# <a name="build-and-deploy-the-iot-plug-and-play-bridge"></a>Créer et déployer le pont IoT Plug-and-Play

Le [pont IoT Plug-and-Play](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) vous permet de connecter les appareils existants attachés à une passerelle à votre hub IoT. Ce pont peut être utilisé pour mapper les interfaces IoT Plug-and-Play aux appareils attachés. Une interface IoT Plug-and-Play définit la télémétrie envoyée par un appareil, les propriétés synchronisées entre l’appareil et le cloud, ainsi que les commandes auxquelles l’appareil répond. Vous pouvez installer et configurer l’application de pont open source sur des passerelles Windows ou Linux. En outre, le pont peut être exécuté en tant que module du runtime Azure IoT Edge.

Cet article explique comment dans le détail :

- Configurez un pont.
- Comment générer et exécuter le pont dans différents environnements.

Pour obtenir un exemple simple d’utilisation du pont, consultez [Comment connecter l’exemple de pont Plug-and-Play qui s’exécute sur Linux ou Windows à un hub IoT](howto-use-iot-pnp-bridge.md).

L’aide et les exemples de cet article supposent que vous maîtrisez les bases de [Azure Digital Twins ](../digital-twins/overview.md) et [IoT Plug-and-Play](overview-iot-plug-and-play.md).

## <a name="general-prerequisites"></a>Conditions préalables

### <a name="supported-os-platforms"></a>Plateformes de système d’exploitation prises en charge

Les plateformes et versions de système d’exploitation suivantes sont prises en charge :

|Plateforme  |Versions prises en charge  |
|---------|---------|
|Windows 10 |     Toutes les références SKU Windows sont prises en charge. Par exemple : IoT Enterprise, Server, Desktop, IoT Core. *Pour la fonctionnalité d’analyse du fonctionnement de la caméra, 20H1 ou une version ultérieure est recommandé. Toutes les autres fonctionnalités sont disponibles sur toutes les versions de Windows 10.*  |
|Linux     |Testé et pris en charge sur Ubuntu 18.04 ; le fonctionnement des autres distributions n’a pas été testé.         |
||

### <a name="hardware"></a>Matériel

- Toute plateforme matérielle prenant en charge les références SKU et versions de système d’exploitation ci-dessus.
- Les capteurs et périphériques série, USB, Bluetooth et de caméra sont pris en charge en mode natif. Le pont IoT Plug-and-Play peut être étendu pour prendre en charge n’importe quel périphérique ou capteur personnalisé.

### <a name="azure-iot-products-and-tools"></a>Outils et produits Azure IoT

- **Azure IoT Hub** : vous avez besoin d’un [hub Azure IoT](../iot-hub/index.yml) dans votre abonnement Azure pour connecter votre appareil. Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. Si vous n’avez pas de hub IoT, [suivez ces instructions pour en créer un](../iot-hub/iot-hub-create-using-cli.md). La prise en charge d’IoT Plug-and-Play n’est pas incluse dans les hubs IoT de niveau De base.
- **Explorateur Azure IoT** : pour interagir avec votre appareil IoT Plug-and-Play, vous pouvez utiliser l’outil Explorateur Azure IoT. [Téléchargez et installez la dernière version de l’Explorateur Azure IoT](./howto-use-iot-explorer.md) pour votre système d’exploitation. Configurez l’outil Explorateur Azure IoT pour vous connecter à votre hub IoT et recherchez les définitions de modèle dans le dossier *pnpbridge\docs\schemas* dans le pont **iot-plug-and-play-bridge** que vous avez cloné.

## <a name="configure-a-bridge"></a>Configurer un pont

Il existe deux manières de configurer le pont :

- Si le pont s’exécute en mode natif sur un appareil IoT ou une passerelle, utilisez le fichier de configuration. Ce scénario peut être suivi sur un ordinateur Linux ou Windows.
- Si le pont s’exécute en tant que module IoT Edge dans le runtime IoT Edge, utilisez la propriété de jumeau de module de votre choix. Ce scénario suppose que le runtime IoT Edge est hébergé dans un environnement Linux.

### <a name="configuration-file"></a>Fichier de configuration

Lorsque le pont Plug-and-Play s’exécute en mode natif sur un appareil IoT ou une passerelle, il utilise un fichier de configuration pour :

- obtenir les informations de connexion IoT Central ou IoT Hub ;
- configurer les appareils pour lesquels les interfaces IoT Plug-and-Play sont publiées.

Utilisez l’une des options suivantes pour fournir le fichier de configuration au pont :

- Transmettez le chemin d’accès au fichier de configuration en tant que paramètre de ligne de commande à l’exécutable du pont.
- Utilisez le fichier *config.json* existant dans le dossier *pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console*.

Le [schéma du fichier de configuration](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json) est disponible dans le référentiel GitHub.

> [!TIP]
> Si vous modifiez un fichier de configuration de pont dans VS Code, vous pouvez utiliser ce fichier de schéma pour valider votre fichier.

### <a name="iot-edge-module-configuration"></a>Configuration du module IoT Edge

Lorsque le pont s’exécute en tant que module IoT Edge sur un runtime IoT Edge, le fichier de configuration est envoyé à partir du cloud en tant que mise à jour de la propriété souhaitée de `PnpBridgeConfig`. Le pont attend la mise à jour de cette propriété avant de configurer les adaptateurs et les composants.

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>Créer et exécuter le pont sur un appareil ou une passerelle IoT

| Plateforme | Prise en charge |
| :-----------: | :-----------: |
| Windows |  Oui |
| Linux | Oui |

### <a name="prerequisites"></a>Prérequis

Pour terminer cette section, vous devez installer les logiciels suivants sur votre ordinateur local :

- Un environnement de développement prenant en charge la compilation C++, comme [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) : veillez à inclure le composant Gestionnaire de package NuGet et la charge de travail **Développement Desktop en C++** quand vous installez Visual Studio.
- [CMake](https://cmake.org/download/) : quand vous installez CMake, sélectionnez l’option **Ajouter CMake au chemin d'accès système**.
- Si vous créez sur Windows, vous devrez également télécharger le [kit de développement logiciel (SDK) Windows 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk).

### <a name="source-code"></a>Code source

Clonez le référentiel [IoT Plug and Play bridge](https://github.com/Azure/iot-plug-and-play-bridge) sur votre ordinateur local :

```console
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

L’exécution de la commande précédente prendra sûrement plusieurs minutes.

> [!NOTE]
> Si vous rencontrez des problèmes d’échec de `git submodule update` sur Windows, essayez la commande suivante pour résoudre le problème : `git config --system core.longpaths true`.

### <a name="build-the-bridge-on-windows"></a>Créer le pont sur Windows

Ouvrez l’**invite de commandes développeur pour Visual Studio 2019** et accédez au dossier qui contient le référentiel que vous avez cloné, puis exécutez les commandes suivantes :

```console
cd pnpbridge\scripts\windows

build.cmd
```

L’exécution de la commande précédente prendra sûrement plusieurs minutes.

Si vous le souhaitez, vous pouvez ouvrir le fichier solution *pnpbridge\cmake\pnpbridge_x86\azure_iot_pnp_bridge.sln* généré dans Visual Studio pour modifier, régénérer et déboguer le code.

> [!TIP]
> Ce projet utilise CMAKE pour générer les fichiers projet. Toutes les modifications que vous apportez au projet dans Visual Studio risquent d’être perdues si les fichiers CMAKE appropriés ne sont pas mis à jour.

### <a name="build-the-bridge-on-linux"></a>Créer le pont sur Linux

À l’aide de l’interpréteur de commandes bash, accédez au dossier qui contient le référentiel que vous avez cloné, puis exécutez les commandes suivantes :

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>Configurer les capteurs génériques

Modifiez les paramètres suivants sous le nœud `pnp_bridge_connection_parameters` dans le fichier *config.json* situé dans le dossier *iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console* dans Windows ou dans le dossier *iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_linux\src/pnpbridge/samples/console* dans Windows :

Si vous utilisez une chaîne de connexion pour vous connecter à votre hub IoT :

```JSON
{
  "connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "root_interface_model_id": "[To fill in]",
    "auth_parameters": {
      "auth_type": "symmetric_key",
      "symmetric_key": "[To fill in]"
    }
  }
}
```

> [!TIP]
> La valeur `symmetric_key` doit correspondre à la clé SAS dans la chaîne de connexion.

Si vous utilisez DPS pour vous connecter à votre hub IoT ou à votre application IoT Central :

```JSON
{
  "connection_parameters": {
    "connection_type" : "dps",
    "root_interface_model_id": "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "auth_parameters" : {
      "auth_type" : "symmetric_key",
      "symmetric_key" : "[DEVICE KEY]"
    },
    "dps_parameters" : {
      "global_prov_uri" : "[GLOBAL PROVISIONING URI] - typically it is global.azure-devices-provisioning.net",
      "id_scope": "[IoT Central / IoT Hub ID SCOPE]",
      "device_id": "[DEVICE ID]"
    }
  }
}
```

Examinez le reste du fichier config pour connaître les composants d’interface et les paramètres globaux qui sont configurés dans cet exemple.

### <a name="start-the-bridge-in-windows"></a>Démarrer le pont dans Windows

Démarrez le pont en l’exécutant à l’invite de commandes :

```console
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> Pour utiliser un fichier config à partir d’un autre emplacement, transmettez son chemin d’accès en tant que paramètre de ligne de commande lorsque vous exécutez le pont.
  
> [!TIP]
> Si vous disposez d’une caméra intégrée ou d’une caméra USB connectée à votre ordinateur, vous pouvez démarrer une application qui utilise la caméra, telle que l’application **Caméra** intégrée. Lorsque l’application **Caméra** est en cours d’exécution, la sortie de la console de pont affiche les statistiques de surveillance et la fréquence d’images est indiquée à votre hub IoT via l’interface de jumeau numérique.

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>Générer et exécuter le pont en tant que module IoT Edge

| Plateforme | Prise en charge |
| :-----------: | :-----------: |
| Windows |  Non |
| Linux | Oui |

### <a name="prerequisites"></a>Prérequis

Pour terminer cette section, vous avez besoin d’un hub Azure IoT gratuit ou de niveau standard. Pour savoir comment créer un hub IoT, consultez [Créer un hub IoT](../iot-hub/iot-hub-create-through-portal.md).

Les étapes de cette section supposent que vous disposez de l’environnement de développement suivant sur un ordinateur Windows 10. Ces outils vous permettent de créer et de déployer un module IoT Edge sur votre appareil IoT Edge :

- Sous-système Windows pour Linux (WSL) 2 exécutant Ubuntu 18.04 LTS. Pour en savoir plus, consultez le [guide d’installation du sous-système Windows pour Linux pour Windows 10](/windows/wsl/install-win10).
- Docker Desktop pour Windows configuré pour utiliser WSL 2. Pour plus d’informations, consultez [Backend Docker Desktop WSL 2](https://docs.docker.com/docker-for-windows/wsl/).
- [Visual Studio Code installée dans votre environnement Windows](https://code.visualstudio.com/docs/setup/windows) avec les trois extensions suivantes installées :

  - [Pack d’extension de développement distant](https://aka.ms/vscode-remote/download/extension) : cette extension vous permet de générer et d’exécuter du code dans WSL 2.
  - [Docker pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) : cette extension doit être activée dans l’environnement **WSL: Ubuntu-18.04** de Visual Studio Code.
  - [Azure IoT Tools pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) : cette extension doit être activée dans l’environnement **WSL: Ubuntu-18.04** de Visual Studio Code.

- Exécutez les commandes suivantes dans votre environnement WSL 2 pour installer les outils de génération requis :

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- L’[interface de ligne de commande Azure](/cli/azure/install-azure-cli-apt) installée dans votre environnement WSL 2 pour gérer vos ressources Azure.

  > [!TIP]
  > Si vous préférez, vous pouvez exécuter les commandes `az` dans le shell [Azure Cloud Shell](https://shell.azure.com/) où l’interface de ligne de commande est préinstallée.

### <a name="create-an-iot-edge-device"></a>Créer un appareil IoT Edge

Ici, les commandes créent un appareil IoT Edge qui s’exécute sur une machine virtuelle Azure. L’exécution d’un appareil IoT Edge sur une machine virtuelle est utile pour tester votre déploiement si vous n’avez pas accès à un appareil réel.

Pour inscrire un appareil IoT Edge dans votre hub IoT, exécutez les commandes suivantes dans votre environnement WSL 2. Utilisez la commande `az login` pour vous connecter à votre abonnement Azure :

```azurecli
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

Pour créer une machine virtuelle Azure avec le runtime IoT Edge installé, exécutez les commandes suivantes. Mettez à jour les espaces réservés avec les valeurs appropriées :

```azurecli
az group create --name bridge-edge-resources --location eastus
az deployment group create \
--resource-group bridge-edge-resources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='{unique DNS name for virtual machine}' \
--parameters adminUsername='{admin user name}' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name} -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="{admin password for virtual machine}"
```

Vous disposez maintenant d’un runtime IoT Edge en cours d’exécution sur une machine virtuelle. Vous pouvez utiliser la commande suivante pour vérifier que **$edgeAgent** et **$edgeHub** sont en cours d’exécution sur l’appareil :

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> Vous êtes facturé pendant l’exécution de cette machine virtuelle. Veillez à l’arrêter lorsque vous ne l’utilisez pas et supprimez-la une fois que vous avez fini de l’utiliser.

### <a name="download-the-source-code"></a>Télécharger le code source

Dans les étapes suivantes, vous générez l’image Docker dans votre environnement WSL 2. Pour cloner le référentiel **iot-plug-and-play-bridge**, exécutez les commandes suivantes dans un interpréteur de commandes bash WSL 2 dans un dossier approprié :

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

L’exécution de la commande précédente prendra sûrement plusieurs minutes.

### <a name="update-the-dockerfile"></a>Mettre à jour le fichier *Dockerfile*

Démarrez VS Code, ouvrez la palette de commandes, entrez *WSL distant : ouvrir le dossier dans WSL*, puis ouvrez le dossier *iot-plug-and-play-bridge* qui contient le référentiel cloné.

Ouvrez le fichier *pnpbridge\Dockerfile.amd64*. Modifiez les définitions des variables d’environnement comme suit :

```dockerfile
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> Vous pouvez utiliser la commande suivante pour récupérer la chaîne de connexion de l’appareil : `az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

Il existe des exemples de fichiers *Dockerfile* pour les autres architectures.

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>Générer l’image de module du pont IoT Plug-and-Play

Dans VS Code, ouvrez la palette de commandes, entrez *Registres Docker : Se connecter à l’interface de commande Docker*, puis sélectionnez votre abonnement Azure et le registre de conteneurs. Cette commande permet à Docker de se connecter à votre registre de conteneurs et de charger l’image de module.

Ouvrez le fichier *pnpbridge/module.json*. Ajoutez `{your container registry name}.azurecr.io/iotpnpbridge` comme référentiel d’images de conteneur et `v1` en tant que version.

Dans VS Code, cliquez avec le bouton droit sur le fichier *pnpbridge/module.json* dans la vue **Explorateur**, sélectionnez **Générer et pousser l’image du module IoT Edge**, puis sélectionnez **amd64** comme plateforme.

Dans VS Code, dans la vue **Docker**, vous pouvez parcourir le contenu de votre registre de conteneurs qui comprend désormais l’image de module **iotpnpbridge:v1-amd64**.

### <a name="create-a-container-registry"></a>Créer un registre de conteneurs

Un appareil IoT Edge télécharge ses images de module à partir d’un registre de conteneurs. Cet exemple utilise le registre de conteneurs Azure.

Créez un registre de conteneurs Azure dans le groupe de ressources **bridge-edge-resources**. Activez ensuite l’accès administrateur à votre registre de conteneurs et récupérez les informations d’identification dont votre appareil IoT Edge a besoin pour télécharger les images du module :

```azurecli
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>Créer le manifeste de déploiement

Un manifeste de déploiement IoT Edge spécifie les modules et les valeurs de configuration à déployer sur un appareil IoT Edge.

Dans VS Code, ouvrez le fichier *pnpbridge/deployment.template.json* :

- Mettez à jour `registryCredentials` avec les valeurs de la commande précédente. La valeur `address` ressemble à `{your container registry name}.azurecr.io`.
- Mettez à jour la valeur `image` pour `ModulePnpBridge`. L’image du module se présente ainsi : `{your container registry}.azurecr.io/iotpnpbridge:v1-amd64`.
- Remplacez `PnPBridgeConfig` par le code JSON suivant pour configurer l’adaptateur de détection de CO2 :

  ```json
  "PnpBridgeConfig": {
    "pnp_bridge_interface_components": [
      {
        "_comment": "Component 1 - Modbus Device",
        "pnp_bridge_component_name": "Co2Detector1",
        "pnp_bridge_adapter_id": "modbus-pnp-interface",
        "pnp_bridge_adapter_config": {
          "unit_id": 1,
          "tcp": {
            "host": "10.159.29.2",
            "port": 502
          },
          "modbus_identity": "DL679"
        }
      }
    ],
    "pnp_bridge_adapter_global_configs": {
      "modbus-pnp-interface": {
        "DL679": {
          "telemetry": {
            "co2": {
              "startAddress": "40001",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 5000,
              "conversionCoefficient": 1
            },
            "temperature": {
              "startAddress": "40003",
              "length": 1,
              "dataType": "decimal",
              "defaultFrequency": 5000,
              "conversionCoefficient": 0.01
            }
          },
          "properties": {
            "firmwareVersion": {
              "startAddress": "40482",
              "length": 1,
              "dataType": "hexstring",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "modelName": {
              "startAddress": "40483",
              "length": 2,
              "dataType": "string",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmStatus_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "boolean",
              "defaultFrequency": 1000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmThreshold_co2": {
              "startAddress": "40225",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 30000,
              "conversionCoefficient": 1,
              "access": 2
            }
          },
          "commands": {
            "clearAlarm_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "flag",
              "conversionCoefficient": 1
            }
          }
        }
      }
    }
  }
  ```

  Enregistrez les changements.

Dans VS Code, cliquez avec le bouton droit sur le fichier *pnpbridge/deployment.template.json* dans la vue **Explorateur**, puis sélectionnez **Générer un manifeste de déploiement IoT Edge**. Cette commande génère le manifeste de déploiement *pnpbridge/config/deployment.amd64.json*.

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>Déployer le pont sur votre appareil IoT Edge

Dans VS Code, ouvrez la palette de commandes, entrez *Azure IoT Hub : sélectionner le hub IoT*, puis sélectionnez votre abonnement et votre hub IoT.

Dans VS Code, cliquez avec le bouton droit sur le fichier *pnpbridge/config/deployment.amd64.json* dans la vue **Explorateur**, sélectionnez **Créer un déploiement pour un seul appareil**, puis sélectionnez **bridge-edge-device**.

Pour voir l’état des modules sur votre appareil, exécutez la commande suivante :

```azurecli
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

La liste des modules en cours d’exécution comprend désormais le module **ModulePnpBridge** qui est configuré pour utiliser l’adaptateur de détection de CO2.

### <a name="tidy-up"></a>Nettoyer

Pour supprimer la machine virtuelle et le registre de conteneurs de votre abonnement Azure, exécutez la commande suivante :

```azurecli
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>Structure de dossiers

*pnpbridge\deps\azure-iot-sdk-c-pnp* : Sous-modules Git contenant le kit de développement logiciel (SDK) Azure IoT Device SDK pour C.

*pnpbridge\scripts* : Scripts de génération.

*pnpbridge\src* : Code source pour le noyau du pont IoT Plug-and-Play.

*pnpbridge\src\adapters* : Code source pour divers adaptateurs de pont IoT Plug-and-Play.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le pont IoT Plug-and-Play, rendez-vous sur le référentiel GitHub [Pont IoT Plug-and-Play](https://github.com/Azure/iot-plug-and-play-bridge).