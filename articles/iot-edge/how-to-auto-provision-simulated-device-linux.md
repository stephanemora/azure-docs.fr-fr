---
title: Provisionner automatiquement des appareils Linux avec DPS - Azure IoT Edge | Microsoft Docs
description: Utiliser un module de plateforme sécurisée (TPM) simulé sur une machine virtuelle Linux afin de tester le service de provisionnement d’appareils Azure pour Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5ab85a8fb56789dbf3ecd6cf1cbc63e338615915
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439122"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Créer et provisionner un appareil IoT Edge avec un TPM virtuel sur une machine virtuelle Linux

Les appareils Azure IoT Edge peuvent être provisionnés automatiquement à l’aide du [service Device Provisioning](../iot-dps/index.yml). Si vous ne connaissez pas le processus de provisionnement automatique, révisez les [concepts du provisionnement automatique](../iot-dps/concepts-auto-provisioning.md) avant de poursuivre. 

Cet article montre comment tester le provisionnement automatique sur un appareil Edge simulé avec les étapes suivantes : 

* Création d’une machine virtuelle Linux dans Hyper-V avec un module de plateforme sécurisée (TPM) simulé pour la sécurité du matériel.
* Création d’une instance du service IoT Hub Device Provisioning.
* Création d’une inscription individuelle pour l’appareil
* Installation du runtime IoT Edge et connexion de l’appareil à IoT Hub

Les étapes décrites dans cet article sont conçues à des fins de test.

## <a name="prerequisites"></a>Prérequis

* Une machine de développement Windows avec [Hyper-V activé](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Cet article utilise Windows 10 exécutant une machine virtuelle Ubuntu Server. 
* Un hub IoT actif. 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Créer une machine virtuelle Linux avec un TPM virtuel

Dans cette section, vous créez une machine virtuelle Linux sur Hyper-V. Vous avez configuré cette machine avec un TPM simulé, afin de pouvoir l’utiliser pour tester le fonctionnement du provisionnement automatique avec IoT Edge. 

### <a name="create-a-virtual-switch"></a>Créer un commutateur virtuel

Un commutateur virtuel permet à votre machine virtuelle de se connecter à un réseau physique.

1. Ouvrez le Gestionnaire Hyper-V sur votre machine Windows. 

2. Dans le menu **Actions**, sélectionnez **Gestionnaire de commutateur virtuel**. 

3. Choisissez un commutateur virtuel **externe**, puis sélectionnez **Créer un commutateur virtuel**. 

4. Nommez votre nouveau commutateur virtuel, par exemple **EdgeSwitch**. Vérifiez que le type de connexion est défini sur **Réseau externe**, puis sélectionnez **Ok**.

5. Une fenêtre contextuelle vous avertit que la connectivité réseau peut être interrompue. Cliquez sur **Oui** pour continuer. 

Si vous constatez des erreurs lors de la création du commutateur virtuel, assurez-vous qu’aucun autre commutateur n’utilise l’adaptateur ethernet, et qu’aucun autre commutateur n’utilise ce nom. 

### <a name="create-virtual-machine"></a>Créer une machine virtuelle

1. Téléchargez un fichier image de disque à utiliser pour votre machine virtuelle et enregistrez-le localement. Par exemple, [Ubuntu Server](https://www.ubuntu.com/download/server). 

2. Toujours dans le Gestionnaire Hyper-V, sélectionnez **Nouveau** > **Machine virtuelle** dans le menu **Actions**.

3. Exécutez l’**Assistant Nouvel ordinateur virtuel** avec les configurations spécifiques suivantes :

   1. **Spécifier la génération** : sélectionnez **Génération 2**. La virtualisation imbriquée est activée sur les machines virtuelles de génération 2 : celle-ci est nécessaire pour exécuter IoT Edge sur une machine virtuelle.
   2. **Configurer la mise en réseau** : définissez la valeur de **Connexion** sur le commutateur virtuel que vous avez créé à la section précédente. 
   3. **Options d’installation** : sélectionnez **Installer un système d’exploitation à partir d’un fichier image de démarrage** et accédez au fichier image de disque que vous avez enregistré localement.

4. Sélectionnez **Terminer** dans l’Assistant pour créer la machine virtuelle.

La création de la nouvelle machine virtuelle peut prendre plusieurs minutes. 

### <a name="enable-virtual-tpm"></a>Activer le TPM virtuel

Une fois que votre machine virtuelle est créée, ouvrez ses paramètres pour activer le module de plateforme sécurisée (TPM) approuvé virtuel qui vous permet de provisionner automatiquement l’appareil. 

1. Sélectionnez la machine virtuelle, puis ouvrez ses **Paramètres**.

2. Naviguez vers **Sécurité**. 

3. Décochez la case **Activer le démarrage sécurisé**.

4. Cochez la case **Activer le module de plateforme sécurisée**. 

5. Cliquez sur **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Démarrer la machine virtuelle et collecter les données TPM

Dans la machine virtuelle, créez un outil SDK C que vous pouvez utiliser pour récupérer l’**ID d’inscription** et la **Paire de clés de type EK** de l’appareil. 

1. Démarrez votre machine virtuelle et connectez-vous à celle-ci.

2. Suivez les invites au sein de la machine virtuelle pour terminer le processus d’installation et redémarrez la machine. 

3. Connectez-vous à votre machine virtuelle, puis suivez les étapes de [Configurer un environnement de développement Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) pour installer et générer Azure IoT device SDK pour C. 

   >[!TIP]
   >Dans cet article, vous allez copier et coller depuis et vers la machine virtuelle, ce qui n’est pas facile via l’application de connexion du Gestionnaire Hyper-V. Vous pouvez vous connecter une fois à la machine virtuelle via le Gestionnaire Hyper-V pour récupérer son adresse IP : `ifconfig`. Vous pouvez ensuite utiliser l’adresse IP pour vous connecter via SSH : `ssh <username>@<ipaddress>`.

4. Exécutez les commandes suivantes pour créer un outil SDK C qui récupère les informations de provisionnement de votre appareil. 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```
   >[!TIP]
   >Si vous testez avec le simulateur TPM, vous devez placer un paramètre supplémentaire `-Duse_tpm_simulator:BOOL=ON` pour l’activer. La commande complète sera `cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..`.

5. Copiez les valeurs de **ID d’inscription** et de **Paire de clés de type EK** . Vous utilisez ces valeurs pour créer une inscription individuelle de votre appareil dans le service Device Provisioning. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurer le service IoT Hub Device Provisioning

Créez une nouvelle instance du service IoT Hub Device Provisioning dans Azure et liez-la à votre hub IoT. Vous pouvez suivre les instructions dans [Configurer le service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md).

Après avoir lancé l’exécution du service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Créer une inscription au service Device Provisioning

Récupérez les informations de provisionnement à partir de votre machine virtuelle et utilisez-les pour créer une inscription individuelle dans le service Device Provisioning. 

Lorsque vous créez une inscription auprès du service Device Provisioning, vous avez la possibilité de déclarer un **État initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des métriques dont vous avez besoin dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](how-to-deploy-monitor.md). 


1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning. 

2. Sous **Paramètres**, sélectionnez **Gérer les inscriptions**. 

3. Sélectionnez **Ajouter une inscription individuelle** et suivez ces étapes pour configurer l’inscription :  

   1. Pour **Mécanisme**, sélectionnez **TPM**. 
   
   2. Spécifiez la **Paire de clés de type EK (Endorsement Key)** et l’**ID d’inscription** que vous avez copiés à partir de votre machine virtuelle.
   
   3. Sélectionnez **Vrai** pour déclarer que cette machine virtuelle est un appareil IoT Edge. 
   
   4. Choisissez le **Hub IoT** lié auquel vous voulez connecter votre appareil. Vous pouvez choisir plusieurs hubs : l’appareil sera affecté à l’un d’entre eux en fonction de la stratégie d’allocation sélectionnée. 
   
   5. Fournissez un ID pour votre appareil si vous le souhaitez. Vous pouvez utiliser l’ID d’appareil pour cibler un appareil individuel lors du déploiement de module. Si vous ne fournissez pas un ID d’appareil, l’ID d’inscription est utilisé.
   
   6. Ajoutez une valeur de balise à l’**État initial du jumeau d’appareil** si vous le souhaitez. Vous pouvez utiliser des balises pour cibler des groupes d’appareils lors du déploiement de module. Par exemple : 

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Sélectionnez **Enregistrer**. 

Maintenant qu’une inscription existe pour cet appareil, le runtime IoT Edge peut provisionner automatiquement l’appareil lors de l’installation. 

## <a name="install-the-iot-edge-runtime"></a>Installer le runtime IoT Edge

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Ses composants s’exécutent dans des conteneurs et vous permettent de déployer des conteneurs supplémentaires sur l’appareil, pour que vous puissiez exécuter du code en périphérie. Installez le runtime IoT Edge sur votre machine virtuelle. 

Procurez-vous l’**Étendue de l’ID** de votre service Device Provisioning et l’**ID d’inscription** de votre appareil avant de commencer l’article qui correspond à votre type d’appareil. Si vous avez installé l’exemple de Ubuntu Server, utilisez les instructions **x64**. Veillez à configurer le runtime IoT Edge pour le provisionnement automatique, et non manuel. 

* [Installer le runtime Azure IoT Edge sur Linux (x64)](how-to-install-iot-edge-linux.md)
* [Installer le runtime Azure IoT Edge sur Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Donner à IoT Edge l’accès au TPM

Pour provisionner automatiquement votre appareil, le runtime IoT Edge a besoin d’accéder au TPM. 

Vous pouvez accorder au TPM un accès au runtime IoT Edge en substituant les paramètres système afin que le service **iotedge** dispose des privilèges root. Si vous ne souhaitez pas élever les privilèges de service, vous pouvez également utiliser les étapes suivantes pour fournir manuellement un accès au TPM. 

1. Trouvez le chemin au module matériel TPM sur votre appareil et enregistrez-le en tant que variable locale. 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Créez une règle qui donne au runtime IoT Edge l’accès à tpm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Ouvrez le fichier de règles. 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Copiez les informations d’accès suivantes dans le fichier de règles. 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Enregistrez et fermez le fichier. 

6. Déclenchez le système udev pour évaluer la nouvelle règle. 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Vérifiez que la règle a bien été appliquée.

   ```bash
   ls -l /dev/tpm0
   ```

   La sortie correcte ressemble à ceci :

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Si vous constatez que les autorisations appropriées n’ont pas été appliquées, essayez de redémarrer votre ordinateur pour actualiser udev. 

8. Ouvrez le fichier de substitution du runtime IoT Edge. 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. Ajoutez le code suivant pour établir une variable d’environnement TPM.

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

10. Enregistrez et fermez le fichier.

11. Vérifiez que le remplacement s’est bien produit.

    ```bash
    sudo systemctl cat iotedge.service
    ```

    La sortie correcte affiche les variables de service par défaut **iotedge**, puis indique la variable d’environnement que vous avez définie dans **override.conf**. 

12. Rechargez les paramètres.

    ```bash
    sudo systemctl daemon-reload
    ```

## <a name="restart-the-iot-edge-runtime"></a>Redémarrer le runtime IoT Edge

Redémarrez le runtime IoT Edge afin qu’il récupère toutes les modifications de configuration que vous avez effectuées sur l’appareil. 

   ```bash
   sudo systemctl restart iotedge
   ```

Vérifiez que le runtime IoT Edge est en cours d’exécution. 

   ```bash
   sudo systemctl status iotedge
   ```

Si vous constatez des erreurs de provisionnement, il est possible que les modifications de configuration ne soient pas encore prises en compte. Essayez de redémarrer le démon IoT Edge. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
Ou bien, essayez de redémarrer votre machine virtuelle pour voir si les modifications sont prises en compte à un redémarrage à zéro. 

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si le runtime a été démarré correctement, vous pouvez accéder à votre hub IoT et voir que votre nouvel appareil a été automatiquement provisionné. Votre appareil est maintenant prêt à exécuter des modules IoT Edge. 

Vérifiez l’état du démon IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examinez les journaux d’activité du démon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Répertoriez les modules en cours d’exécution.

```cmd/sh
iotedge list
```

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service Device Provisioning a été utilisée. Dans le Portail Azure, accédez à l’instance du service Device Provisioning. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste. 

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service Device Provisioning vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-monitor.md) ou [d’Azure CLI](how-to-deploy-monitor-cli.md).
