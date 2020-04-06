---
title: Installer Azure IoT Edge sur Linux | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur des appareils Linux exécutant Ubuntu ou Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: kgremban
ms.openlocfilehash: fb86ee9ce956917f8da44146e58a4775e0ba639f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535899"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Installer le runtime Azure IoT Edge sur des systèmes Linux Debian

Le runtime Azure IoT Edge est ce qui transforme un appareil en appareil IoT Edge. Le runtime peut être déployé sur un appareil de petite taille comme un Raspberry Pi ou de grande taille comme un serveur industriel. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud. Pour en savoir plus, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Cet article répertorie les étapes pour installer le runtime Azure IoT Edge sur un appareil Linux X64, ARM32 ou ARM64. Nous fournissons des paquets d’installation pour Ubuntu Server 16.04, Ubuntu Server 18.04 et Raspbian Stretch. Reportez-vous à [Systèmes Azure IoT Edge pris en charge](support.md#operating-systems) pour obtenir une liste des systèmes d’exploitation et architectures Linux pris en charge.

> [!NOTE]
> Chaque package des référentiels de logiciels Linux est soumis aux termes du contrat de licence qu’il contient (/usr/share/doc/*nom_package*). Lisez les termes du contrat de licence avant d’utiliser le package. Le fait d’installer et d’utiliser le package revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package.

## <a name="install-the-latest-runtime-version"></a>Installer la dernière version du runtime

Utilisez les sections suivantes pour installer la version la plus récente du runtime Azure IoT Edge sur votre appareil.

>[!NOTE]
>La prise en charge des appareils ARM64 est en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="register-microsoft-key-and-software-repository-feed"></a>Inscrire la clé Microsoft et le flux des référentiels de logiciels

Préparez votre appareil pour l’installation du runtime IoT Edge.

Installez la configuration du référentiel. Choisissez la commande **16.04** ou **18.04** correspondant au système d'exploitation de votre appareil :

* **Ubuntu Server 16.04** :

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04** :

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch** :

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copiez la liste générée.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installer la clé publique Microsoft GPG

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Installer le runtime de conteneur

Azure IoT Edge s’appuie sur un runtime de conteneur [compatible avec OCI](https://www.opencontainers.org/). Pour les scénarios de production, nous vous recommandons d’utiliser le moteur [Moby](https://mobyproject.org/) fourni ci-dessous. Le moteur Moby est le seul moteur de conteneur officiellement pris en charge avec Azure IoT Edge. Les images conteneur Docker CE/EE sont compatibles avec le runtime Moby.

Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

Installez le moteur Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Installez l’interface de ligne de commande (CLI) Moby. L’interface CLI est utile pour le développement, mais facultative pour les déploiements de production.

   ```bash
   sudo apt-get install moby-cli
   ```

Si vous rencontrez des erreurs lors de l’installation du runtime de conteneur Moby, suivez les étapes de [vérification de la compatibilité de votre noyau Linux avec Moby](#verify-your-linux-kernel-for-moby-compatibility), décrites plus loin dans cet article.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Installer le démon de sécurité Azure IoT Edge

Le **démon de sécurité IoT Edge** fournit et gère les standards de sécurité sur l’appareil IoT Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge.

La commande d’installation installe également la version standard du **libiothsm** si elle n’est pas déjà présente.

Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

Installez le démon de sécurité. Le package est installé sous `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Une fois l’installation de IoT Edge réussie, la sortie vous invite à mettre à jour le fichier de configuration. Suivez les étapes de la section [Configurer le démon de sécurité](#configure-the-security-daemon) pour terminer l’approvisionnement de l’appareil.

## <a name="install-a-specific-runtime-version"></a>Installer une version du runtime spécifique

Si vous voulez installer une version spécifique de Moby et du runtime Azure IoT Edge au lieu d’utiliser les versions les plus récentes, vous pouvez cibler les fichiers de composants directement depuis le référentiel GitHub IoT Edge. Suivez les étapes suivantes pour obtenir tous les composants IoT Edge sur votre appareil : le moteur Moby et CLI, le libiothsm, et enfin le démon de sécurité IoT Edge. Passez à la section suivante, [Configurer le démon de sécurité](#configure-the-security-daemon), si vous ne souhaitez pas passer à une version spécifique du runtime.

1. Accédez aux [versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)et recherchez celle que vous souhaitez cibler.

2. Développez la section **Ressources** pour cette version.

3. Des mises à jour du moteur Moby peuvent être disponibles dans une version donnée, ou non. Si vous voyez des fichiers qui commencent par **moby-engine** et **moby-cli**, utilisez les commandes suivantes pour mettre à jour ces composants. Si vous ne voyez aucun fichier Moby, retournez dans les anciennes versions jusqu'à trouver la version la plus récente.

   1. Trouvez le fichier **moby-engine** qui correspond à l’architecture de votre périphérique IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien.

   2. Utilisez le lien copié dans la commande suivante pour installer cette version du moteur Moby :

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Recherchez le fichier **moby-cli** qui correspond à l’architecture de votre périphérique IoT Edge. L’interface de ligne de commande Moby est un composant optionnel, mais peut être utile pendant le développement. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien.

   4. Utilisez le lien copié dans la commande suivante pour installer cette version de l’interface de ligne de commande Moby :

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Chaque version devrait avoir de nouveaux fichiers pour le démon de sécurité IoT Edge et le hsmlib. Utilisez les commandes suivantes pour mettre à jour ces composants.

   1. Trouvez le fichier **libiothsm-std** qui correspond à l’architecture de votre périphérique IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien.

   2. Utilisez le lien copié dans la commande suivante pour installer cette version du hsmlib :

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Trouvez le fichier **iotedge** qui correspond à l’architecture de votre périphérique IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien.

   4. Utilisez le lien copié dans la commande suivante pour installer cette version du démon de sécurité de l’IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Une fois l’installation de IoT Edge réussie, la sortie vous invite à mettre à jour le fichier de configuration. Suivez les étapes de la section suivante pour terminer l’approvisionnement de l’appareil.

## <a name="configure-the-security-daemon"></a>Configurer le démon de sécurité

Configurez le runtime IoT Edge pour lier votre appareil physique à une identité d’appareil existante dans un IoT Hub Azure.

Le démon peut être configuré à l’aide du fichier de configuration situé à l’emplacement `/etc/iotedge/config.yaml`. Le fichier étant protégé en écriture par défaut, vous pouvez avoir besoin d’autorisations élevées pour le modifier.

Un appareil IoT Edge unique peut être approvisionné manuellement à l’aide d’une chaîne de connexion d’appareil fournie par IoT Hub. Vous pouvez également utiliser le service Device Provisioning pour approvisionner automatiquement des appareils. Ce service s’avère particulièrement utile lorsque vous devez approvisionner de nombreux appareils. Choisissez le script d’installation approprié selon votre choix en matière d’approvisionnement.

### <a name="option-1-manual-provisioning"></a>Option 1 : Provisionnement manuel

Pour provisionner manuellement un appareil, vous devez lui fournir une [chaîne de connexion d’appareil](how-to-register-device.md#register-in-the-azure-portal) que vous pouvez créer en inscrivant un nouvel appareil dans votre hub IoT.

Ouvrez le fichier de configuration.

```bash
sudo nano /etc/iotedge/config.yaml
```

Recherchez les configurations de provisionnement du fichier et supprimez les commentaires de la section **Configuration du provisionnement manuel**. Mettez à jour la valeur de **device_connection_string** avec la chaîne de connexion à partir de votre appareil IoT Edge. Assurez-vous que les commentaires des autres sections de provisionnement sont supprimés. Assurez-vous que la ligne **approvisionnement :** n’est pas précédée d’une espace et que les éléments imbriqués sont en retrait de deux espaces.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Pour coller le contenu du Presse-papiers dans Nano, appuyez sur `Shift+Right Click` ou sur `Shift+Insert`.

Enregistrez et fermez le fichier.

   `CTRL + X`, `Y`, `Enter`

Après avoir entré les informations de provisionnement dans le fichier de configuration, redémarrez le démon :

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Option n°2 : Provisionnement automatique

Les appareils IoT Edge peuvent être provisionnés automatiquement à l’aide du [service Azure IoT Hub Device Provisioning (DPS)](../iot-dps/index.yml). Actuellement, ioT Edge prend en charge deux mécanismes d’attestation lors de l’utilisation de l’approvisionnement automatique, mais votre configuration matérielle requise peut également affecter vos choix. Par exemple, les appareils Raspberry Pi ne sont pas fournis avec une puce de Module de plateforme sécurisée (TPM) par défaut. Pour plus d’informations, consultez les articles suivants :

* [Créer et approvisionner un appareil IoT Edge avec un TPM virtuel sur une machine virtuelle Linux](how-to-auto-provision-simulated-device-linux.md)
* [Créer et provisionner un appareil IoT Edge à l’aide de certificats X.509](how-to-auto-provision-x509-certs.md)
* [Créer et approvisionner un appareil IoT Edge à l’aide d’une attestation de clé symétrique](how-to-auto-provision-symmetric-keys.md)

Ces articles vous guident tout au long de la configuration des inscriptions dans DPS et de la génération des certificats ou des clés appropriés pour l’attestation. Quel que soit le mécanisme d’attestation choisi, les informations d’approvisionnement sont ajoutées au fichier de configuration IoT Edge sur votre appareil IoT Edge.

Ouvrez le fichier de configuration.

```bash
sudo nano /etc/iotedge/config.yaml
```

Veuillez rechercher les configurations de provisionnement du fichier, puis supprimez les commentaires de la section appropriée pour votre mécanisme d’attestation. Assurez-vous que les commentaires des autres sections de provisionnement sont supprimés. La ligne d’**approvisionnement :** ne doit pas être précédée d’une espace et les éléments imbriqués doivent être en retrait de deux espaces. Mettez à jour la valeur de **scope_id** avec la valeur de votre instance de service IoT Hub Device Provisioning, et fournissez les valeurs appropriées pour les champs d’attestation.

Attestation TPM :

```yml
# DPS TPM provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "tpm"
    registration_id: "<REGISTRATION_ID>"
```

Attestation X.509 :

```yml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

Attestation de clé symétrique :

```yml
# DPS symmetric key provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "symmetric_key"
    registration_id: "<REGISTRATION_ID>"
    symmetric_key: "<SYMMETRIC_KEY>"
```

Pour coller le contenu du Presse-papiers dans Nano, appuyez sur `Shift+Right Click` ou sur `Shift+Insert`.

Enregistrez et fermez le fichier. `CTRL + X`, `Y`, `Enter`

Après avoir entré les informations de provisionnement dans le fichier de configuration, redémarrez le démon :

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si vous avez utilisé la procédure de **configuration manuelle** dans la section précédente, le runtime IoT Edge doit être correctement provisionné et en cours d’exécution sur votre appareil. Si vous avez utilisé la procédure de **configuration automatique**, vous devez suivre quelques étapes supplémentaires pour que le runtime puisse inscrire à votre place votre appareil avec votre hub IoT. Pour connaître les étapes suivantes, consultez [Créer et provisionner un appareil IoT Edge avec TPM simulé sur une machine virtuelle Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Vous pouvez vérifier l’état du démon IoT Edge :

```bash
systemctl status iotedge
```

Examinez les journaux du démon :

```bash
journalctl -u iotedge --no-pager --no-full
```

Exécutez une vérification automatisée des erreurs de configuration et de mise en réseau les plus courantes :

```bash
sudo iotedge check
```

Tant que vous n’avez pas déployé votre premier module sur IoT Edge sur votre appareil, le module système **$edgeHub** n’est pas déployé sur l’appareil. Par conséquent, la vérification automatisée renverra une erreur pour la vérification de la connectivité `Edge Hub can bind to ports on host`. Vous pouvez ignorer cette erreur, sauf si elle se produit après le déploiement d’un module sur l’appareil.

Enfin, listez les modules en cours d’exécution :

```bash
sudo iotedge list
```

Après avoir installé IoT Edge sur votre appareil, le seul module que vous devez voir en cours d’exécution est **edgeAgent**. Une fois que vous avez créé votre premier déploiement, l’autre module système **$edgeHub** démarre également sur l’appareil. Pour plus d’informations, consultez [Déployer des modules IoT Edge](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Conseils et résolution des problèmes

Vous avez besoin de privilèges élevés pour exécuter les commandes `iotedge`. Après l’installation du runtime, déconnectez-vous de votre machine et reconnectez-vous pour mettre à jour vos autorisations automatiquement. Dans l’intervalle, utilisez **sudo** devant les commandes `iotedge`.

Sur les appareils aux ressources limitées, il est vivement recommandé d’affecter la valeur *false* à la variable d’environnement *OptimizeForPerformance*, conformément aux instructions figurant dans le [guide de résolution des problèmes](troubleshoot.md).

Si votre réseau comporte un serveur proxy, effectuez les étapes décrites dans [Configurer votre appareil IoT Edge pour communiquer via un serveur proxy](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Chargement d’une grande quantité de données à l’aide d’une requête personnalisée, en dessous avec des partitions physiques

De nombreux fabricants d’appareils embarqués livrent des images d’appareils qui contiennent des noyaux Linux personnalisés sans les fonctionnalités nécessaires à la compatibilité de l’exécution des conteneurs. Si vous rencontrez des problèmes lors de l’installation du runtime conteneur Moby recommandé, vous pouvez dépanner votre configuration du noyau Linux en utilisant le script [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) à partir du [référentiel Moby GitHub officiel](https://github.com/moby/moby). Exécutez les commandes suivantes sur l’appareil pour vérifier la configuration de votre noyau :

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Cette commande fournit une sortie détaillée contenant l’état des fonctionnalités du noyau qu’utilise le runtime Moby. Nous vous conseillons de vérifier que tous les éléments sous `Generally Necessary` et `Network Drivers` sont activés pour vous assurer que votre noyau est entièrement compatible avec le runtime Moby.  Si vous avez identifié des fonctionnalités manquantes, vous activez-les en reconstruisant votre noyau à partir des sources et en sélectionnant les modules associés à inclure dans le fichier kernel.config approprié.  De même, si vous utilisez un générateur de configuration du noyau comme `defconfig` ou `menuconfig`, trouvez et activez les fonctionnalités respectives et recompilez votre noyau en conséquence.  Une fois que vous avez déployé votre nouveau noyau modifié, exécutez à nouveau le script check-config pour vérifier que toutes les fonctionnalités requises ont été activées avec succès.

## <a name="uninstall-iot-edge"></a>Désinstaller IoT Edge

Si vous souhaitez supprimer l’installation d’IoT Edge de votre appareil Linux, utilisez les commandes suivantes à partir de la ligne de commande.

Supprimez le runtime IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Lors de la suppression du runtime IoT Edge, les conteneurs qu’il a créés sont arrêtés mais continuent d’exister sur votre appareil. Affichez tous les conteneurs pour voir ceux qui restent.

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

Maintenant que vous disposez d’un appareil IoT Edge approvisionné avec le runtime installé, vous pouvez [déployer des modules IoT Edge](how-to-deploy-modules-portal.md).

Si vous ne parvenez pas à installer correctement le runtime IoT Edge, consultez la page de [résolution des problèmes](troubleshoot.md).

Pour mettre à jour une installation existante vers la dernière version d’IoT Edge, voir [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md).
