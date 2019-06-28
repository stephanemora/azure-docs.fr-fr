---
title: ESP8266 vers cloud - Connecter la carte Feather HUZZAH ESP8266 à Azure IoT Hub | Microsoft Docs
description: Ce didacticiel explique comment configurer la carte Adafruit Feather HUZZAH ESP8266 et la connecter à Azure IoT Hub pour envoyer des données à la plateforme cloud Azure.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 00d0e3a749c4af8faacfed5f725ce16e0492c399
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409077"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Connecter l’Adafruit Feather HUZZAH ESP8266 à Azure IoT Hub dans le cloud

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Connexion entre un capteur DHT22, une carte Feather HUZZAH ESP8266 et IoT Hub](./media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>Procédure

En premier lieu, vous connecterez l’Adafruit Feather HUZZAH ESP8266 à un IoT Hub créé à cette fin. Ensuite, vous exécutez un exemple d’application sur l’ESP8266 pour collecter des données de température et d’humidité provenant d’un capteur DHT22. Enfin, vous enverrez les données du capteur à votre IoT Hub.

> [!NOTE]
> Si vous utilisez une autre carte ESP8266, vous pouvez également suivre ces étapes pour la connecter à votre IoT Hub. Selon la carte ESP8266 que vous utilisez, il se peut que vous deviez reconfigurer le paramètre `LED_PIN`. Par exemple, si vous utilisez l’ESP8266 d’AI-Thinker, vous devrez peut-être remplacer la valeur `0` de ce paramètre par `2`. Vous n’avez pas encore de kit ? Obtenez-le depuis le [site web Azure](https://azure.com/iotstarterkits).

## <a name="what-you-learn"></a>Contenu

* Création d’un IoT Hub et enregistrement d’un appareil pour la carte Feather HUZZAH ESP8266
* Connexion du Feather HUZZAH ESP8266 au capteur et à votre ordinateur
* Collecte des données du capteur en exécutant un exemple d’application sur la carte Feather HUZZAH ESP8266
* Envoi des données du capteur à votre IoT Hub

## <a name="what-you-need"></a>Ce dont vous avez besoin

![Composants requis pour le didacticiel](./media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Pour cette opération, vous aurez besoin des composants suivants de votre Kit de démarrage Feather HUZZAH ESP8266 :

* La carte Feather HUZZAH ESP8266
* Un câble Micro USB Micro B vers USB Type A

Vous aurez également besoin des éléments suivants pour votre environnement de développement :

* Un abonnement Azure actif. Si vous ne possédez pas de compte Azure, vous pouvez [créer un compte d’évaluation Azure gratuit](https://azure.microsoft.com/free/) en quelques minutes.
* Un Mac ou un PC exécutant Windows ou Ubuntu.
* Un réseau sans fil auquel connecter la carte Feather HUZZAH ESP8266.
* Une connexion Internet pour télécharger l’outil de configuration.
* [Extension de Visual Studio Code pour Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> La version IDE d’Arduino utilisée par l’extension de Visual Studio Code pour Arduino doit être la version 1.6.8 ou une version ultérieure. Les versions antérieures ne fonctionneront pas avec la bibliothèque AzureIoT.

Les éléments suivants sont facultatifs si vous n’avez pas de capteur. Vous avez également la possibilité d’utiliser des données de capteur simulées.

* Un capteur de température et d’humidité Adafruit DHT22
* Une platine d’expérimentation
* Des câbles de liaison M/M

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Récupérer la chaîne de connexion pour le hub IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Inscrire un nouvel appareil dans le hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Connecter la carte Feather HUZZAH ESP8266 au capteur et à votre ordinateur

Dans cette section, vous connectez les capteurs à votre tableau. Puis vous connectez votre appareil à votre ordinateur pour une utilisation ultérieure.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Connecter un capteur de température et d’humidité DHT22 à la carte Feather HUZZAH ESP8266

Utilisez la platine d’expérimentation et les câbles de liaison pour effectuer la connexion comme suit. Si vous n’avez pas de capteur, ignorez cette section. Vous pourrez utiliser des données de capteur simulées à la place.

![Schéma des connexions](./media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)

Pour les broches du capteur, utilisez le câblage suivant :

| Début (capteur)           | Fin (carte)            | Couleur du câble   |
| -----------------------  | ---------------------- | ------------  |
| VDD (broche 31F)            | 3V (broche 58H)           | Câble rouge     |
| DATA (broche 32F)           | GPIO 2 (broche 46A)       | Câble bleu    |
| GND (broche 34F)            | GND (broche 56I)          | Câble noir   |

Pour plus d’informations, consultez [Adafruit DHT22 sensor setup](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) (Configuration du capteur Adafruit DHT22) et [Adafruit Feather HUZZAH ESP8266 Pinouts](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts) (Disposition des broches de l’Adafruit Feather HUZZAH ESP8266).

Votre carte Feather HUZZAH ESP8266 devrait à présent être connectée à un capteur opérationnel.

![Connexion du capteur DHT22 à la carte Feather Huzzah](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Connexion de la carte Feather HUZZAH ESP8266 à votre ordinateur

Comme présenté ensuite, à l’aide du câble Micro USB vers USB Type A, connectez la carte Feather HUZZAH ESP8266 à votre ordinateur.

![Connexion de la carte Feather Huzzah à votre ordinateur](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Ajouter des autorisations de port série (Ubuntu uniquement)

Si vous utilisez Ubuntu, assurez-vous que vous disposez des autorisations nécessaires pour utiliser le port USB de la carte Feather HUZZAH ESP8266. Pour ajouter des autorisations de port série, procédez comme suit :

1. Dans un terminal, exécutez les commandes suivantes :

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Vous obtenez l’une des sorties suivantes :

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   Dans la sortie, `uucp` ou `dialout` correspond au nom du propriétaire du groupe du port USB.

2. Ajoutez l’utilisateur au groupe en exécutant la commande suivante :

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` est le nom du propriétaire du groupe que vous avez obtenu à l’étape précédente. `<username>` est le nom de votre utilisateur Ubuntu.

3. Déconnectez-vous d’Ubuntu, puis reconnectez-vous pour que la modification prenne effet.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Collecter les données du capteur et les envoyer à votre IoT Hub

Dans cette section, vous allez déployer et exécuter un exemple d’application sur la carte Feather HUZZAH ESP8266. L’exemple d’application fait clignoter la LED de la carte Feather HUZZAH ESP8266 et envoie les données de température et d’humidité collectées à partir du capteur DHT22 à votre IoT Hub.

### <a name="get-the-sample-application-from-github"></a>Obtenir l’exemple d’application à partir de GitHub

L’exemple d’application est hébergé sur GitHub. Clonez l’exemple de référentiel contenant l’exemple d’application à partir de GitHub. Pour cloner l’exemple de référentiel, procédez comme suit :

1. Ouvrez une invite de commandes ou une fenêtre de terminal.

2. Accédez au dossier à utiliser pour le stockage de l’exemple d’application.

3. Exécutez la commande suivante :

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

   Ensuite, installez le package pour la carte Feather HUZZAH ESP8266 dans Visual Studio Code.

4. Ouvrez le dossier où l’exemple d’application est stocké.

5. Ouvrez le fichier app.ino dans le dossier d’application de Visual Studio Code.

   ![Ouvrir l’exemple d’application dans Visual Studio Code](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

6. Dans Visual Studio Code, entrez `F1`.

7. Saisissez **Arduino** et sélectionnez **Arduino : Gestionnaire de cartes**.

8. Dans l’onglet **Gestionnaire de cartes Arduino**, cliquez sur **URL supplémentaires**.

   ![Gestionnaire de cartes Arduino de VS Code](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

9. Dans la fenêtre **Paramètres utilisateur**, copiez et collez le texte suivant à la fin du fichier

   ```json
   "arduino.additionalUrls": "https://arduino.esp8266.com/stable/package_esp8266com_index.json"
   ```

   ![Configurez l’URL du package Arduino dans VS Code](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

10. Enregistrez le fichier et fermez l’onglet **Paramètres utilisateur**.

11. Cliquez sur **Actualiser les index du package**. Une fois l’actualisation terminée, recherchez **esp8266**.

12. Cliquez sur le bouton **installer** pour esp8266.

    Le gestionnaire de cartes indique que ESP8266 est installé avec une version 2.2.0 ou ultérieure.

    ![Le package ESP8266 est installé](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

13. Entrez `F1`, puis saisissez **Arduino** et sélectionnez **Arduino : configuration des cartes**.

14. Cliquez sur la case **Carte sélectionnée :** et saisissez **esp8266**, puis sélectionnez **Adafruit HUZZAH ESP8266 (esp8266)** .

    ![Sélectionnez la carte d’esp8266](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>Installer les bibliothèques nécessaires

1. Dans Visual Studio Code, entrez `F1`, puis tapez **Arduino** et sélectionnez **Arduino : Library Manager**.

2. Recherchez les noms de bibliothèque suivants un par un. Pour chacune des bibliothèques trouvées, cliquez sur **Installer**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Vous n’avez pas de capteur DHT22 ?

L’exemple d’application permet de simuler des données de température et d’humidité si vous n’avez pas de capteur DHT22. Pour configurer l’utilisation de données simulées par l’exemple d’application, procédez comme suit :

1. Ouvrez le fichier `config.h` dans le dossier `app`.

2. Recherchez la ligne de code suivante et remplacez la valeur `false` par `true` :

   ```c
   define SIMULATED_DATA true
   ```

   ![Configuration de l’exemple d’application pour utiliser des données simulées](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

3. Enregistrez le fichier .

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Déployer l’exemple d’application sur la carte Feather HUZZAH ESP8266

1. Dans Visual Studio Code, cliquez sur **\<Select Serial Port>** (Sélectionner le port série) dans la barre d’état, puis cliquez sur le port série de Feather HUZZAH ESP8266.

2. Entrez `F1`, puis saisissez **Arduino** et sélectionnez **Arduino : Téléverser** pour générer et déployer l’exemple d’application sur la carte Feather HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Entrer vos informations d’identification

Une fois le chargement terminé, suivez cette procédure pour entrer vos informations d’identification :

1. Ouvrez l’IDE Arduino, cliquez sur **Outils** > **Moniteur série**.

2. Dans la fenêtre Moniteur série, vous pouvez remarquer la présence de deux listes déroulantes dans le coin inférieur droit.

3. Pour la liste déroulante de gauche, sélectionnez **Pas de fin de ligne**.

4. Pour la liste déroulante de droite, sélectionnez **115200 baud**.

5. Dans la zone de saisie située en haut de la fenêtre Moniteur série, entrez les informations suivantes si elles vous sont demandées, puis cliquez sur **Envoyer**.

   * SSID Wi-Fi
   * Mot de passe Wi-Fi
   * Chaîne de connexion de l’appareil

> [!Note]
> Les informations d’identification sont stockées dans la mémoire EEPROM de la carte Feather HUZZAH ESP8266. Si vous cliquez sur le bouton de réinitialisation de la carte Feather HUZZAH ESP8266, l’exemple d’application vous demande si vous souhaitez effacer ces informations. Entrez `Y` pour effacer les informations. Vous êtes invité à fournir les informations une deuxième fois.

### <a name="verify-the-sample-application-is-running-successfully"></a>Vérifier que l’exemple d’application s’exécute correctement

Si vous voyez la sortie suivante dans la fenêtre Moniteur série et la LED clignoter sur la carte Feather HUZZAH ESP8266, l’exemple d’application s’exécute correctement.

![Sortie finale dans l’IDE Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Lire les messages reçus par votre hub

Une façon de surveiller les messages reçus par votre hub IoT à partir de votre appareil consiste à utiliser Azure IoT Tools pour Visual Studio Code. Pour en savoir plus, consultez [Utiliser Azure IoT Tools pour Visual Studio Code afin d’envoyer et de recevoir des messages entre votre appareil et un IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Pour d’autres méthodes de traitement des données envoyées par votre appareil, passez à la section suivante.

## <a name="next-steps"></a>Étapes suivantes

Vous avez correctement connecté une carte Feather HUZZAH ESP8266 à votre IoT Hub et envoyé les données de capteur collectées à votre IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]