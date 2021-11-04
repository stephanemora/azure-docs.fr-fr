---
title: 'Didacticiel : Déployer un registre connecté sur un appareil IoT Edge imbriqué'
description: Utilisez des commandes Azure CLI pour déployer un registre de conteneurs Azure connecté dans une hiérarchie Azure IoT Edge imbriquée.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3652a87de4e517f2f40821a5e6e7367c1c88b078
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131098000"
---
# <a name="tutorial-deploy-a-connected-registry-to-a-nested-iot-edge-hierarchy"></a>Didacticiel : Déployer un registre connecté sur une hiérarchie Azure IoT Edge imbriquée

Ce didacticiel explique comment utiliser des commandes Azure CLI pour créer une hiérarchie à deux couches d’appareils Azure IoT Edge et déployer un [registre connecté](intro-connected-registry.md) en tant que module sur chaque couche.

Pour obtenir une vue d’ensemble de l’utilisation d’un registre connecté avec IoT Edge, consultez [Utilisation d’un registre connecté avec Azure IoT Edge](overview-connected-registry-and-iot-edge.md). Dans ce scénario, un appareil dans la [couche supérieure](overview-connected-registry-and-iot-edge.md#top-layer) communique avec un registre cloud, et un appareil dans la [couche inférieure](overview-connected-registry-and-iot-edge.md#top-layer) communique avec son registre connecté parent dans la couche supérieure. 

[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]
* Azure IoT Hub. Pour découvrir les étapes de déploiement, consultez [Créer un hub IoT à l’aide du portail Azure](../iot-hub/iot-hub-create-through-portal.md).
* Deux ressources de registre connecté dans Azure. Pour découvrir les étapes du déploiement, consultez les démarrages rapides à l’aide d’[Azure CLI][quickstart-connected-registry-cli] ou du [portail Azure][quickstart-connected-registry-portal]. 

    * Pour la couche supérieure, le registre connecté peut être en mode lecture-écriture ou en mode lecture seule. Cet article part du principe que le registre connecté est en mode lecture-écriture, et que son nom est stocké dans la variable d’environnement *$CONNECTED _REGISTRY_RW*.
    * Pour la couche inférieure, le registre connecté doit être en mode lecture seule. Cet article part du principe que le nom du registre connecté est stocké dans la variable d’environnement *$CONNECTED _REGISTRY_RO*.

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="retrieve-connected-registry-configuration"></a>Récupérer la configuration de registre connecté 

Pour déployer chaque registre connecté sur l’appareil IoT Edge dans la hiérarchie, vous devez récupérer les paramètres de configuration à partir de la ressource de registre connecté dans Azure. Si nécessaire, exécutez la commande [az acr connected-registry get-settings][az-acr-connected-registry-get-settings] pour chaque registre connecté afin de récupérer la configuration. 

Par défaut, les informations de paramètres n’incluent pas le mot de passe du [jeton de synchronisation](overview-connected-registry-access.md#sync-token) qui est également nécessaire pour déployer le registre connecté. Vous pouvez également générer l’un des mots de passe en passant paramètre `--generate-password 1` ou `generate-password 2`. Enregistrez le mot de passe généré dans un emplacement sûr. Vous ne pourrez le récupérer une nouvelle fois.

> [!WARNING]
> La régénération d’un mot de passe déclenche une rotation des informations d’identification du jeton de synchronisation. Si vous avez configuré un appareil à l’aide du mot de passe précédent, vous devez mettre à jour la configuration.

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

# Run the command for each registry resource in the hierarchy

az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --parent-protocol https

az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RO \
  --parent-protocol https
```

[!INCLUDE [container-registry-connected-connection-configuration](../../includes/container-registry-connected-connection-configuration.md)]

## <a name="configure-deployment-manifests-for-the-nested-iot-edge-devices"></a>Configurer les manifestes de déploiement pour les appareils IoT Edge imbriqués

Un manifeste de déploiement est un document JSON décrivant les modules à déployer sur un appareil IoT Edge. Pour plus d’informations, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](../iot-edge/module-composition.md).

Pour déployer le module de registre connecté sur chaque appareil IoT Edge à l’aide d’Azure CLI, enregistrez les manifestes de déploiement suivants localement en tant que fichiers JSON. Utilisez les informations des sections précédentes pour mettre à jour les valeurs JSON pertinentes dans chaque manifeste. Vous allez utiliser les chemins d’accès de fichiers dans la section suivante lors de l’exécution de la commande qui applique la configuration à votre appareil.

### <a name="deployment-manifest-for-the-top-layer"></a>Manifeste de déploiement pour la couche supérieure

Pour l’appareil dans la couche supérieure, créez un fichier manifeste de déploiement `deploymentTopLayer.json` avec le contenu suivant. Ce manifeste est similaire à celui utilisé dans [Démarrage rapide : Déployer un registre connecté sur un appareil IOT Edge](quickstart-deploy-connected-registry-iot-edge-cli.md). 

> [!NOTE]
> Si vous avez déjà déployé un registre connecté sur un appareil IoT Edge de couche supérieure en suivant le [démarrage rapide](quickstart-deploy-connected-registry-iot-edge-cli.md), vous pouvez l’utiliser dans la couche supérieure d’une hiérarchie imbriquée. Vous devrez modifier les étapes de déploiement décrites dans ce didacticiel pour le configurer dans la hiérarchie (non montré).

[!INCLUDE [container-registry-connected-iot-edge-manifest](../../includes/container-registry-connected-iot-edge-manifest.md)]

### <a name="deployment-manifest-for-the-lower-layer"></a>Manifeste de déploiement pour la couche inférieure

Pour l’appareil dans la couche inférieure, créez un fichier manifeste de déploiement `deploymentLowerLayer.json` avec le contenu suivant.

Les fichiers de déploiement de la couche inférieure et de la couche supérieure sont globalement similaires. Les différences sont les suivantes : 

- Il extrait les images requises à partir du registre connecté dans la couche supérieure au lieu du registre cloud. 
    
    Quand vous configurez le registre connecté de la couche supérieure, assurez-vous qu’il synchronise toutes les images requises localement (`azureiotedge-agent`, `azureiotedge-hub`, `azureiotedge-api-proxy`, `acr/connected-registry`). L’appareil IoT de la couche inférieure doit extraire ces images du registre connecté de la couche supérieure.
- Il utilise le jeton de synchronisation configuré au niveau de la couche inférieure pour s’authentifier auprès du registre connecté de la couche supérieure.
- Il configure le point de terminaison de la passerelle parente avec l’adresse IP ou le nom de domaine complet (FQDN) du registre connecté de la couche supérieure au lieu du FQDN du registre cloud. 

> [!IMPORTANT]
> Dans le manifeste de déploiement suivant, `$upstream` est utilisé comme adresse IP ou FQDN de l’appareil hébergeant le registre connecté parent. Toutefois, `$upstream` n’est pas pris en charge dans une variable d’environnement. Le registre connecté doit lire la variable d’environnement `ACR_PARENT_GATEWAY_ENDPOINT` pour obtenir le point de terminaison de la passerelle parente. Au lieu d’utiliser `$upstream`, le registre connecté prend en charge la résolution dynamique de l’adresse IP ou du FQDN à partir d’une autre variable d’environnement. Sur l’IoT Edge imbriqué, il existe une variable d’environnement `$IOTEDGE_PARENTHOSTNAME` dans la couche inférieure, qui équivaut à l’adresse IP ou au FQDN de l’appareil parent. Remplacez manuellement la variable d’environnement en tant que valeur de `ParentGatewayEndpoint` dans la chaîne de connexion, afin d’éviter de coder en dur l’adresse IP ou le FQDN de l’appareil parent. Étant donné que l’appareil parent dans ce exemple exécute nginx sur le port 8000, passez la valeur `$IOTEDGE_PARENTHOSTNAME:8000`. Vous devez également sélectionner le protocole approprié dans `ParentEndpointProtocol`.

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                "modules": {
                    "connected-registry": {
                        "settings": {
                            "image": "$upstream:8000/acr/connected-registry:0.5.0",
                            "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"]}}"
                        },
                        "type": "docker",
                        "env": {
                            "ACR_REGISTRY_CONNECTION_STRING": {
                                "value": "ConnectedRegistryName=<REPLACE_WITH_CONNECTED_REGISTRY_NAME>;SyncTokenName=<REPLACE_WITH_SYNC_TOKEN_NAME>;SyncTokenPassword=<REPLACE_WITH_SYNC_TOKEN_PASSWORD>;ParentGatewayEndpoint=$IOTEDGE_PARENTHOSTNAME:8000;ParentEndpointProtocol=https"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    },
                    "IoTEdgeApiProxy": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-api-proxy:1.1.2",
                            "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\": \"8000\"}]}}}"
                        },
                        "type": "docker",
                        "version": "1.0",
                        "env": {
                            "NGINX_DEFAULT_PORT": {
                                "value": "8000"
                            },
                            "CONNECTED_ACR_ROUTE_ADDRESS": {
                                "value": "connected-registry:8080"
                            },
                            "NGINX_CONFIG_ENV_VAR_LIST": {
                                    "value": "NGINX_DEFAULT_PORT,BLOB_UPLOAD_ROUTE_ADDRESS,CONNECTED_ACR_ROUTE_ADDRESS,IOTEDGE_PARENTHOSTNAME,DOCKER_REQUEST_ROUTE_ADDRESS"
                            },
                            "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                "value": "AzureBlobStorageonIoTEdge:11002"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "startupOrder": 3
                    }
                },
                "runtime": {
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "registryCredentials": {
                            "connectedregistry": {
                                "address": "$upstream:8000",
                                "password": "<REPLACE_WITH_SYNC_TOKEN_PASSWORD>",
                                "username": "<REPLACE_WITH_SYNC_TOKEN_NAME>"
                            }
                        }
                    },
                    "type": "docker"
                },
                "schemaVersion": "1.1",
                "systemModules": {
                    "edgeAgent": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-agent:1.2.4",
                            "createOptions": ""
                        },
                        "type": "docker",
                        "env": {
                            "SendRuntimeQualityTelemetry": {
                                "value": "false"
                            }
                        }
                    },
                    "edgeHub": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-hub:1.2.4",
                            "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                        },
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always"
                    }
                }
            }
        },
        "$edgeHub": {
            "properties.desired": {
                "routes": {
                    "route": "FROM /messages/* INTO $upstream"
                },
                "schemaVersion": "1.1",
                "storeAndForwardConfiguration": {
                    "timeToLiveSecs": 7200
                }
            }
        }
    }
}
```

## <a name="set-up-and-deploy-connected-registry-modules-on-nested-iot-edge-devices"></a>Configurer et déployer des modules de registre connecté sur des appareils IoT Edge imbriqués

Les étapes suivantes sont adaptées à partir de la procédure décrite dans [Didacticiel : Créer une hiérarchie d’appareils IoT Edge](../iot-edge/tutorial-nested-iot-edge.md). Elles sont spécifiques au déploiement de modules de registre connecté dans la hiérarchie IOT Edge. Pour plus d’informations sur les étapes individuelles, consultez ce didacticiel.

### <a name="create-top-layer-and-lower-layer-devices"></a>Créer des appareils de couche supérieure et de couche inférieure

Créez des machines virtuelles de couche supérieure et de couche inférieure à l’aide d’un [modèle ARM](https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json) existant. Le modèle installe également l’agent IoT Edge. Si vous souhaitez plutôt opérer le déploiement à partir de vos propres appareils, consultez [Didacticiel : Installer ou désinstaller Azure IoT Edge pour Linux](../iot-edge/how-to-install-iot-edge.md) afin de savoir comment configurer l’appareil manuellement.

> [!IMPORTANT]
> Pour accéder ultérieurement aux modules déployés sur l’appareil de couche supérieure, veillez à ouvrir les ports entrants suivants : 8000, 443, 5671 et 8883. Pour plus d’informations, consultez [Guide d’ouverture de ports vers une machine virtuelle avec le portail Azure](../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="create-and-configure-the-hierarchy"></a>Créer et configurer la hiérarchie

Servez-vous de l’outil `iotedge-config` pour créer et configurer votre hiérarchie en procédant comme suit dans Azure CLI ou Azure Cloud Shell :

1. Téléchargez l’outil de configuration.
    ```bash
    mkdir nestedIotEdgeTutorial
    cd ~/nestedIotEdgeTutorial
    wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
    tar -xvf iotedge_config.tar
    ```

    Cette étape crée le dossier `iotedge_config_cli_release` dans le répertoire de votre didacticiel. Le fichier de modèle utilisé pour créer la hiérarchie d’appareils est le fichier `iotedge_config.yaml` situé dans `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial`. Le même répertoire contient deux manifestes de déploiement pour les couches supérieure et inférieure, à savoir les fichiers `deploymentTopLayer.json` et `deploymentLowerLayer.json`. 

1. Modifiez `iotedge_config.yaml` avec vos informations. Celles-ci comprennent les noms `iothub_hostname` et `iot_name`, les noms de fichiers manifestes de déploiement pour les couches supérieure et inférieure, ainsi que les informations d’identification du jeton client que vous avez créées pour extraire des images à partir de l’amont de chaque couche. Voici un exemple de configuration

    ```yaml
    config_version: "1.0"

    iothub:
        iothub_hostname: <REPLACE_WITH_HUB_NAME>.azure-devices.net
        iothub_name: <REPLACE_WITH_HUB_NAME>
        ## Authentication method used by IoT Edge devices: symmetric_key or x509_certificate
        authentication_method: symmetric_key 

        ## Root certificate used to generate device CA certificates. Optional. If not provided a self-signed CA will be generated
        # certificates:
        #   root_ca_cert_path: ""
        #   root_ca_cert_key_path: ""

        ## IoT Edge configuration template to use
    configuration:
        template_config_path: "./templates/tutorial/device_config.toml"
        default_edge_agent: "$upstream:8000/azureiotedge-agent:1.2.4"

        ## Hierarchy of IoT Edge devices to create
    edgedevices:
        device_id: top-layer
        edge_agent: "<REPLACE_WITH_REGISTRY_NAME>.azurecr.io/azureiotedge-agent:1.2.4" ## Optional. If not provided, default_edge_agent will be used
        deployment: "./templates/tutorial/deploymentTopLayer.json" ## Optional. If provided, the given deployment file will be applied to the newly created device
            # hostname: "FQDN or IP" ## Optional. If provided, install.sh will not prompt user for this value nor the parent_hostname value
        container_auth: ## The token used to pull the image from cloud registry
            serveraddress: "<REPLACE_WITH_REGISTRY_NAME>.azurecr.io"
            username: "<REPLACE_WITH_SYNC_TOKEN_NAME_FOR_TOP_LAYER>"
            password: "<REPLACE_WITH_SYNC_TOKEN_PASSWORD_FOR_TOP_LAYER>"
        child:
            - device_id: lower-layer
              deployment: "./templates/tutorial/deploymentLowerLayer.json" ## Optional. If provided, the given deployment file will be applied to the newly created device
               # hostname: "FQDN or IP" ## Optional. If provided, install.sh will not prompt user for this value nor the parent_hostname value
              container_auth: ## The token used to pull the image from parent connected registry
                serveraddress: "$upstream:8000"
                username: "<REPLACE_WITH_SYNC_TOKEN_NAME_FOR_LOWER_LAYER>"
                password: "<REPLACE_WITH_SYNC_TOKEN_PASSWORD_FOR_LOWER_LAYER>"
    ```


1. Préparez les fichiers de déploiement des couches supérieure et inférieure (deploymentTopLayer.json et deploymentLowerLayer.json). Copiez les [fichiers manifestes de déploiement](#configure-deployment-manifests-for-the-nested-iot-edge-devices) que vous avez créés précédemment dans cet article dans le dossier suivant : `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial`.

1. Accédez à votre répertoire `iotedge_config_cli_release` et exécutez l’outil pour créer votre hiérarchie d’appareils IoT Edge.

    ```bash
    cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
    ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
    ```

    Quand l’indicateur `--output` est défini, l’outil crée les certificats d’appareil, les bundles de certificats ainsi qu’un fichier journal dans le répertoire de votre choix. Quand l’indicateur `-f` est défini, l’outil recherche automatiquement les appareils IoT Edge existants dans votre hub IoT, et les supprime, pour éviter les erreurs et nettoyer le hub.

    L’exécution de l’outil peut prendre plusieurs minutes.

1. Copiez les fichiers `top-layer.zip` et `lower-layer.zip` générés à l’étape précédente vers les machines virtuelles des couches supérieure et inférieure à l’aide de la commande `scp`.

    ```bash
    scp <PATH_TO_CONFIGURATION_BUNDLE>   <USER>@<VM_IP_OR_FQDN>:~
    ```

1. Connectez-vous à l’appareil de la couche supérieure pour installer le pack de configuration.
    1. Décompressez le pack de configuration. Vous devez d’abord installer le fichier zip.

        ```bash
        sudo apt install zip
        unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip #unzip top-layer.zip
        ```
    1. Exécutez `sudo ./install.sh`, puis entrez l’adresse IP ou le nom d’hôte. Nous vous recommandons d’utiliser l’adresse IP.
    1. Exécutez `sudo iotedge list` pour vérifier que tous les modules fonctionnent.

1. Connectez-vous à l’appareil de la couche inférieure pour installer le pack de configuration.
    1. Décompressez le pack de configuration. Vous devez d’abord installer le fichier zip.

        ```bash
        sudo apt install zip
        unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip #unzip lower-layer.zip
        ```
    1. Exécutez `sudo ./install.sh`, puis entrez l’appareil et les adresses IP ou les noms d’hôte parents. Nous vous recommandons d’utiliser les adresses IP.
    1. Exécutez `sudo iotedge list` pour vérifier que tous les modules fonctionnent.
 
    
Si vous n’avez pas spécifié de fichier de déploiement pour la configuration de l’appareil, ou si des problèmes de déploiement se produisent, tels que la présence d’un manifeste de déploiement non valide sur l’appareil de couche supérieure ou inférieure, déployez les modules manuellement. Voir la section suivante.

## <a name="manually-deploy-the-connected-registry-module"></a>Déployer manuellement le module de registre connecté

Pour déployer manuellement le module de registre connecté sur un appareil IoT Edge, utilisez la commande suivante :

```azurecli
az iot edge set-modules \
  --device-id <device-id> \
  --hub-name <hub-name> \
  --content <deployment-manifest-filename>
```

Pour plus d’informations, consultez [Déployer des modules Azure IoT Edge avec Azure CLI](../iot-edge/how-to-deploy-modules-cli.md).

Une fois le déploiement réussi, le registre connecté affiche l’état `Online`.

Pour vérifier l’état du registre connecté, utilisez la commande [az acr connected-registry show][az-acr-connected-registry-show] suivante :

```azurecli
az acr connected-registry show \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RO \
  --output table
```

Il se peut que vous deviez patienter quelques minutes jusqu’à ce que le déploiement du registre connecté se termine.

Une fois le déploiement réussi, le registre connecté affiche l’état `Online`.

Pour résoudre des problèmes de déploiement, exécutez la commande `iotedge check` sur l’appareil concerné. Pour plus d’informations, consultez la page [Dépannage](../iot-edge/tutorial-nested-iot-edge.md#troubleshooting).

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à déployer un registre connecté sur un appareil IoT Edge imbriqué. Passez au guide suivant pour découvrir comment extraire des images du registre connecté nouvellement déployé.

> [!div class="nextstepaction"]
> [Extraire des images à partir d’un registre connecté][pull-images-from-connected-registry]

<!-- LINKS - internal -->
[az-acr-connected-registry-get-settings]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_get_settings
[az-acr-connected-registry-show]: /cli/azure/acr/connected-registr#az_acr_connected_registry_show
[az-acr-import]: /cli/azure/acr#az-acr-import
[az-acr-token-credential-generate]: /cli/azure/acr/credential#az-acr-token-credential-generate
[container-registry-intro]: container-registry-intro.md
[pull-images-from-connected-registry]: pull-images-from-connected-registry.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]: quickstart-connected-registry-portal.md
