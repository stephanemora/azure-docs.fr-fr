---
title: Connecter Raspberry Pi à Azure IoT Hub à l'aide de C | Microsoft Docs
description: Apprenez à configurer Raspberry Pi et à le connecter à Azure IoT Hub pour lui permettre d'envoyer des données à la plateforme cloud Azure
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 06/14/2021
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 845a2d7af740b804d25250753c02c2791da82944
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414932"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Connecter Raspberry Pi à Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Dans ce didacticiel, vous commencez par découvrir les principes fondamentaux de l’utilisation de Raspberry Pi exécutant le système d’exploitation Raspberry Pi. Vous apprenez ensuite à connecter en toute transparence vos appareils au cloud avec [Azure IoT Hub](about-iot-hub.md). Pour obtenir des exemples Windows 10 IoT Standard, accédez au [centre de développement Windows](https://www.windowsondevices.com/).

Vous n’avez pas encore de kit ? Essayez [le simulateur en ligne Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Ou achetez un nouveau kit [ici](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Procédure

* Créez un hub IoT.

* Inscrivez un appareil pour Pi dans votre IoT Hub.

* Configurez Raspberry Pi.

* Exécutez un exemple d’application sur Pi pour envoyer des données de capteur à votre IoT Hub.

Connectez Raspberry Pi à un IoT Hub créé à cette fin. Exécutez ensuite un exemple d’application sur Pi pour collecter des données de température et d’humidité provenant d’un capteur BME280. Enfin, envoyez les données du capteur à votre IoT Hub.

## <a name="what-you-learn"></a>Contenu

* Création d’un Azure IoT Hub et obtention de la chaîne de connexion de votre nouvel appareil.

* Connexion de Pi à un capteur BME280.

* Collecte des données du capteur en exécutant un exemple d’application sur Pi.

* Envoi des données du capteur à votre IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

![Ce dont vous avez besoin](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Une carte Raspberry Pi 2 ou Raspberry Pi 3.

* Un abonnement Azure actif. Si vous ne possédez pas de compte Azure, vous pouvez [créer un compte d’évaluation Azure gratuit](https://azure.microsoft.com/free/) en quelques minutes.

* Un moniteur, un clavier USB et une souris qui se connectent à Pi.

* Un Mac ou un PC exécutant Windows ou Linux.

* Une connexion Internet.

* Une carte microSD d’au moins 16 Go.

* Un adaptateur USB-SD ou une carte microSD pour graver l’image du système d’exploitation sur la carte microSD.

* Une alimentation 5 V 2 A avec le câble micro USB de 6 pieds (env. 183 cm).

Les éléments suivants sont facultatifs :

* Un capteur de température, de pression et d’humidité Adafruit BME280 assemblé.

* Une platine d’expérimentation.

* 6 câbles de liaison F/M.

* Une LED de 10 mm diffuse.

> [!NOTE]
> Ces éléments sont facultatifs, car l’exemple de code prend en charge les données de capteur simulées.
>

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Inscrire un nouvel appareil dans le hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Installer Raspberry Pi

Configurez maintenant l’appareil Raspberry Pi.

### <a name="install-the-raspberry-pi-os"></a>Installer le système d’exploitation Raspberry PI

Préparez la carte microSD pour l’installation de l’image du système d’exploitation Raspberry PI.

1. Télécharger le système d’exploitation Raspberry Pi

   1. [Téléchargez système d’exploitation Raspberry Pi](https://www.raspberrypi.org/software/) (fichier .zip).

   2. Extrayez l’image dans un dossier sur votre ordinateur.

2. Installez le système d’exploitation Raspberry Pi sur la carte microSD.

   1. [Téléchargez et installez l’utilitaire de graveur de carte SD Etcher](https://etcher.io/).

   2. Exécutez Etcher et sélectionnez l’image du système d’exploitation Raspberry Pi extraite à l’étape 1.

   3. Sélectionnez le lecteur de carte microSD. Remarque : Etcher a peut-être déjà sélectionné le lecteur approprié.

   4. Cliquez sur Flash pour installer le système d’exploitation Raspberry Pi sur la carte microSD.

   5. Retirez la carte microSD de votre ordinateur une fois l’installation terminée. Vous pouvez retirer directement la carte microSD en toute sécurité, car Etcher éjecte ou démonte automatiquement la carte microSD une fois le processus terminé.

   6. Insérez la carte microSD dans Pi.

### <a name="enable-ssh-and-spi"></a>Activer SSH et SPI

1. Connectez Pi au moniteur, au clavier et à la souris, démarrez Pi, puis connectez-vous au système d’exploitation Raspberry Pi en utilisant `pi` comme nom d’utilisateur et `raspberry` comme mot de passe.
 
2. Cliquez sur l’icône Raspberry > **Préférences** > **Configuration de Raspberry Pi**.

   ![Menu des préférences du système d’exploitation Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. Sur l’onglet **Interfaces**, définissez **SPI** et **SSH** sur **Activer**, puis cliquez sur **OK**. Si vous ne possédez pas de capteurs physiques et que vous souhaitez utiliser des données de capteur simulé, cette étape est facultative.

   ![Activer SPI et SSH sur Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Pour activer SSH et SPI, vous trouverez d’autres documents de référence sur [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) et [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Connecter le capteur à Pi

Utilisez la platine d’expérimentation et les câbles de liaison pour connecter une LED et un BME280 à Pi comme suit. Si vous ne disposez pas de ce capteur, [ignorez cette section](#connect-pi-to-the-network).

![La connexion entre Raspberry Pi et le capteur](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

Le capteur BME280 peut collecter des données sur la température et l’humidité. Et la DEL clignote s’il existe une communication entre l’appareil et le cloud.

Pour les broches du capteur, utilisez le câblage suivant :

| Début (capteur et LED)     | Fin (carte)            | Couleur du câble   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (broche 5G)         | GPIO 4 (broche 7)         | Câble blanc   |
| LED GND (broche 6G)         | GND (broche 6)            | Câble noir   |
| VDD (broche 18F)            | ALIM 3,3V (broche 17)      | Câble blanc   |
| GND (broche 20F)            | GND (broche 20)           | Câble noir   |
| SCK (broche 21F)            | SPI0 SCLK (broche 23)     | Câble orange  |
| SDO (broche 22F)            | SPI0 MISO (broche 21)     | Câble jaune  |
| SDI (broche 23F)            | SPI0 MOSI (broche 19)     | Câble vert   |
| CS (broche 24F)             | SPI0 CS (broche 24)       | Câble bleu    |

Cliquez pour afficher les [mappages de broches de Raspberry Pi 2 et 3](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) à titre de référence.

Une fois le BME280 connecté à votre Raspberry Pi, il doit se présenter comme sur l’image ci-dessous.

![Pi et BME280 connectés](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Connexion de Pi au réseau

Mettez Pi sous tension à l’aide du câble micro USB et de l’alimentation. Utilisez le câble Ethernet pour connecter Pi à votre réseau câblé ou suivez les [instructions fournies par la Fondation Raspberry Pi](https://www.raspberrypi.org/documentation/configuration/wireless/) pour connecter Pi à votre réseau sans fil. Une fois que votre Pi est correctement connecté au réseau, vous devez relever son [adresse IP](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Connecté au réseau câblé](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Exécuter un exemple d’application sur Pi

### <a name="sign-into-your-raspberry-pi"></a>Vous connecter à votre Raspberry Pi

1. Utilisez l’un des clients SSH suivants à partir de votre ordinateur hôte pour vous connecter à votre Raspberry Pi.
   
   **Utilisateurs Windows**
   1. Téléchargez et installez [PuTTY](https://www.putty.org/) pour Windows. 
   1. Copiez l’adresse IP de votre Pi dans la section du nom d’hôte (ou adresse IP) et sélectionnez le type de connexion SSH.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Utilisateurs Mac et Ubuntu**

   Utilisez le client SSH intégré sur Ubuntu ou macOS. Vous devrez peut-être exécuter `ssh pi@<ip address of pi>` pour connecter le Pi via le protocole SSH.
   > [!NOTE]
   > Le nom d’utilisateur par défaut est `pi` et le mot de passe est `raspberry`.


### <a name="configure-the-sample-application"></a>Configurer l’exemple d’application

1. Clonez l’exemple d’application en exécutant la commande suivante :

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Un script d’installation est fourni avec l’exemple pour préparer l’environnement de développement et générer l’exemple. Exécutez le script de configuration :

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Si vous **n’avez pas de BME280 physique**, vous pouvez utiliser '--simulated-data' en tant que paramètre de ligne de commande pour simuler des données de température et d’humidité. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Générer et exécuter l'exemple d’application

1. Le script d’installation doit avoir déjà généré l’exemple. Toutefois, si vous apportez des modifications et que vous devez régénérer l’exemple d’application, exécutez la commande suivante :

   ```bash
   cmake . && make
   ```
   
   ![Sortie de la génération](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Exécutez l’exemple d’application en exécutant la commande suivante :

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Assurez-vous de copier-coller la chaîne de connexion de l’appareil entre les guillemets simples.
   >

Vous devriez voir le résultat suivant, qui affiche les données de capteur et les messages envoyés à votre IoT Hub.

![Sortie - données de capteur envoyées depuis Raspberry Pi vers votre IoT Hub](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Lire les messages reçus par votre hub

Une façon de surveiller les messages reçus par votre hub IoT à partir de votre appareil consiste à utiliser Azure IoT Tools pour Visual Studio Code. Pour en savoir plus, consultez [Utiliser Azure IoT Tools pour Visual Studio Code afin d’envoyer et de recevoir des messages entre votre appareil et un IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Pour d’autres méthodes de traitement des données envoyées par votre appareil, passez à la section suivante.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Vous pouvez utiliser les ressources créées dans cette rubrique avec d’autres didacticiels et démarrages rapides dans cet ensemble de documents. Si vous souhaitez continuer à utiliser d’autres démarrages rapides ou les didacticiels, ne nettoyez pas les ressources créées dans cette rubrique. Sinon, effectuez les opérations suivantes pour supprimer toutes les ressources créées dans cette rubrique dans le portail Azure.

1. À partir du menu de gauche, dans le portail Azure, sélectionnez **Toutes les ressources**, puis l’IoT Hub que vous avez créé. 
1. En haut du volet d’aperçu du IoT Hub, cliquez sur **Supprimer**.
1. Entrez le nom de votre hub et cliquez de nouveau sur **Supprimer** pour confirmer la suppression définitive du IoT Hub.


## <a name="next-steps"></a>Étapes suivantes

Vous avez exécuté un exemple d’application pour collecter des données de capteur et les envoyer à votre IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]