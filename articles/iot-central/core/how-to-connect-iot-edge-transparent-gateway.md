---
title: Connecter une passerelle transparente IoT Edge à une application Azure IoT Central
description: Guide pratique pour connecter des appareils via une passerelle transparente IoT Edge à une application IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 2703e5f92ad0756ece8088c4948170ea13fa0e06
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109683440"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>Guide pratique pour connecter des appareils via une passerelle transparente IoT Edge

Un appareil IoT Edge peut fonctionner en tant que passerelle fournissant une connexion entre d’autres appareils sur un réseau local et votre application IoT Central. Vous utilisez une passerelle lorsque l’appareil ne peut pas accéder directement à votre application IoT Central.

IoT Edge prend en charge les modèles de passerelle [*transparente* et de *traduction*.](../../iot-edge/iot-edge-as-gateway.md) Cet article résume l’implémentation du modèle de passerelle transparente. Dans ce modèle, la passerelle transmet les messages de l’appareil en aval au point de terminaison IoT Hub dans votre application IoT Central.

Cet article utilise des machines virtuelles pour héberger l’appareil en aval et la passerelle. Dans un scénario réel, l’appareil en aval et la passerelle s’exécutent sur des appareils physiques de votre réseau local.

## <a name="prerequisites"></a>Prérequis

Pour accomplir les étapes décrites dans cet article, vous avez besoin de ce qui suit :

- Un abonnement Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- [Application IoT Central créée](howto-create-iot-central-application.md) à partir du modèle **Application personnalisée**. Pour plus d’informations, consultez [Créer une application IoT Central](howto-create-iot-central-application.md).

Pour suivre les étapes de cet article, téléchargez les fichiers suivants sur votre ordinateur :

- [Modèle d’appareil à thermostat](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [Manifeste de la passerelle transparente](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>Ajouter des modèles d’appareil

Les appareils en aval et l’appareil de passerelle requièrent des modèles d’appareil dans IoT Central. IoT Central vous permet de modéliser la relation entre vos appareils en aval et votre passerelle afin de les afficher et de les gérer une fois qu’ils sont connectés.

Pour créer un modèle d’appareil pour un appareil en aval, créez un modèle d’appareil standard qui modélise les fonctionnalités de votre appareil. L’exemple présenté dans cet article utilise le modèle d’appareil avec thermostat.

Pour créer un modèle d’appareil pour un appareil en aval :

1. Créez un modèle d’appareil et choisissez **Appareil IoT** comme type de modèle.

1. Dans la page **Personnaliser** de l’assistant, entrez un nom tel que *Thermostat* pour le modèle d’appareil.

1. Après avoir créé le modèle d’appareil, sélectionnez **Importer un modèle**. Sélectionnez un modèle tel que le fichier *thermostat-1.json* que vous avez téléchargé précédemment.

1. Pour générer des vues par défaut pour le thermostat, sélectionnez des vues, puis choisissez **Générer des vues par défaut**.

1. Publier le modèle d’appareil.

Pour créer un modèle d’appareil pour une passerelle IoT Edge transparente :

1. Créez un modèle d’appareil et choisissez **Azure IoT Edge** comme type de modèle.

1. Dans la page **Personnaliser** de l’assistant, entrez un nom tel que *Edge Gateway* pour le modèle d’appareil.

1. Sur la page **Personnaliser** de l’assistant, cochez la case **Appareil de passerelle avec des appareils en aval**.

1. Sur la page **Personnaliser** de l’assistant, sélectionnez **Parcourir**. Téléchargez le fichier *EdgeTransparentGatewayManifest.json* que vous avez téléchargé précédemment.

1. Ajoutez une entrée dans la section **Relations** du modèle d’appareil en aval.

La capture d’écran suivante montre la page **Relations** d’un appareil de passerelle IoT Edge comportant des appareils en aval qui utilisent le modèle d’appareil **Thermostat** :

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="Capture d’écran montrant une relation de modèle d’appareil de passerelle IoT Edge avec un modèle d’appareil à thermostat en aval.":::

La capture d’écran précédente montre un modèle d’appareil de passerelle IoT Edge sans aucun module défini. Une passerelle transparente ne nécessite aucun module, car le runtime IoT Edge transfère les messages des appareils en aval vers IoT Central. Si la passerelle elle-même doit envoyer des données de télémétrie, synchroniser des propriétés, ou gérer des commandes, vous pouvez définir ces fonctionnalités dans le composant par défaut ou dans un module.

Ajoutez les propriétés de cloud et les vues requises avant de publier la passerelle et les modèles d’appareil en aval.

## <a name="add-the-devices"></a>Ajouter les appareils

Lorsque vous ajoutez les appareils à votre application IoT Central, vous pouvez définir la relation entre les appareils en aval et la passerelle transparente.

Pour ajouter les appareils :

1. Accédez à la page des appareils de votre application IoT Central.

1. Ajoutez une instance de l’appareil de passerelle transparente IoT Edge. Dans cet article, l’ID d’appareil de passerelle est `edgegateway`.

1. Ajoutez une ou plusieurs instances de l’appareil en aval. Dans cet article, les appareils en aval sont des thermostats avec les ID `thermostat1` et `thermostat2`.

1. Dans la liste des appareils, choisissez chaque appareil en aval, puis sélectionnez **Attacher à la passerelle**.

La capture d’écran suivante montre comment afficher la liste des appareils attachés à une passerelle sur la page **Appareils en aval** :

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="Capture d’écran montrant la liste des appareils en aval connectés à une passerelle transparente.":::

Dans une passerelle transparente, les appareils en aval se connectent à la passerelle elle-même et non à un module personnalisé hébergé par la passerelle.

Avant de déployer les appareils, vous avez besoin des éléments suivants :

- **Étendue de l’ID** de votre application IoT Central.
- Valeurs d'**ID d’appareil** pour la passerelle et les appareils en aval.
- Valeurs de **clé primaire** pour la passerelle et les appareils en aval.

Pour rechercher ces valeurs, accédez à chaque appareil dans la liste des appareils, puis sélectionnez **Se connecter**. Prenez note de ces valeurs avant de continuer.

## <a name="deploy-the-gateway-and-devices"></a>Déployer la passerelle et les appareils

Pour vous permettre de tester ce scénario, les étapes suivantes vous montrent comment déployer la passerelle et les appareils en aval sur des machines virtuelles Azure. Dans un scénario réel, l’appareil en aval et la passerelle s’exécutent sur des appareils physiques de votre réseau local.

Pour tester le scénario de passerelle transparente, sélectionnez le bouton suivant pour déployer deux machines virtuelles Linux. Une machine virtuelle est une passerelle IoT Edge transparente, tandis que l’autre est un appareil en aval qui simule un thermostat :

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Lorsque les deux machines virtuelles sont déployées et en cours d’exécution, vérifiez que l’appareil de passerelle IoT Edge est en cours d’exécution sur la machine virtuelle `edgegateway` :

1. Accédez à la page **Appareils** dans votre application IoT Central. Si l’appareil de passerelle IoT Edge est connecté à IoT Central, son état indique **Provisioned** (Approvisionné).

1. Ouvrez l’appareil de passerelle IoT Edge et vérifiez l’état des modules sur la page **Modules**. Si le runtime IoT Edge a démarré avec succès, l’état des modules **$edgeAgent** et **$edgeHub** indique **Running** :

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="Capture d’écran montrant les modules $edgeAgent et $edgeHub en cours d’exécution sur la passerelle IoT Edge.":::

> [!TIP]
> Vous devrez peut-être patienter quelques minutes pendant le démarrage de la machine virtuelle et l’approvisionnement de l’appareil dans votre application IoT Central.

## <a name="configure-the-gateway"></a>Configurer la passerelle

Pour que votre appareil IoT Edge fonctionne comme une passerelle transparente, il a besoin de certificats pour prouver son identité à tous les appareils en aval. Cet article utilise des certificats de démonstration. Dans un environnement de production, utilisez des certificats fournis par votre autorité de certification.

Pour générer les certificats de démonstration et les installer sur votre appareil de passerelle :

1. Utilisez SSH pour vous connecter à la machine virtuelle de votre appareil de passerelle.

1. Exécutez les commandes suivantes pour cloner le dépôt IoT Edge et générer vos certificats de démonstration :

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Après avoir exécuté les commandes précédentes, les fichiers ci-dessous sont prêts à être utilisés dans les étapes suivantes :

    - *~/certs/certs/azure-iot-test-only.root.ca.cert.pem* - Le certificat d’autorité de certification racine utilisé pour créer tous les autres certificats de démonstration en vue de tester un scénario IoT Edge.
    - *~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem* - Un certificat d’autorité de certification d’appareil, référencé à l’aide du fichier *config.yaml*. Dans un scénario de passerelle, ce certificat de l'autorité de certification précise comment l'appareil IoT Edge vérifie son identité auprès des appareils en aval.
    - *~/certs/private/iot-edge-device-mycacert.key.pem* - La clé privée associée au certificat de l’autorité de certification de l’appareil.

    Pour en savoir plus sur ces certificats de démonstration, consultez [Créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](../../iot-edge/how-to-create-test-certificates.md).

1. Ouvrez le fichier *config.yaml* dans un éditeur de texte. Par exemple :

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Recherchez les paramètres `Certificate settings`. Supprimez les marques de commentaire et modifiez les paramètres du certificat comme suit :

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    L’exemple ci-dessus suppose que vous êtes connecté en tant que **AzureUser** et que vous avez créé un certificat d’autorité de certification d’appareil appelé « mycacert ».

1. Enregistrez les modifications, puis redémarrez le runtime IoT Edge :

    ```bash
    sudo systemctl restart iotedge
    ```

Si le runtime IoT Edge démarre correctement après vos modifications, l’état des modules **$edgeAgent** et **$EdgeHub** passe à **Running** (en cours d’exécution) sur la page **Modules** de votre appareil de passerelle dans IoT Central.

Si le runtime ne démarre pas, vérifiez les modifications que vous avez apportées dans le fichier *config.yaml*, puis consultez [Résoudre les problèmes de votre appareil IoT Edge](../../iot-edge/troubleshoot.md).

Votre passerelle transparente est maintenant configurée et prête à démarrer le transfert de données de télémétrie à partir des appareils en aval.

## <a name="provision-a-downstream-device"></a>Approvisionner un appareil en aval

Actuellement, IoT Edge ne permet pas d’approvisionner automatiquement un appareil en aval dans votre application IoT Central. Les étapes suivantes vous montrent comment approvisionner l’appareil `thermostat1`. Pour effectuer cette procédure, vous avez besoin d’un environnement Python 3.6 (ou version ultérieure) installé et d’une connexion Internet. Python 3.7 est préinstallé sur [Azure Cloud Shell](https://shell.azure.com/) :

1. Exécutez la commande suivante pour installer le module `azure.iot.device` :

    ```bash
    pip install azure.iot.device
    ```

1. Exécutez la commande suivante pour télécharger le script Python qui effectue l’approvisionnement :

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. Pour approvisionner l'appareil en aval `thermostat1` dans votre application IoT Central, exécutez les commandes suivantes, en remplaçant `{your application id scope}` et `{your device primary key}` :

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

Dans votre application IoT Central, vérifiez que l'**état** de l’appareil thermostat1 indique maintenant **Provisioned** (Approvisionné). 

## <a name="configure-a-downstream-device"></a>Configurer un appareil en aval

Dans la section précédente, vous avez configuré la machine virtuelle `edgegateway` avec les certificats de démonstration pour lui permettre de s’exécuter en tant que passerelle. La machine virtuelle `leafdevice` est prête à installer un simulateur de thermostat qui utilise la passerelle pour se connecter à IoT Central.

La machine virtuelle `leafdevice` a besoin d’une copie du certificat d’autorité de certification racine que vous avez créé sur la machine virtuelle `edgegateway`. Copiez le fichier */home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem* de la machine virtuelle `edgegateway` vers votre répertoire de base sur la machine virtuelle `leafdevice`. Vous pouvez utiliser la commande **scp** pour copier des fichiers vers et depuis une machine virtuelle Linux.

Pour savoir comment vérifier la connexion entre l’appareil en aval et la passerelle, consultez [Test de connexion à la passerelle](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection).

Pour exécuter le simulateur de thermostat sur la machine virtuelle `leafdevice` :

1. Téléchargez l’exemple Python dans votre répertoire de base :

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. Installez le module Python de l’appareil Azure IoT :

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. Définissez les variables d’environnement pour configurer l’exemple. Remplacez `{your device shared key}` par la clé primaire de la valeur `thermostat1` que vous avez notée précédemment. Ces variables supposent que le nom de la machine virtuelle de passerelle est `edgegateway`, et que l’ID de l’appareil de thermostat est `thermostat1` :

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    Notez que la chaîne de connexion utilise le nom de l’appareil de passerelle et non celui d’un IoT Hub.

1. Pour exécuter le code, utilisez la commande suivante :

    ```bash
    python3 simple_thermostat.py
    ```

    La sortie de cette commande ressemble à ceci :

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. Pour afficher les données de télémétrie dans IoT Central, accédez à la page **Vue d’ensemble** de l’appareil **thermostat1** :

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="Capture d’écran montrant les données de télémétrie de l’appareil en aval.":::

    La page **À propos de** affiche les valeurs de propriétés envoyées à partir de l’appareil en aval, et la page **Commande** vous permet d’appeler des commandes sur l’appareil en aval.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à configurer une passerelle transparente avec IoT Central, l’étape suivante vous en apprendra plus sur [IoT Edge](../../iot-edge/about-iot-edge.md).
