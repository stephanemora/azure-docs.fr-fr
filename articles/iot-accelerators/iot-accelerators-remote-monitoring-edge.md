---
title: Tutoriel de détection des anomalies en périphérie dans une solution - Azure | Microsoft Docs
description: Dans ce tutoriel, vous allez découvrir comment superviser vos appareils IoT avec l’accélérateur de solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e234cc29589e7b104ea7ef54ab7493e27d11cf27
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460426"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Tutoriel : Détecter des anomalies à la périphérie avec l’accélérateur de solution de supervision à distance

Dans ce tutoriel, vous allez configurer la solution de supervision à distance pour résoudre les anomalies détectées par un appareil IoT Edge. Les appareils IoT Edge vous permettent de traiter la télémétrie à la périphérie afin de réduire le volume des données de télémétrie envoyées à la solution et de répondre plus rapidement aux événements des appareils. Pour connaître les avantages du traitement de données en périphérie, consultez [Qu’est-ce qu’Azure IoT Edge](../iot-edge/about-iot-edge.md).

Pour présenter le traitement des données en périphérie à l’aide de la supervision à distance, ce tutoriel utilise une simulation d’un capteur de pompe à pétrole. La pompe à pétrole est gérée par une organisation appelée Contoso et connectée à l’accélérateur de solution de supervision à distance. Les capteurs de la pompe à pétrole mesurent la température et la pression. Les opérateurs de Contoso savent qu’une augmentation anormale de la température peut entraîner le ralentissement de la pompe. Les opérateurs de Contoso n’ont pas besoin de surveiller la température du dispositif lorsque celle-ci est comprise dans la plage normale.

Contoso souhaite déployer un module de périphérie intelligent qui détecte les anomalies de température sur la pompe à pétrole. Un autre module de périphérie est chargé d’envoyer des alertes à la solution de supervision à distance. Lorsqu’une alerte est reçue, un opérateur Contoso peut la transmettre à un technicien de maintenance. Contoso peut également configurer une action automatisée, comme l’envoi d’un e-mail, qui doit être exécutée lorsque la solution reçoit une alerte.

Le diagramme suivant montre les composants clés dans le scénario du tutoriel :

![Le diagramme illustre la pompe à pétrole connectée au module Stream Analytics IoT Edge dans l’appareil IoT Edge pour les données de télémétrie et commandes. Les données de télémétrie filtrées sont dirigées vers l’appareil IoT Edge de l’accélérateur de solution de supervision à distance dans le cloud. Le cloud contient également le déploiement et le package. Le déploiement déploie le runtime IoT Edge dans l’appareil.](media/iot-accelerators-remote-monitoring-edge/overview.png)

Dans ce tutoriel, vous allez :

>[!div class="checklist"]
> * Ajouter un appareil IoT Edge à la solution
> * Créer un manifeste Edge
> * Importer un manifeste sous la forme d’un package qui définit les modules à exécuter sur l’appareil
> * Déployer le package sur votre appareil IoT Edge
> * Afficher les alertes sur votre appareil

Sur l’appareil IoT Edge :

* Le runtime reçoit le package et installe les modules.
* Le module Stream Analytics détecte les anomalies de température dans la pompe et envoie des commandes pour résoudre le problème.
* Le module Stream Analytics transfère les données filtrées à l’accélérateur de solution.

Ce tutoriel utilise une machine virtuelle Linux comme appareil IoT Edge. Vous installez également un module Edge pour simuler le capteur de pompe à pétrole.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>Ajouter un appareil IoT Edge

Deux étapes sont nécessaires pour ajouter un appareil IoT Edge à votre accélérateur de solution de supervision à distance. Cette section vous montre comment :

* Ajouter un appareil IoT Edge dans la page **Device Explorer** de l’interface utilisateur web de la supervision à distance
* Installer le runtime IoT Edge sur une machine virtuelle Linux

### <a name="add-an-iot-edge-device-to-your-solution"></a>Ajouter un appareil IoT Edge à votre solution

Pour ajouter un appareil IoT Edge à l’accélérateur de solution de supervision à distance, accédez à la page **Device Explorer** dans l’interface utilisateur web, puis cliquez sur **+ Nouvel appareil**.

Dans le panneau **Nouvel appareil**, choisissez **Appareil IoT Edge**, puis entrez **oil-pump** (pompe à pétrole) comme ID d’appareil. Vous pouvez conserver les valeurs par défaut des autres paramètres. Cliquez alors sur **Appliquer** :

[![Ajouter un appareil IoT Edge](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Notez la chaîne de connexion d’appareil, car vous en aurez besoin dans la section suivante de ce tutoriel.

Quand vous inscrivez un appareil auprès du hub IoT dans l’accélérateur de solution de supervision à distance, l’appareil est listé dans la page **Device Explorer** de l’interface utilisateur web :

[![Nouvel appareil IoT Edge](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Pour faciliter la gestion des appareils IoT Edge dans la solution, créez un groupe d’appareils, puis ajoutez-y l’appareil IoT Edge :

1. Sélectionnez l’appareil **oil-pump** dans la liste de la page **Device Explorer**, puis cliquez sur **Travaux**.

1. Créez un travail pour ajouter l’étiquette **IsEdge** à l’appareil en utilisant les paramètres suivants :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Travail     | Étiquettes  |
    | Nom du travail | AddEdgeTag |
    | Clé     | IsOilPump |
    | Valeur   | O     |
    | Type    | Texte  |

    [![Ajouter une étiquette](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Cliquez sur **Appliquer**, puis sur **Fermer**.

1. Dans la page **Device Explorer**, cliquez sur **Gérer les groupes d’appareils**.

1. Cliquez sur **Créer un groupe d’appareils**. Créez un groupe d’appareils avec les paramètres suivants :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom    | OilPumps |
    | Champ   | Tags.IsOilPump |
    | Opérateur | = Equals |
    | Valeur    | O |
    | Type     | Texte |

    [![Créer un groupe d’appareils](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Cliquez sur **Enregistrer**.

Votre appareil IoT Edge se trouve maintenant dans le groupe **OilPumps**.

### <a name="install-the-edge-runtime"></a>Installer le runtime Edge

Les appareils de périphérie nécessitent l’installation du runtime Edge. Dans ce tutoriel, vous allez installer le runtime Edge sur une machine virtuelle Linux Azure pour tester le scénario. Les étapes suivantes utilisent Azure Cloud Shell pour installer et configurer la machine virtuelle :

1. Pour créer une machine virtuelle Linux dans Azure, exécutez les commandes suivantes. Vous pouvez utiliser un emplacement proche de vous :

    ```azurecli-interactive
    az group create \
      --name IoTEdgeDevices \
      --location eastus
    az vm create \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest \
      --admin-username azureuser \
      --generate-ssh-keys \
      --size Standard_B1ms
    ```

1. Pour configurer le runtime Edge avec la chaîne de connexion de l’appareil, exécutez la commande suivante à l’aide de la chaîne de connexion de l’appareil notée précédemment :

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Veillez à placer votre chaîne de connexion entre guillemets doubles.

Vous venez d’installer et de configurer le runtime IoT Edge sur un appareil Linux. Plus loin dans ce tutoriel, vous utiliserez la solution de supervision à distance pour déployer des modules IoT Edge sur cet appareil.

## <a name="create-an-edge-manifest"></a>Créer un manifeste Edge

Pour simuler le capteur de la pompe à pétrole, vous devez ajouter les modules suivants sur votre appareil de périphérie :

* Module de simulation de la température
* Détection des anomalies dans Azure Stream Analytics

Les étapes suivantes vous montrent comment créer un manifeste de déploiement Edge comprenant ces modules. Plus loin dans ce tutoriel, vous importerez ce manifeste en tant que package dans l’accélérateur de solution de supervision à distance.

### <a name="create-the-azure-stream-analytics-job"></a>Créer le travail Azure Stream Analytics

Définissez le travail Stream Analytics dans le portail avant de l’empaqueter sous forme de module Edge.

1. Dans le portail Azure, créez un compte de stockage Azure en utilisant les options par défaut du groupe de ressources **IoTEdgeDevices**. Notez le nom que vous avez choisi.

1. Dans le portail Azure, créez un **travail Stream Analytics** sous le groupe de ressources **IoTEdgeDevices**. Utilisez les valeurs de configuration suivantes :

    | Option | Valeur |
    | ------ | ----- |
    | Nom du travail | EdgeDeviceJob |
    | Abonnement | Votre abonnement Azure |
    | Resource group | IoTEdgeDevices |
    | Emplacement | USA Est |
    | Environnement d’hébergement | Edge |
    | Unités de diffusion en continu | 1 |

1. Ouvrez le travail Stream Analytics **EdgeDeviceJob** dans le portail, cliquez sur Entrées, puis ajoutez une entrée de flux **Edge Hub** appelée **telemetry**.

1. Sur le portail, dans le travail Stream Analytics **EdgeDeviceJob**, cliquez sur **Sorties**, puis ajoutez une sortie **Edge Hub** appelée **output** (sortie).

1. Sur le portail, dans le travail Stream Analytics **EdgeDeviceJob**, cliquez sur **Sorties**, puis ajoutez une deuxième sortie **Edge Hub** appelée **alert**.

1. Sur le portail, dans le travail Stream Analytics **EdgeDeviceJob**, cliquez sur **Requête**, puis ajoutez l’instruction **select** suivante :

    ```sql
    SELECT  
    avg(machine.temperature) as temperature, 
    'F' as temperatureUnit 
    INTO output 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 5) 
    SELECT 'reset' as command 
    INTO alert 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 3) 
    HAVING avg(machine.temperature) > 400
    ```

1. Sur le portail, dans le travail Stream Analytics **EdgeDeviceJob**, cliquez sur **Paramètres du compte de stockage**. Ajoutez le compte de stockage que vous avez ajouté au groupe de ressources **IoTEdgeDevices** au début de cette section. Créez un conteneur appelé **edgeconfig**.

La capture d’écran suivante montre le travail Stream Analytics après enregistrement :

[![Travail Stream Analytics](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

Vous venez de définir un travail Stream Analytics devant être exécuté sur votre appareil de périphérie. Le travail calcule la température moyenne dans une fenêtre de 5 secondes. Elle envoie également une alerte si la température moyenne dans une fenêtre de 3 secondes dépasse 400.

### <a name="create-the-iot-edge-deployment"></a>Créer le déploiement IoT Edge

Ensuite, créez un manifeste de déploiement IoT Edge qui définit les modules à exécuter sur votre appareil de périphérie. Dans la section suivante, vous importerez ce manifeste en tant que package dans la solution de supervision à distance.

1. Dans le portail Azure, naviguez jusqu’au hub IoT du groupe de ressources qui contient votre solution de supervision à distance. Vous y trouverez le hub IoT du groupe de ressources qui porte le même nom que votre solution de supervision à distance.

1. Dans le hub IoT, cliquez sur **IoT Edge** dans la section **Gestion automatique des appareils**. Cliquez sur **Ajouter un déploiement IoT Edge**.

1. Dans la page **Créer un déploiement > Nom et étiquette**, entrez le nom **oil-pump-device**. Cliquez sur **Suivant**.

1. Dans la page **Créer un déploiement > Ajouter des modules**, cliquez sur **+Ajouter**. Choisissez **Module IoT Edge**.

1. Dans le panneau **Modules personnalisés IoT Edge**, entrez **temperatureSensor** comme nom et **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64** comme URI de l’image. Cliquez sur **Enregistrer**.

1. Dans la page **Créer un déploiement > Ajouter des modules**, cliquez sur **+Ajouter** pour ajouter un deuxième module. Choisissez **Module Azure Stream Analytics**.

1. Dans le panneau **Déploiement Edge**, sélectionnez votre abonnement et le travail **EdgeDeviceJob** que vous avez créé dans la section précédente. Cliquez sur **Enregistrer**.

1. Dans la page **Créer un déploiement > Ajouter des modules**, cliquez sur **Suivant**.

1. Dans la page **Créer un déploiement > Spécifier des routes**, ajoutez le code suivant :

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    Ce code achemine la sortie du module Stream Analytics vers les emplacements appropriés.

    Cliquez sur **Suivant**.

1. Dans la page **Créer un déploiement > Spécifier les métriques**, cliquez sur **Suivant**.

1. Dans la page **Créer un déploiement > Appareils cibles**, entrez 10 comme valeur de priorité. Cliquez sur **Suivant**.

1. Dans la page **Créer un déploiement > Passer en revue le déploiement**, cliquez sur **Envoyer** :

    [![Vérifier le déploiement](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. Dans la page **IoT Edge** principale, cliquez sur **Déploiements IoT Edge**. **oil-pump-device** figure dans la liste des déploiements.

1. Cliquez sur le déploiement **oil-pump-device**, puis cliquez sur **Télécharger le manifeste IoT Edge**. Enregistrez le fichier sous **oil-pump-device.json** dans un emplacement approprié de votre ordinateur local. Vous aurez besoin de ce fichier dans la section suivante de ce tutoriel.

Vous venez de créer un manifeste IoT Edge devant être importé dans la solution de supervision à distance en tant que package. En règle générale, le développeur crée les modules et le fichier manifeste IoT Edge.

## <a name="import-a-package"></a>Importer un package

Dans cette section, vous importerez ce manifeste Edge en tant que package dans la solution de supervision à distance.

1. Dans l’interface utilisateur web de la supervision à distance, accédez à la page **Packages**, puis cliquez sur **+Nouveau package** :

    [![Nouveau package](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. Dans le panneau **Nouveau package**, choisissez **Manifeste Edge** comme type de package, cliquez sur **Parcourir** pour trouver le fichier **oil-pump-device.json** sur votre ordinateur local, puis cliquez sur **Charger** :

    [![Charger le package](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    La liste de packages inclut désormais le package **oil-pump-device.json**.

Dans la section suivante, vous allez créer un déploiement qui applique le package à votre appareil de périphérie.

## <a name="deploy-a-package"></a>Déployer un package

Vous êtes maintenant prêt à déployer le package sur votre appareil.

1. Dans l’interface utilisateur web de la supervision à distance, accédez à la page **Déploiements**, puis cliquez sur **+Nouveau déploiement** :

    [![Nouveau déploiement](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. Dans le panneau **Nouveau déploiement**, créez un déploiement avec les paramètres suivants :

    | Option | Valeur |
    | ------ | ----- |
    | Nom   | OilPumpDevices |
    | Type de package | Manifeste Edge |
    | Package | oil-pump-device.json |
    | Groupe d’appareils | OilPumps |
    | Priorité | 10 |

    [![Créer un déploiement](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Cliquez sur **Appliquer**.

Vous devez attendre plusieurs minutes pour que le package soit déployé sur votre appareil et que les données de télémétrie commencent à être envoyées par l’appareil.

[![Déploiement actif](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

La page **Déploiements** affiche les métriques suivantes :

* **Ciblé** indique le nombre d’appareils qui se trouvent dans le groupe d’appareils.
* **Appliqué** indique le nombre d’appareils auxquels le contenu de déploiement a été appliqué.
* **Réussi** montre le nombre d’appareils de périphérie du déploiement indiquant une réussite de l’exécution du client IoT Edge.
* **Échec** montre le nombre d’appareils de périphérie du déploiement indiquant un échec de l’exécution du client IoT Edge.

## <a name="monitor-the-device"></a>Superviser l’appareil

Vous pouvez afficher les données de télémétrie de température du capteur de la pompe à pétrole dans l’interface utilisateur web de la supervision à distance :

1. Accédez à la page **Device Explorer**, puis sélectionnez votre dispositif de pompe à huile.
1. Dans la section **Télémétrie** du panneau **Détails de l’appareil**, cliquez sur **Température** :

    [![Afficher les données de télémétrie](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

Vous pouvez voir comment la température augmente jusqu’à atteindre le seuil maximal. Le module Edge Stream Analytics détecte le moment où la température atteint ce seuil et envoie une commande au capteur pour qu’il réduise immédiatement la température. Tout ce traitement est effectué sur l’appareil sans communication avec le cloud.

Si vous souhaitez avertir les opérateurs lorsque le seuil a été atteint, vous pouvez créer une règle dans l’interface utilisateur web de la supervision à distance :

1. Accédez à la page **Règles**, puis cliquez sur **+Nouvelle règle**.
1. Créez une règle avec les paramètres suivants :

    | Option | Valeur |
    | ------ | ----- |
    | Nom de la règle | Température de la pompe à pétrole |
    | Description | La température de la pompe à pétrole a dépassé les 300 degrés |
    | Groupe d’appareils | OilPumps |
    | Calcul | Instantané |
    | Champ | température |
    | Opérateur | > |
    | Valeur | 300 |
    | Niveau de gravité | Info |

    [![Créer une règle](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Cliquez sur **Appliquer**.

1. Accédez à la page **Tableau de bord**. Une alerte s’affiche dans le panneau **Alertes** lorsque la température du capteur **oil-pump** dépasse les 300 degrés.

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a montré comment ajouter et configurer un appareil IoT Edge dans l’accélérateur de solution de supervision à distance. Pour plus d’informations sur l’utilisation des packages IoT Edge dans la solution de supervision à distance, consultez le guide pratique suivant :

> [!div class="nextstepaction"]
> [Importer un package IoT Edge dans votre accélérateur de solution de supervision à distance](iot-accelerators-remote-monitoring-import-edge-package.md)

Pour plus d’informations sur l’installation du runtime IoT Edge, consultez [Installer le runtime Azure IoT Edge sur Linux (x64)](../iot-edge/how-to-install-iot-edge.md).

Pour plus d’informations sur l’utilisation d’Azure Stream Analytics sur les appareils de périphérie, consultez [Déployer Azure Stream Analytics en tant que module IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md).