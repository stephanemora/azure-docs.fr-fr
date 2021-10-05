---
title: fichier descriptif
description: Fichier include
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 08/03/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 6f8cf902303dec652c8b1217815ccd9698e2f21e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610238"
---
[![Parcourir le code](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples)

Dans ce guide de démarrage rapide, vous allez découvrir un workflow simple de développement d’application Azure IoT. Vous utilisez l’interface Azure CLI pour créer un hub Azure IoT et un appareil. Ensuite, vous utilisez un exemple Azure IoT device SDK pour exécuter un contrôleur de température simulé, le connecter en toute sécurité au hub et envoyer des données de télémétrie.

## <a name="prerequisites"></a>Prérequis
- Si vous n’avez pas d’abonnement Azure, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- [Visual Studio (Community, Professional ou Enterprise) 2019](https://visualstudio.microsoft.com/downloads/).
- Une copie locale du dépôt GitHub [Microsoft Azure IoT Samples for C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp). Téléchargez une copie du dépôt et extrayez-la : [Télécharger le fichier .zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip).
- [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) : utilitaire multiplateforme basé sur l’interface graphique utilisateur permettant de surveiller et gérer Azure IoT. 
- Azure CLI. Vous avez le choix entre deux options pour exécuter les commandes Azure CLI dans ce guide de démarrage rapide :
    - Utilisez Azure Cloud Shell, un interpréteur de commandes interactif qui exécute des commandes CLI dans votre navigateur. Cette option est recommandée, car vous n’avez pas besoin d’installer quoi que ce soit. Si vous utilisez Cloud Shell pour la première fois, connectez-vous au [portail Azure](https://portal.azure.com). Suivez les étapes décrites dans [Démarrage rapide de Cloud Shell](../articles/cloud-shell/quickstart.md) pour **démarrer Cloud Shell** et **sélectionner l’environnement Bash**.
    - Si vous le souhaitez, exécutez Azure CLI sur votre ordinateur local. Si l’interface de ligne de commande Azure est déjà installée, exécutez `az upgrade` pour la mettre à niveau, ainsi que les extensions, vers la version actuelle. Pour installer l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure]( /cli/azure/install-azure-cli).

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-a-simulated-device"></a>Exécuter un appareil simulé
Dans cette section, vous allez utiliser le kit SDK C# pour envoyer des messages d’un appareil simulé à votre hub IoT. Vous allez exécuter un exemple qui implémente un contrôleur de température avec deux capteurs de thermostat.

Pour exécuter l’exemple d’application dans Visual Studio :

1. Dans le dossier où vous avez décompressé les exemples Azure IoT pour C#, ouvrez le fichier solution *azure-iot-samples-csharp-master\iot-hub\Samples\device\IoTHubDeviceSamples.sln"* dans Visual Studio. 

1. Dans **Explorateur de solutions**, sélectionnez le fichier de projet **PnpDeviceSamples > TemperatureController**, cliquez dessus avec le bouton droit et sélectionnez **Définir comme projet de démarrage**.

1. Cliquez avec le bouton droit sur le projet **TemperatureController**, sélectionnez **Propriétés**, sélectionnez l’onglet **Déboguer**, puis ajoutez les variables d’environnement suivantes au projet :

    | Nom | Valeur |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | *connectionString* |
    | IOTHUB_DEVICE_CONNECTION_STRING | Chaîne de connexion enregistrée précédemment. |

1. Enregistrez le fichier de projet **TemperatureController** mis à jour.

1. Dans Visual Studio, appuyez sur Ctrl+F5 pour exécuter l’exemple.

Une fenêtre de console s'ouvre. L’échantillon se connecte en toute sécurité à votre hub IoT en tant qu’appareil que vous avez inscrit et commence à envoyer des messages de télémétrie. L’exemple de sortie s’affiche dans la console.

## <a name="view-telemetry"></a>Afficher les données de télémétrie

Vous pouvez afficher la télémétrie des appareils avec l’Explorateur IoT. Si vous le souhaitez, vous pouvez afficher les données de télémétrie à l’aide d’Azure CLI.

Pour afficher la télémétrie dans Azure IoT Explorer :

1. À partir de votre hub IoT dans IoT Explorer, sélectionnez **Voir les appareils dans ce hub**, puis sélectionnez votre appareil dans la liste. 
1. Dans le menu de gauche pour votre appareil, sélectionnez **Télémétrie**.
1. Confirmez que **Utiliser un Event Hub intégré** est défini sur *Oui*, puis sélectionnez **Démarrer**.
1. Affichez la télémétrie à mesure que l’appareil envoie des messages au cloud.

    :::image type="content" source="media/iot-develop-send-telemetry-iot-hub-csharp/iot-explorer-device-telemetry.png" alt-text="Capture d’écran de télémétrie d’appareil dans IoT Explorer":::

1. Sélectionnez **Arrêter** pour cesser de recevoir des événements.

Pour lire les données de télémétrie envoyées par les composants d’appareils individuels, vous pouvez utiliser les fonctionnalités Plug-and-Play dans l’Explorateur IoT. Par exemple, le contrôleur de température dans ce démarrage rapide a deux thermostats : thermostat1 et thermostat2. Pour afficher la température signalée par thermostat1 : 

1. Sur votre appareil dans l’Explorateur IoT, sélectionnez **Composants IoT Plug-and-Play** dans le menu de gauche. Ensuite, sélectionnez **thermostat1** dans la liste des composants.

1. Sur le volet du composant **thermostat1**, sélectionnez **Télémétrie** dans le menu supérieur.

1. Sur le volet **Télémétrie**, suivez les mêmes étapes que précédemment. Vérifiez que **Utiliser un Event Hub intégré** est défini sur *Oui*, puis sélectionnez **Démarrer**.

Pour afficher la télémétrie de l’appareil avec Azure CLI :

1. Exécutez la commande [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) pour surveiller les événements envoyés par l’appareil simulé à votre hub IoT. Utilisez les noms que vous avez créés précédemment dans Azure IoT pour votre appareil et le hub IoT.

    ```azurecli
    az iot hub monitor-events --output table --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. Visualisez les détails de la connexion et la sortie de la télémétrie dans la console.

    ```output
    Starting event monitor, filtering on device: mydevice, use ctrl-c to stop...
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 39.8
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 36.7
    ```

1. Sélectionnez CTRL+C pour cesser la surveillance.
