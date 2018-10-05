---
title: Configurer des appareils Azure IoT Edge pour des proxys réseau | Microsoft Docs
description: Découvrez comment configurer le runtime Azure IoT Edge et les modules IoT Edge accessibles sur Internet pour communiquer via un serveur proxy.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 09/24/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6e6a1d2f758cabca41ac405a01de1f0d8bfd0a7b
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037454"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configurer un appareil IoT Edge pour communiquer via un serveur proxy

Les appareils IoT Edge envoient des requêtes HTTPS pour communiquer avec IoT Hub. Si votre appareil est connecté à un réseau qui utilise un serveur proxy, vous devez configurer le runtime IoT Edge pour communiquer via le serveur. Les serveurs proxy peuvent également impacter des modules IoT Edge individuels s’ils effectuent des requêtes HTTP ou HTTPS qui ne sont pas routées à travers le hub Edge. 

Pour configurer un appareil IoT Edge devant communiquer avec un serveur proxy, effectuez les étapes de base suivantes : 

1. Installez le runtime IoT Edge sur votre appareil. 
2. Configurez le démon Docker et le démon IoT Edge sur votre appareil pour utiliser le serveur proxy.
3. Configurez les propriétés d’edgeAgent dans le fichier config.yaml sur votre appareil.
4. Définissez les variables d’environnement pour le runtime IoT Edge et les autres modules IoT Edge dans le manifeste de déploiement. 

## <a name="install-the-runtime"></a>Installer le runtime

Si vous installez le runtime IoT Edge sur un appareil Linux, configurez le Gestionnaire de package de sorte à passer par votre serveur proxy pour accéder au package d’installation. Par exemple, [Configurez apt-get pour utiliser un proxy http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Une fois que votre gestionnaire de package est configuré, suivez les instructions dans [Installer le runtime Azure IoT Edge sur Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) ou [Installer le runtime Azure IoT Edge sur Linux (x64)](how-to-install-iot-edge-linux.md) comme d’habitude. 

Si vous installez le runtime IoT Edge sur un appareil Windows, vous devez passer par votre serveur proxy pour accéder au package d’installation. Vous pouvez configurer les informations de proxy dans les paramètres Windows ou inclure vos informations de proxy directement dans le script d’installation. Le script Powershell suivant est un exemple d’installation de Windows qui utilise l’argument `-proxy` :

```powershell
. {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows
```

Pour plus d’informations et d’options d’installation, consultez [Installer le runtime Azure IoT Edge sur Windows pour l’utiliser avec des conteneurs Windows](how-to-install-iot-edge-windows-with-windows.md) ou [Installer le runtime Azure IoT Edge sur Windows pour l’utiliser avec des conteneurs Linux](how-to-install-iot-edge-windows-with-linux.md).

Une fois que le runtime IoT Edge est installé, utilisez la section suivante pour le configurer avec vos informations de proxy. 

## <a name="configure-the-daemons"></a>Configurer les démons

Les démons Docker et IoT Edge exécutés sur votre appareil IoT Edge doivent être configurés pour utiliser le serveur proxy. Le démon Docker effectue des requêtes web pour tirer (pull) les images conteneur des registres de conteneurs. Le démon IoT Edge effectue des requêtes web pour communiquer avec IoT Hub.

### <a name="docker-daemon"></a>Démon Docker

Pour savoir comment configurer le démon Docker avec des variables d’environnement, consultez la documentation de Docker. La plupart des registres de conteneurs (y compris les registres de conteneurs DockerHub et Azure) prennent en charge les requêtes HTTPS. Vous devez donc définir la variable **HTTPS_PROXY**. Si vous tirez des images d’un registre qui ne prend pas en charge le protocole TLS, vous devez définir la variable **HTTP_PROXY**. 

Consultez l’article correspondant à votre version de Docker : 

* [Docker](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Docker pour Windows](https://docs.docker.com/docker-for-windows/#proxies)

### <a name="iot-edge-daemon"></a>Démon IoT Edge

Le démon IoT Edge est configuré de manière similaire au démon Docker. Toutes les requêtes envoyées à IoT Hub par IoT Edge utilisent HTTPS. Suivez les étapes ci-dessous afin de définir une variable d’environnement pour le service, selon votre système d’exploitation. 

#### <a name="linux"></a>Linux

Ouvrez un éditeur dans le terminal pour configurer le démon IoT Edge. 

```bash
sudo systemctl edit iotedge
```

Entrez le texte suivant, en remplaçant **\<proxy URL>** par l’adresse et le port de votre serveur proxy. Ensuite, enregistrez et quittez. 

```text
[Service]
Environment="https_proxy=<proxy URL>"
```

Actualisez Service Manager afin de charger la nouvelle configuration pour IoT Edge.

```bash
sudo systemctl daemon-reload
```

Redémarrez IoT Edge pour que les modifications soient prises en compte.

```bash
sudo systemctl restart iotedge
```

Vérifiez que votre variable d’environnement a été créée et que la nouvelle configuration a été chargée. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Ouvrez une fenêtre PowerShell en tant qu’administrateur et exécutez la commande suivante pour modifier le registre avec la nouvelle variable d’environnement. Remplacez **\<proxy url>** par l’adresse et le port de votre serveur proxy. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Redémarrez IoT Edge pour que les modifications soient prises en compte.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-edge-agent"></a>Configurer l’agent Edge

L’agent Edge est le premier module démarré sur un appareil IoT Edge. Il est démarré pour la première fois en fonction des informations contenues dans le fichier config.yaml sur IoT Edge. L’agent Edge se connecte ensuite à IoT Hub pour récupérer les manifestes de déploiement, qui déclarent les autres modules devant être déployés sur l’appareil.

Ouvrez le fichier config.yaml sur votre appareil IoT Edge. Sur les systèmes Linux, ce fichier se trouve dans **/etc/iotedge/config.yaml**. Sur les systèmes Windows, ce fichier se trouve dans **C:\ProgramData\iotedge\config.yaml**. Le fichier de configuration étant protégé, vous devez avoir des privilèges Administrateur pour y accéder. Sur les systèmes Linux, cela implique d’utiliser la commande `sudo` avant d’ouvrir le fichier dans votre éditeur de texte habituel. Sur Windows, vous devez ouvrir un éditeur de texte, tel que le bloc-notes, en tant qu’administrateur, puis ouvrir le fichier. 

Dans le fichier config.yaml, recherchez la section **Edge Agent module spec**. La définition de l’agent Edge inclut un paramètre **env** où vous pouvez ajouter des variables d’environnement. 

![Définition d’edgeAgent](./media/how-to-configure-proxy-support/edgeagent-unedited.png)

Supprimez les accolades servant d’espaces réservés pour le paramètre env, puis ajoutez la nouvelle variable sur une nouvelle ligne. N’oubliez pas que les mises en retrait dans YAML sont de deux espaces. 

```yaml
https_proxy: "<proxy URL>"
```

Le runtime IoT Edge utilise AMQP par défaut pour communiquer avec IoT Hub. Certains serveurs proxy bloquent les ports AMQP. Si c’est le cas, vous devez également configurer edgeAgent pour utiliser AMQP sur WebSocket. Ajoutez une deuxième variable d’environnement.

```yaml
UpstreamProtocol: "AmqpWs"
```

![Définition d’edgeAgent avec les variables d’environnement](./media/how-to-configure-proxy-support/edgeagent-edited.png)

Enregistrez les modifications dans config.yaml et fermez l’éditeur. Redémarrez IoT Edge pour que les modifications soient prises en compte. 

* Linux : 

   ```bash
   sudo systemctl restart iotedge
   ```

* Windows :

   ```powershell
   Restart-Service iotedge
   ```

## <a name="configure-deployment-manifests"></a>Configurer les manifestes de déploiement  

Une fois que vous avez configuré votre appareil IoT Edge pour communiquer via votre serveur proxy, vous devez également déclarer les variables d’environnement dans tous les manifestes de déploiement que vous allez utiliser. Les deux modules de runtime, edgeAgent et edgeHub, doivent toujours être configurés avec le serveur proxy pour maintenir la communication avec IoT Hub. Vous pouvez configurer les modules IoT Edge pour communiquer via un serveur proxy, mais cela n’est pas nécessaire pour les modules qui routent leurs messages via edgeHub ou qui communiquent uniquement avec d’autres modules sur l’appareil. 

Vous pouvez créer des manifestes de déploiement à l’aide du portail Azure, ou manuellement en modifiant un fichier JSON. 

### <a name="azure-portal"></a>Portail Azure

Quand vous créez des déploiements sur des appareils IoT Edge à l’aide de l’Assistant **Définir des modules**, chaque module a une section **Variables d’environnement** dans laquelle vous pouvez configurer des connexions au serveur proxy. 

Pour configurer les modules edgeAgent et edgeHub, sélectionnez **Configurer les paramètres avancés du runtime Edge** à la première étape de l’Assistant. 

![Configurer les paramètres avancés du runtime Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Ajoutez la variable d’environnement **https_proxy** aux définitions des deux modules edgeAgent et edgeHub. Si vous avez inclus la variable d’environnement **UpstreamProtocol** dans le fichier config.yaml sur votre appareil IoT Edge, ajoutez-la également à la définition du module edgeAgent. 

![Définition des variables d'environnement](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Tous les autres modules que vous ajoutez à un manifeste de déploiement suivent le même modèle. La page où vous définissez le nom et l’image du module contient une section de variables d’environnement.

### <a name="json-deployment-manifest-files"></a>Fichiers de manifeste de déploiement JSON

Quand vous créez des déploiements sur des appareils IoT Edge en utilisant des modèles dans Visual Studio Code ou en créant manuellement des fichiers JSON, vous pouvez ajouter les variables d’environnement directement à chaque définition de module. 

Utilisez le format JSON suivant : 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Après l’ajout des variables d’environnement, votre définition de module doit ressembler à l’exemple edgeHub suivant :

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "https://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Si vous avez inclus la variable d’environnement **UpstreamProtocol** dans le fichier config.yaml sur votre appareil IoT Edge, ajoutez-la également à la définition du module edgeAgent. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les rôles du [runtime IoT Edge](iot-edge-runtime.md)

Résoudre les erreurs d’installation et de configuration avec [Problèmes courants et résolutions pour Azure IoT Edge](troubleshoot.md)

