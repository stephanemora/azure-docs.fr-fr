---
title: Ajouter un appareil Azure IoT Edge à Azure IoT Central | Microsoft Docs
description: Ajouter un appareil Azure IoT Edge à votre application Azure IoT Central en qualité d’opérateur
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c60cf4b90b089d271c0ccd91031420efe9017b1e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758156"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Tutoriel : Ajouter un appareil Azure IoT Edge à votre application Azure IoT Central

*Cet article s’applique aux créateurs de solutions et aux développeurs d’appareils.*

Ce tutoriel vous montre comment configurer et ajouter un appareil Azure IoT Edge à votre application Azure IoT Central. Ce tutoriel utilise une machine virtuelle Linux activée pour IoT Edge provenant de la Place de marché Azure pour simuler un appareil IoT Edge. L’appareil IoT Edge utilise un module qui génère des données de télémétrie environnementales simulées. Vous visualisez la télémétrie sur un tableau de bord dans votre application Azure IoT Central.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un modèle d’appareil pour un appareil IoT Edge
> * Créer un appareil IoT Edge dans IoT Central
> * Déployer un appareil IoT Edge simulé sur une machine virtuelle Linux

## <a name="prerequisites"></a>Prérequis

Suivez le guide de démarrage rapide [Créer une application Azure IoT Central](./quick-deploy-iot-central.md) pour créer une application IoT Central avec le modèle **Application personnalisée > Application personnalisée**.

Pour effectuer les étapes de ce tutoriel, vous avez besoin d’un compte Azure actif.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Téléchargez le fichier manifeste IoT Edge à partir de GitHub. Cliquez avec le bouton droit sur le lien, puis sélectionnez **Enregistrer le lien sous** : [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Créer un modèle d’appareil

Dans cette section, vous créez un modèle d’appareil pour un appareil IoT Edge qui se connecte à votre application IoT Central. Pour commencer, vous importez un manifeste IoT Edge, puis vous modifiez le modèle pour ajouter des définitions et des vues de télémétrie :

### <a name="import-manifest-to-create-template"></a>Importer le manifeste pour créer un modèle

Pour créer un modèle d’appareil à partir d’un manifeste IoT Edge :

1. Dans votre application Azure IoT Central, accédez à **Modèles d’appareil**, puis sélectionnez **+ Nouveau**.

1. Dans la page **Sélectionner un type de modèle**, sélectionnez la vignette **Azure IoT Edge**. Ensuite, sélectionnez **Next: Personnaliser**.

1. Dans la page **Charger un manifeste de déploiement Azure IoT Edge**, sélectionnez **Parcourir** pour charger le manifeste **EnvironmentalSensorManifest.json** que vous avez téléchargé précédemment. Ensuite, sélectionnez **Next: Vérification**).

1. Dans la page **Vérifier**, sélectionnez **Créer**.

1. Une fois le modèle créé, changez son nom en *Environmental Sensor Edge Device* (Appareil de périphérie de capteur d’environnement).

1. Sélectionnez l’interface **Gérer** dans le module **SimulatedTemperatureSensor** pour voir les deux propriétés définies dans le manifeste :

![Modèle d’appareil créé à partir du manifeste IoT Edge](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>Ajouter de la télémétrie au manifeste

Un manifeste IoT Edge ne définit pas la télémétrie envoyée par un module. Vous devez ajouter les définitions de télémétrie au modèle d’appareil. Le module **SimulatedTemperatureSensor** envoie des messages de télémétrie qui se présentent comme le code JSON suivant :

```json
{
    "machine": {
        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

Pour ajouter les définitions de télémétrie au modèle d’appareil :

1. Sélectionnez l’interface **Gérer** dans le modèle **Environmental Sensor Edge Device**.

1. Sélectionnez **+ Ajouter une capacité**. Entrez *machine* comme **Nom d’affichage** et vérifiez que le **Type de capacité** est **Télémétrie**.

1. Sélectionnez **Objet** comme type de schéma, puis sélectionnez **Définir**. Dans la page de définition de l’objet, ajoutez *temperature* (température) et *pressure* (pression) comme attributs de type **Double**, puis sélectionnez **Appliquer**.

1. Sélectionnez **+ Ajouter une capacité**. Entrez *ambient* (ambiant) comme **Nom d’affichage** et vérifiez que le **Type de capacité** est **Télémétrie**.

1. Sélectionnez **Objet** comme type de schéma, puis sélectionnez **Définir**. Dans la page de définition de l’objet, ajoutez *temperature* (température) et *humidity* (humidité) comme attributs de type **Double**, puis sélectionnez **Appliquer**.

1. Sélectionnez **+ Ajouter une capacité**. Entrez *timeCreated* comme **Nom d’affichage** et vérifiez que le **Type de capacité** est **Télémétrie**.

1. Sélectionnez **DateTime** comme type de schéma.

1. Sélectionnez **Enregistrer** pour mettre à jour le modèle.

L’interface **Gérer** comprend désormais les types de télémétrie **machine**, **ambient** et **timeCreated** :

![Interface avec les types de télémétrie machine et ambient](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>Ajouter des vues au modèle

Le modèle d’appareil n’a pas encore de vue pour permettre à un opérateur de voir la télémétrie provenant de l’appareil IoT Edge. Pour ajouter une vue au modèle d’appareil :

1. Sélectionnez l’interface **Vues** dans le modèle **Environmental Sensor Edge Device**.

1. Dans la page **Sélectionner pour ajouter une nouvelle vue**, sélectionnez la vignette **Visualisation de l’appareil**.

1. Changez le nom de la vue en *View IoT Edge device telemetry* (Visualiser la télémétrie de l’appareil IoT Edge).

1. Sélectionnez les types de télémétrie **ambient** et **machine**. Sélectionnez ensuite **Ajouter une vignette**.

1. Sélectionnez **Enregistrer** pour enregistrer la vue **View IoT Edge device telemetry**.

![Modèle d’appareil avec la vue de télémétrie](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>Publier le modèle

Avant de pouvoir ajouter un appareil qui utilise le modèle **Environmental Sensor Edge Device**, vous devez publier le modèle.

Accédez au modèle **Environmental Sensor Edge Device**, puis sélectionnez **Publier**. Sélectionnez **Publier** pour publier le modèle :

![Publier le modèle d’appareil](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>Ajouter un appareil IoT Edge

Maintenant que vous avez publié le modèle **Environmental Sensor Edge Device**, vous pouvez ajouter un appareil à votre application IoT Central :

1. Dans votre application IoT Central, accédez à la page **Appareils**, puis sélectionnez **Environmental Sensor Edge Device** dans la liste des modèles disponibles.

1. Sélectionnez **+** pour ajouter un nouvel appareil à partir du modèle. Dans la page **Créer un appareil**, sélectionnez **Créer**.

Vous disposez maintenant d’un nouvel appareil avec l’état **Inscrit** :

![Publier le modèle d’appareil](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>Obtenir les informations d’identification de l’appareil

Quand vous déployez l’appareil IoT Edge plus loin dans ce tutoriel, vous avez besoin des informations d’identification qui permettent à l’appareil de se connecter à votre application IoT Central. Pour obtenir les informations d’identification de l’appareil :

1. Dans la page **Appareils**, sélectionnez l’appareil que vous avez créé.

1. Sélectionnez **Connecter**.

1. Dans la page **Connexion de l’appareil**, notez les valeurs de **Étendue de l’ID**, **ID de l’appareil** et **Clé principale**. Vous utiliserez ces valeurs plus tard.

1. Sélectionnez **Fermer**.

Vous avez maintenant terminé la configuration de votre application IoT Central pour permettre à un appareil IoT Edge de se connecter.

## <a name="deploy-an-iot-edge-device"></a>Déployer un appareil IoT Edge

Dans ce tutoriel, vous utilisez une machine virtuelle Linux activée pour Azure IoT Edge créée sur Azure pour simuler un appareil IoT Edge. Pour créer la machine virtuelle activée pour IoT Edge :

1. Accédez à [Azure IoT Edge sur Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) sur la Place de marché Azure. Sélectionnez ensuite **Obtenir maintenant**.

1. Dans la page **Créer cette application dans Azure**, sélectionnez **Continuer**. Ce lien vous amène au portail Azure, où vous devrez peut-être vous connecter à votre abonnement Azure.

1. Dans la page **Azure IoT Edge sur Ubuntu** du portail Azure, sélectionnez **Créer**.

1. Dans la page **Créer une machine virtuelle > De base**:

    - Sélectionnez votre abonnement Azure.
    - Créez un groupe de ressources nommé **iot-edge-devices**.
    - Utilisez le nom de la machine virtuelle : **iotedgevm**.
    - Choisissez la région la plus proche de vous.
    - Définissez le type d’authentification sur **Mot de passe**.
    - Choisissez un nom d’utilisateur et un mot de passe.
    - Vous pouvez conserver les valeurs par défaut des autres options.
    - Sélectionnez **Revoir + créer**.

1. Quand la validation est terminée, sélectionnez **Créer**.

Après quelques minutes, quand le déploiement est terminé, sélectionnez **Accéder à la ressource**.

### <a name="provision-vm-as-an-iot-edge-device"></a>Provisionner la machine virtuelle en tant qu’appareil IoT Edge 

Pour provisionner la machine virtuelle en tant qu’appareil IoT Edge :

1. Dans la section **Support + dépannage**, sélectionnez **Console série**.

1. Appuyez sur **Entrée** pour voir l’invite `login:`. Entrez votre nom d’utilisateur et votre mot de passe pour vous connecter.

1. Exécutez la commande suivante pour vérifier la version du runtime IoT Edge. Au moment de la rédaction de cette documentation, la version est 1.0.8 :

    ```bash
    sudo iotedge --version
    ```

1. Utilisez l’éditeur `nano` pour ouvrir le fichier config.yaml d’IoT Edge :

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Faites défiler jusqu’à faire apparaître `# Manual provisioning configuration`. Placez les trois lignes suivantes en commentaire comme indiqué dans l’extrait de code suivant :

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. Faites défiler jusqu’à faire apparaître `# DPS symmetric key provisioning configuration`. Décommentez les huit lignes suivantes comme indiqué dans l’extrait de code suivant :

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Remplacez `{scope_id}` par l’**Étendue de l’ID** que vous avez noté précédemment.

1. Remplacez `{registration_id}` par l’**ID de l’appareil** que vous avez noté précédemment.

1. Remplacez `{symmetric_key}` par la **Clé principale** que vous avez notée précédemment.

1. Enregistrez les modifications (**Ctrl+O**) et quittez (**Ctrl+X**) l’éditeur `nano`.

1. Exécutez la commande suivante pour redémarrer le démon IoT Edge :

    ```bash
    sudo systemctl restart iotedge
    ```

1. Pour vérifier l’état des modules IoT Edge, exécutez la commande suivante :

    ```bash
    iotedge list
    ```

    La sortie a l'aspect suivant :

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>Afficher les données de télémétrie

L’appareil IoT Edge simulé est maintenant en cours d’exécution sur la machine virtuelle. Dans votre application IoT Central, l’état de l’appareil est maintenant **Provisionné** dans la page **Appareils** :

![Appareil provisionné](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

Vous pouvez voir la télémétrie dans la page **Afficher la télémétrie des appareils IoT Edge** :

![Télémétrie d’appareil](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

La page **Modules** affiche l’état des modules IoT Edge :

![Télémétrie d’appareil](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>Étapes suivantes

En tant que développeur d’appareils, maintenant que vous avez appris à utiliser et gérer les appareils IoT Edge dans IoT Central, nous vous suggérons ensuite de lire :

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Se connecter à Azure IoT Central](./concepts-get-connected.md)
