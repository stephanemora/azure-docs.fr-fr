---
title: Fichier include
description: Fichier include
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/05/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 81ab51ccf589aadb6f79891811bf512baa6fadda
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112040030"
---
## <a name="prerequisites"></a>Prérequis
- Si vous n’avez pas d’abonnement Azure, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- [Visual Studio (Community, Professional ou Enterprise) 2019](https://visualstudio.microsoft.com/downloads/).
- Une copie locale du dépôt GitHub [Microsoft Azure IoT Samples for C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp). Téléchargez une copie du dépôt et extrayez-la : [Télécharger le fichier .zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip).
- Azure CLI. Vous avez le choix entre deux options pour exécuter les commandes Azure CLI dans ce guide de démarrage rapide :
    - Utilisez Azure Cloud Shell, un interpréteur de commandes interactif qui exécute des commandes CLI dans votre navigateur. Cette option est recommandée, car vous n’avez pas besoin d’installer quoi que ce soit. Si vous utilisez Cloud Shell pour la première fois, connectez-vous au [portail Azure](https://portal.azure.com). Suivez les étapes décrites dans [Démarrage rapide de Cloud Shell](../articles/cloud-shell/quickstart.md) pour **démarrer Cloud Shell** et **sélectionner l’environnement Bash**.
    - Si vous le souhaitez, exécutez Azure CLI sur votre ordinateur local. Si l’interface de ligne de commande Azure est déjà installée, exécutez `az upgrade` pour la mettre à niveau, ainsi que les extensions, vers la version actuelle. Pour installer l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure]( /cli/azure/install-azure-cli).

[!INCLUDE [iot-hub-include-create-hub-cli](iot-hub-include-create-hub-cli.md)]

## <a name="run-a-simulated-device"></a>Exécuter un appareil simulé
Dans cette section, vous allez configurer votre environnement local et exécuter un exemple qui crée un contrôleur de température simulé.

Pour exécuter l’exemple d’application dans Visual Studio :

1. Dans le dossier où vous avez décompressé les exemples Azure IoT pour C#, ouvrez le fichier solution *azure-iot-samples-csharp-master\iot-hub\Samples\device\IoTHubDeviceSamples.sln"* dans Visual Studio. 

1. Dans **Explorateur de solutions**, sélectionnez le fichier de projet **PnpDeviceSamples > TemperatureController**, cliquez dessus avec le bouton droit et sélectionnez **Définir comme projet de démarrage**.

1. Cliquez avec le bouton droit sur le projet **TemperatureController**, sélectionnez **Propriétés**, sélectionnez l’onglet **Déboguer**, puis ajoutez les variables d’environnement suivantes au projet :

    | Nom | Valeur |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | *connectionString* |
    | IOTHUB_DEVICE_CONNECTION_STRING | Chaîne de connexion enregistrée précédemment. |

1. Enregistrez le fichier de projet **TemperatureController** mis à jour.

1. Dans votre application CLI, exécutez la commande [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) pour commencer à surveiller les événements sur votre appareil IoT simulé.  Les messages d’événement sont imprimés dans le terminal au fur et à mesure de leur arrivée.

    ```azurecli-interactive
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. Dans Visual Studio, appuyez sur Ctrl+F5 pour exécuter l’exemple.

    Une fois que votre appareil simulé se connecte à votre application IoT Central, il commence à envoyer des données de télémétrie. Les détails de connexion et la sortie des données de télémétrie apparaissent dans la console : 
    
    ```output
    Starting event monitor, use ctrl-c to stop...
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: myDevice
      payload:
        temperature: 39.8
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: myDevice
      payload:
        temperature: 36.7
    ```