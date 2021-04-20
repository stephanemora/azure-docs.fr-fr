---
title: Installer Azure IoT Edge | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur des appareils Windows ou Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: kgremban
ms.openlocfilehash: 39e165d862d6e174f763cd58529727fd26b8bd46
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311071"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Installation et désinstallation d’Azure IoT Edge pour Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Le runtime Azure IoT Edge est ce qui transforme un appareil en appareil IoT Edge. Le runtime peut être déployé sur un appareil de petite taille comme un Raspberry Pi ou de grande taille comme un serveur industriel. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud. Pour en savoir plus, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Cet article présente la procédure à suivre pour installer le runtime Azure IoT Edge sur des appareils Linux.

## <a name="prerequisites"></a>Prérequis

* Un [ID d’appareil inscrit](how-to-register-device.md)

  Si vous avez inscrit votre appareil avec l’authentification par clé symétrique, préparez la chaîne de connexion de l’appareil.

  Si vous avez inscrit votre appareil avec l’authentification par certificat auto-signé X.509, vous devez disposer au moins de l’un des certificats d’identité que vous avez utilisés pour inscrire l’appareil et la clé privée correspondante sur votre appareil.

* Un appareil Linux

  Vous devez avoir un appareil Linux x64, ARM32 ou ARM64. Microsoft fournit des packages d’installation pour les systèmes d’exploitation Ubuntu Server 18.04 et Raspberry Pi OS Stretch.

  Pour obtenir les informations les plus récentes sur les systèmes d’exploitation actuellement pris en charge pour les scénarios de production, consultez [Systèmes pris en charge par Azure IoT Edge](support.md#operating-systems).

  >[!NOTE]
  >La prise en charge des appareils ARM64 est en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Préparez votre appareil pour accéder aux packages d’installation de Microsoft.

  Installez la configuration du référentiel qui correspond au système d’exploitation de votre appareil.

  * **Ubuntu Server 18.04** :

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Raspberry Pi OS Stretch** :

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  Copiez la liste générée dans le répertoire sources.list.d.

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  Installez la clé publique Microsoft GPG.

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Les packages logiciels Azure IoT Edge sont soumis aux termes du contrat de licence situés dans chaque package (`usr/share/doc/{package-name}` ou dans le répertoire `LICENSE`). Lisez les termes du contrat de licence avant d’utiliser un package. Le fait d’installer et d’utiliser un package revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package en question.

## <a name="install-a-container-engine"></a>Installer un moteur de conteneur

Azure IoT Edge s’appuie sur un runtime de conteneur compatible avec OCI. Dans les scénarios de production, nous vous recommandons d’utiliser le moteur Moby. Le moteur Moby est le seul moteur de conteneur officiellement pris en charge avec Azure IoT Edge. Les images conteneur Docker CE/EE sont compatibles avec le runtime Moby.

Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

Installez le moteur Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Si des erreurs surviennent lors de l’installation du moteur de conteneur Moby, vérifiez la compatibilité de votre noyau Linux avec Moby. Certains fabricants d’appareils embarqués livrent des images d’appareils qui contiennent des noyaux Linux personnalisés sans les fonctionnalités nécessaires à la compatibilité du moteur de conteneur. Exécutez la commande suivante, qui utilise le [script check-config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) fourni par Moby pour vérifier la configuration de votre noyau :

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Dans la sortie du script, vérifiez que tous les éléments sous `Generally Necessary` et `Network Drivers` sont activés. Si vous ne disposez pas de certaines fonctionnalités, activez-les en régénérant votre noyau à partir des sources et en sélectionnant les modules associés à inclure dans le noyau .config approprié. De même, si vous utilisez un générateur de configuration du noyau comme `defconfig` ou `menuconfig`, trouvez et activez les fonctionnalités respectives et recompilez votre noyau en conséquence. Une fois que vous avez déployé votre nouveau noyau modifié, exécutez à nouveau le script check-config pour vérifier que toutes les fonctionnalités requises ont été activées avec succès.

## <a name="install-iot-edge"></a>Installer IoT Edge

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Le démon de sécurité IoT Edge fournit et gère les standards de sécurité sur l’appareil IoT Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge.

La procédure de cette section représente le processus classique d’installation de la dernière version sur un appareil disposant d’une connexion Internet. Si vous devez installer une version spécifique, comme une préversion, ou si vous devez installer en mode hors connexion, suivez les étapes d’[installation d’une version hors connexion ou spécifique](#offline-or-specific-version-installation-optional) plus loin dans cet article.

Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

Vérifiez les versions d’IoT Edge disponibles.

   ```bash
   apt list -a iotedge
   ```

Si vous souhaitez installer la version la plus récente du démon de sécurité, utilisez la commande suivante, qui installe également la dernière version du package **libiothsm-std** :

   ```bash
   sudo apt-get install iotedge
   ```

Si vous souhaitez installer une version spécifique du démon de sécurité, spécifiez la version dans la sortie de la commande apt list. Spécifiez également la même version pour le package **libiothsm-std**, qui, dans le cas contraire, installe sa dernière version. Par exemple, la commande suivante permet d’installer la dernière version de la mise en production 1.1 :

   ```bash
   sudo apt-get install iotedge=1.1* libiothsm-std=1.1*
   ```

Si la version que vous souhaitez installer ne figure pas dans la liste, suivez la procédure [Installation d’une version spécifique ou hors connexion](#offline-or-specific-version-installation-optional) dans la suite de cet article. Cette section vous montre comment cibler une version antérieure du démon de sécurité IoT Edge ou des versions Release Candidate.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Le service IoT Edge fournit et gère les standards de sécurité sur l’appareil IoT Edge. Le service se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge.

Le service IoT Identity a été introduit avec la version 1.2 d’IoT Edge. Ce service gère l’approvisionnement et la gestion des identités pour IoT Edge et pour d’autres composants d’appareil qui doivent communiquer avec IoT Hub.

La procédure de cette section représente le processus classique d’installation de la dernière version sur un appareil disposant d’une connexion Internet. Si vous devez installer une version spécifique, comme une préversion, ou si vous devez installer en mode hors connexion, suivez les étapes d’[installation d’une version hors connexion ou spécifique](#offline-or-specific-version-installation-optional) plus loin dans cet article.

>[!NOTE]
>Les étapes décrites dans cette section vous indiquent la marche à suivre pour installer IoT Edge version 1.2.
>
>Si vous disposez déjà d’un appareil IoT Edge exécutant une version plus ancienne et que vous souhaitez effectuer une mise à niveau vers la version 1.2, suivez les étapes décrites dans [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md). La version 1.2 est si différente des versions précédentes d’IoT Edge que des étapes spécifiques sont nécessaires pour la mise à niveau.

Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

Vérifiez les versions d’IoT Edge disponibles.

   ```bash
   apt list -a aziot-edge
   ```

Si vous souhaitez installer la version la plus récente d’IoT Edge, utilisez la commande suivante qui installe également la dernière version du package de services d’identité :

   ```bash
   sudo apt-get install aziot-edge
   ```

Si vous souhaitez installer une version spécifique d’IoT Edge et du service d’identité, vous pouvez également spécifier les versions dans la sortie de la liste apt. Spécifiez les mêmes versions pour les deux services. Par exemple, la commande suivante permet d’installer la dernière version de la mise en production 1.2 :

   ```bash
   sudo apt-get install aziot-edge=1.2* aziot-identity-service=1.2*
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>Provisionnement de l’appareil avec son identité cloud

Maintenant que le moteur de conteneur et le runtime d’IoT Edge sont installés sur votre appareil, vous pouvez passer à l’étape suivante, à savoir configurer l’appareil avec ses informations d’identité et d’authentification cloud.

Choisissez la section suivante en fonction du type d’authentification que vous souhaitez utiliser :

* [Option n°1 : Authentification avec des clés symétriques](#option-1-authenticate-with-symmetric-keys)
* [Option n°2 : Authentification avec des certificats X.509](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Option 1 : Authentification avec des clés symétriques

Le runtime IoT Edge est maintenant installé sur votre appareil Linux. Vous devez provisionner l’appareil avec ses informations d’identité et d’authentification cloud.

Cette section décrit la procédure à suivre pour provisionner un appareil avec une authentification par clé symétrique. Vous devez avoir inscrit votre appareil dans IoT Hub et récupéré la chaîne de connexion à partir des informations de l’appareil. Si ce n’est pas le cas, suivez la procédure décrite dans [Inscription d’un appareil IoT Edge dans IoT Hub](how-to-register-device.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Sur l’appareil IoT Edge, ouvrez le fichier de configuration.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Recherchez les configurations d’approvisionnement du fichier et supprimez les marques de commentaire de la section **Manual provisioning configuration using a connection string** si ce n’est déjà fait.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Mettez à jour la valeur de **device_connection_string** avec la chaîne de connexion à partir de votre appareil IoT Edge. Veillez à ajouter des marques de commentaire sur les autres sections de provisionnement. Assurez-vous que la ligne **d’approvisionnement :** n’est pas précédée d’une espace et que les éléments imbriqués sont en retrait de deux espaces.

Pour coller le contenu du Presse-papiers dans Nano, appuyez sur `Shift+Right Click` ou sur `Shift+Insert`.

Enregistrez et fermez le fichier.

   `CTRL + X`, `Y`, `Enter`

Après avoir entré les informations de provisionnement dans le fichier de configuration, redémarrez le démon :

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Créez le fichier de configuration pour votre appareil à partir d’un fichier de modèle fourni dans le cadre de l’installation d’IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

Sur l’appareil IoT Edge, ouvrez le fichier de configuration.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Recherchez la section **Provisioning** du fichier et supprimez les marques de commentaire des lignes sur l’approvisionnement manuel avec une chaîne de connexion.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string = "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Mettez à jour la valeur **connection_string** avec la chaîne de connexion à partir de votre appareil IoT Edge.

Pour coller le contenu du Presse-papiers dans Nano, appuyez sur `Shift+Right Click` ou sur `Shift+Insert`.

Enregistrez et fermez le fichier.

   `CTRL + X`, `Y`, `Enter`

Après avoir entré les informations d’approvisionnement dans le fichier de configuration, appliquez vos modifications :

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

### <a name="option-2-authenticate-with-x509-certificates"></a>Option n°2 : Authentification avec des certificats X.509

Le runtime IoT Edge est maintenant installé sur votre appareil Linux. Vous devez provisionner l’appareil avec ses informations d’identité et d’authentification cloud.

Cette section décrit la procédure à suivre pour provisionner un appareil avec une authentification par certificat X.509. Vous devez avoir inscrit votre appareil dans IoT Hub, en indiquant les empreintes correspondant au certificat et à la clé privée situés sur votre appareil IoT Edge. Si ce n’est pas le cas, suivez la procédure décrite dans [Inscription d’un appareil IoT Edge dans IoT Hub](how-to-register-device.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Sur l’appareil IoT Edge, ouvrez le fichier de configuration.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Recherchez la section des configurations de provisionnement du fichier et supprimez les marques de commentaire de la section **Manual provisioning configuration using an X.509 identity certificate**. Veillez à ajouter des marques de commentaire sur les autres sections de provisionnement. Assurez-vous que la ligne **d’approvisionnement :** n’est pas précédée d’une espace et que les éléments imbriqués sont en retrait de deux espaces.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Mettez à jour les champs suivants :

* **iothub_hostname** : nom d’hôte de l’hub IoT auquel l’appareil doit se connecter. Par exemple, `{IoT hub name}.azure-devices.net`.
* **device_id** : ID que vous avez indiqué lors de l’inscription de l’appareil.
* **identity_cert** : URI vers un certificat d’identité sur l’appareil. Par exemple, `file:///path/identity_certificate.pem`.
* **identity_pk** : URI vers le fichier de clé privée pour le certificat d’identité fourni. Par exemple, `file:///path/identity_key.pem`.

Enregistrez et fermez le fichier.

   `CTRL + X`, `Y`, `Enter`

Après avoir entré les informations de provisionnement dans le fichier de configuration, redémarrez le démon :

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Créez le fichier de configuration pour votre appareil à partir d’un fichier de modèle fourni dans le cadre de l’installation d’IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

Sur l’appareil IoT Edge, ouvrez le fichier de configuration.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Recherchez la section **Provisioning** du fichier, et supprimez les marques de commentaire des lignes sur l’approvisionnement manuel avec un certificat d’identité X.509. Veillez à ajouter des marques de commentaire sur les autres sections de provisionnement.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Mettez à jour les champs suivants :

* **iothub_hostname** : nom d’hôte de l’hub IoT auquel l’appareil doit se connecter. Par exemple, `{IoT hub name}.azure-devices.net`.
* **device_id** : ID que vous avez indiqué lors de l’inscription de l’appareil.
* **identity_cert** : URI d’un certificat d’identité sur l’appareil, par exemple : `file:///path/identity_certificate.pem`. Ou émettez le certificat de manière dynamique en utilisant EST ou une autorité de certification locale.
* **identity_pk** : URI du fichier de clé privée pour le certificat d’identité fourni. par exemple : `file:///path/identity_key.pem`. Ou fournissez un URI PKCS#11, puis fournissez vos informations de configuration dans la section **PKCS#11** ultérieurement dans le fichier config.

Enregistrez et fermez le fichier.

   `CTRL + X`, `Y`, `Enter`

Après avoir entré les informations d’approvisionnement dans le fichier config, appliquez vos modifications :

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>Vérification de la réussite de la configuration

Vérifiez que le runtime a été correctement installé et configuré sur votre appareil IoT Edge.

>[!TIP]
>Vous avez besoin de privilèges élevés pour exécuter les commandes `iotedge`. Une fois que vous vous déconnectez de votre machine et que vous vous reconnectez pour la première fois après avoir installé le runtime IoT Edge, vos autorisations sont automatiquement mises à jour. Dans l’intervalle, ajoutez `sudo` devant les commandes.

Vérifiez que le service système IoT Edge est en cours d’exécution.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

Une réponse d’état réussie est `Ok`.

::: moniker-end

Si vous avez besoin de résoudre les problèmes du service, récupérez les journaux d’activité de ce dernier.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

Utilisez l’outil `check` pour vérifier l’état de la configuration et de la connexion de l’appareil.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>Utilisez toujours `sudo` pour exécuter l’outil de vérification, même après la mise à jour de vos autorisations. L’outil a besoin de privilèges élevés pour accéder au fichier config pour vérifier l’état de la configuration.

Affichez tous les modules s’exécutant sur votre appareil IoT Edge. Lors du premier démarrage du service, seul le module **edgeAgent** apparaît dans cette liste. Le module edgeAgent s’exécute par défaut et vous aide à installer et démarrer tous les modules supplémentaires que vous déployez sur votre appareil.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>Installation d’une version spécifique hors connexion (facultatif)

Les étapes de cette section concernent les scénarios non couverts par les étapes d’installation standard. Cela peut inclure :

* Installer IoT Edge en mode hors connexion
* Installer une version Release candidate

Utilisez les étapes de cette section si vous souhaitez installer une version spécifique du runtime Azure IoT Edge, qui n’est pas disponible via `apt-get install`. La liste des packages Microsoft contient uniquement un ensemble limité de versions récentes et leurs sous-versions. Par conséquent, ces étapes sont destinées à toute personne souhaitant installer une version antérieure ou une version Release Candidate.

À l’aide de commandes curl, vous pouvez cibler les fichiers de composants directement à partir du référentiel GitHub IoT Edge.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. Accédez aux [versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)et recherchez celle que vous souhaitez cibler.

2. Développez la section **Ressources** pour cette version.

3. Chaque version devrait avoir de nouveaux fichiers pour le démon de sécurité IoT Edge et le hsmlib. Si vous envisagez d’installer IoT Edge sur un appareil hors connexion, téléchargez ces fichiers à l’avance. Autrement, utilisez les commandes suivantes pour mettre à jour ces composants.

   1. Trouvez le fichier **libiothsm-std** qui correspond à l’architecture de votre périphérique IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien.

   2. Utilisez le lien copié dans la commande suivante pour installer cette version du hsmlib :

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
      ```

   3. Trouvez le fichier **iotedge** qui correspond à l’architecture de votre périphérique IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien.

   4. Utilisez le lien copié dans la commande suivante pour installer cette version du démon de sécurité de l’IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Si votre appareil exécute actuellement IoT Edge version 1.1 ou une version antérieure, désinstallez les packages **iotedge** et **libiothsm-std** avant de suivre les étapes de cette section. Pour plus d’informations, consultez [Mise à jour de la version 1.0 ou 1.1 vers la version 1.2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

1. Accédez aux [versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)et recherchez celle que vous souhaitez cibler.

2. Développez la section **Ressources** pour cette version.

3. Chaque mise en production devrait avoir de nouveaux fichiers pour IoT Edge et le service d’identité. Si vous envisagez d’installer IoT Edge sur un appareil hors connexion, téléchargez ces fichiers à l’avance. Autrement, utilisez les commandes suivantes pour mettre à jour ces composants.

   1. Recherchez le fichier **aziot-identity-service** correspondant à l’architecture de votre appareil IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien.

   2. Utilisez le lien copié dans la commande suivante pour installer cette version du service d’identité :

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
      ```

   3. Recherchez le fichier **aziot-edge** correspondant à l’architecture de votre appareil IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien.

   4. Utilisez le lien copié dans la commande suivante pour installer cette version d’IoT Edge.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

Maintenant que le moteur de conteneur et le runtime IoT Edge sont installés sur votre appareil, vous pouvez passer à l’étape suivante, [Provisionnement de l’appareil avec son identité cloud](#provision-the-device-with-its-cloud-identity).

## <a name="uninstall-iot-edge"></a>Désinstaller IoT Edge

Si vous souhaitez supprimer l’installation d’IoT Edge de votre appareil, utilisez les commandes suivantes.

Supprimez le runtime IoT Edge.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

Utilisez l’indicateur `--purge` pour supprimer tous les fichiers associés à IoT Edge, dont vos fichiers de configuration. Omettez cet indicateur si vous souhaitez réinstaller IoT Edge et utiliser les mêmes informations de configuration à l’avenir.

Lors de la suppression du runtime IoT Edge, tous les conteneurs qu’il a créés sont arrêtés, mais continuent d’exister sur votre appareil. Affichez tous les conteneurs pour voir ceux qui restent.

```bash
sudo docker ps -a
```

Supprimez les conteneurs de votre appareil, dont les deux conteneurs de runtime.

```bash
sudo docker rm -f <container name>
```

Enfin, supprimez le runtime du conteneur de votre appareil.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Étapes suivantes

Passez à [Déployer des modules IoT Edge](how-to-deploy-modules-portal.md) pour savoir comment déployer des modules sur votre appareil.
