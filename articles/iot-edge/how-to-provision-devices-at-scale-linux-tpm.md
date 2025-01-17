---
title: Provisionnement d’un appareil avec un module TPM virtuel sur Linux – Azure IoT Edge
description: Utilisez un module TPM simulé sur un appareil Linux afin de tester le service de provisionnement des appareils Azure pour Azure IoT Edge.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 07/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7d36bace70e8e539d03e75808152a79b17b35423
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063390"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-with-a-tpm-on-linux"></a>Création et provisionnement d’appareils IoT Edge à grande échelle avec un module TPM sur Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Cet article donne des instructions de bout en bout concernant le provisionnement automatique d’un appareil Linux IoT Edge avec un Module de plateforme sécurisée (TPM, Trusted Platform Module). Vous pouvez provisionner automatiquement des appareils Azure IoT Edge à l’aide du [service Device Provisioning (DPS) Azure IoT Hub](../iot-dps/index.yml). Si vous ne connaissez pas le processus de provisionnement automatique, consultez la présentation du [provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Cet article décrit deux méthodologies. Sélectionnez votre préférence en fonction de l’architecture de votre solution :

1. Provisionnement automatique d’un appareil Linux avec du matériel TPM physique. Exemple : le module TPM [Infineon OPTIGA&trade; SLB 9670](https://devicecatalog.azure.com/devices/3f52cdee-bbc4-d74e-6c79-a2546f73df4e).
1. Provisionnement automatique d’une machine virtuelle Linux avec un module TPM simulé exécuté sur un ordinateur de développement Windows sur lequel Hyper-V est activé. Cette solution est recommandée comme scénario de test uniquement, car un module TPM simulé n’offre pas la même sécurité qu’un module TPM physique.

Les instructions varient en fonction de votre méthodologie. Vérifiez que vous vous trouvez dans l’onglet approprié.

# <a name="physical-device"></a>[Appareil physique](#tab/physical-device)

Voici les tâches à effectuer :

1. Récupérez les informations de provisionnement de votre module TPM.
1. Créez une inscription individuelle pour votre appareil dans une instance du Service IoT Hub Device Provisioning.
1. Installez le runtime IoT Edge et connecte l’appareil au hub IoT.

# <a name="virtual-machine"></a>[Machine virtuelle](#tab/virtual-machine)

Voici les tâches à effectuer :

1. Créez une machine virtuelle Linux dans Hyper-V avec un module TPM simulé pour la sécurité matérielle.
1. Récupérez les informations de provisionnement de votre module TPM.
1. Créez une inscription individuelle pour votre appareil dans une instance du Service IoT Hub Device Provisioning.
1. Installez le runtime IoT Edge et connecte l’appareil au hub IoT.

---

## <a name="prerequisites"></a>Prérequis

# <a name="physical-device"></a>[Appareil physique](#tab/physical-device)

* Un hub IoT actif.
* Une instance du service IoT Hub Device Provisioning dans Azure, liée à votre hub IoT.
  * Si vous ne disposez pas d’une instance du service Device Provisioning, vous pouvez suivre les instructions fournies dans les sections [Créer un service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) et [Lier le hub IoT et votre service Device Provisioning](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) du guide de démarrage rapide du service IoT Hub Device Provisioning.
  * Après avoir lancé l’exécution du service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge.

# <a name="virtual-machine"></a>[Machine virtuelle](#tab/virtual-machine)

* Un hub IoT actif.
* Une instance du service IoT Hub Device Provisioning dans Azure, liée à votre hub IoT.
  * Si vous ne disposez pas d’une instance du service Device Provisioning, vous pouvez suivre les instructions fournies dans les sections [Créer un service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) et [Lier le hub IoT et votre service Device Provisioning](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) du guide de démarrage rapide du service IoT Hub Device Provisioning.
  * Après avoir lancé l’exécution du service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge.
* Une machine de développement Windows avec [Hyper-V activé](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Cet article utilise Windows 10 exécutant une machine virtuelle Ubuntu Server.

---

> [!NOTE]
> TPM 2.0 est requis pour utiliser l’attestation TPM avec le Service DPS.
>
> Il est possible de créer des inscriptions DPS individuelles et non des inscriptions DPS groupées avec un module TPM.

## <a name="set-up-your-device"></a>Configurer votre appareil

# <a name="physical-device"></a>[Appareil physique](#tab/physical-device)

Si vous utilisez un appareil Linux physique avec un module TPM, il n’y a pas d’étapes supplémentaires à suivre pour configurer votre appareil.

Vous pouvez continuer.

# <a name="virtual-machine"></a>[Machine virtuelle](#tab/virtual-machine)

Dans cette section, vous créez une machine virtuelle Linux sur Hyper-V. Vous configurez cette machine avec un module TPM simulé permettant de tester le fonctionnement du provisionnement automatique avec IoT Edge.

> [!TIP]
> Si vous utilisez une machine virtuelle, vous allez y effectuer de nombreux copier-coller tout au long de cet article. Ces opérations ne sont pas faciles dans l’application de connexion Hyper-V Manager. Vous pouvez vous connecter une fois à la machine virtuelle avec le Gestionnaire Hyper-V pour récupérer son adresse IP. Exécutez d’abord `sudo apt install net-tools`, puis `hostname -I`. Vous pouvez ensuite utiliser l’adresse IP pour vous connecter via SSH : `ssh <username>@<ipaddress>`.

### <a name="create-a-virtual-switch"></a>Créer un commutateur virtuel

Un commutateur virtuel permet à votre machine virtuelle de se connecter à un réseau physique.

1. Ouvrez Hyper-V Manager sur votre ordinateur Windows.

1. Dans le menu **Actions**, sélectionnez **Gestionnaire de commutateur virtuel**.

1. Choisissez un commutateur virtuel **externe**, puis sélectionnez **Créer un commutateur virtuel**.

1. Donnez un nom à votre nouveau commutateur virtuel, par exemple **EdgeSwitch**. Vérifiez que le type de connexion est défini sur **Réseau externe**, puis sélectionnez **Ok**.

1. Une fenêtre contextuelle vous avertit que la connectivité réseau peut être interrompue. Cliquez sur **Oui** pour continuer.

> [!TIP]
> Si vous constatez des erreurs lors de la création du commutateur virtuel, vérifiez qu’aucun autre commutateur n’utilise l’adaptateur Ethernet ou ne porte ce nom.

### <a name="create-the-virtual-machine"></a>Créer la machine virtuelle

Créez une machine virtuelle à partir d’un fichier image démarrable.

1. Téléchargez un fichier image de disque à utiliser pour votre machine virtuelle et enregistrez-le localement. Par exemple, [Ubuntu Server 18.04](http://releases.ubuntu.com/18.04/). Pour plus d’informations sur les systèmes d’exploitation pris en charge pour les appareils IoT Edge, consultez [Systèmes pris en charge Azure IoT Edge](/azure/iot-edge/support).

1. Dans Hyper-V Manager, sélectionnez **Action** > **Créer** > **Machine virtuelle** dans le menu **Actions**.

1. Exécutez l’**Assistant Nouvel ordinateur virtuel** avec les configurations spécifiques suivantes :

   1. **Spécifier la génération** : sélectionnez **Génération 2**. La virtualisation imbriquée est activée sur les machines virtuelles de génération 2 : celle-ci est nécessaire pour exécuter IoT Edge sur une machine virtuelle.
   1. **Configurer la mise en réseau** : définissez la valeur de **Connexion** sur le commutateur virtuel que vous avez créé à la section précédente.
   1. **Options d’installation** : sélectionnez **Installer un système d’exploitation à partir d’un fichier image de démarrage** et accédez au fichier image de disque que vous avez enregistré localement.

1. Sélectionnez **Terminer** dans l’Assistant pour créer la machine virtuelle.

La création de la nouvelle machine virtuelle peut prendre plusieurs minutes.

### <a name="enable-virtual-tpm"></a>Activer le TPM virtuel

Une fois votre machine virtuelle créée, ouvrez ses paramètres pour activer le module TPM virtuel qui vous permet de provisionner automatiquement l’appareil.

1. Dans le Gestionnaire Hyper-V, cliquez avec le bouton droit sur la machine virtuelle, puis sélectionnez **Paramètres**.

1. Naviguez vers **Sécurité**.

1. Décochez la case **Activer le démarrage sécurisé**.

1. Cochez la case **Activer le module de plateforme sécurisée**.

1. Cliquez sur **OK**.

### <a name="start-the-virtual-machine"></a>Démarrage de la machine virtuelle

Démarrez votre machine virtuelle pour suivre le processus d’installation.

1. Dans le Gestionnaire Hyper-V, démarrez votre machine virtuelle et connectez-vous-y.

1. Suivez les invites au sein de la machine virtuelle pour terminer le processus d’installation et redémarrez la machine.

Une fois l’installation terminée et la connexion à votre machine virtuelle rétablie, vous pouvez continuer.

---

## <a name="retrieve-provisioning-information-for-your-tpm"></a>Récupération des informations de provisionnement de votre module TPM

Dans cette section, vous allez créer un outil permettant de récupérer **l’ID d’inscription** et la **Paire de clés de type EK (Endorsement key)** de votre module TPM.

1. Connectez-vous à votre appareil, puis suivez la procédure indiquée dans [Configuration d’un environnement de développement Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) afin d’installer et de générer le kit Azure IoT device SDK pour C.

1. Exécutez les commandes suivantes pour créer l’outil SDK permettant de récupérer les informations de provisionnement des appareils de votre module TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. La fenêtre Sortie affiche **l’ID d’inscription** et la **Paire de clés de type EK (Endorsement Key)** de l’appareil. Copiez ces valeurs. Vous vous en servirez plus tard, au moment de créer une inscription individuelle pour votre appareil dans le Service DPS.

> [!TIP]
> Si vous ne souhaitez pas utiliser l’outil SDK pour récupérer les informations, vous devez trouver un autre moyen d’obtenir les informations de provisionnement. La **Paire de clés de type EK (Endorsement Key)** , propre à chaque puce TPM, est fournie par le fabricant de la puce TPM associée. Vous pouvez dériver un **ID d'inscription** unique pour votre appareil TPM, en créant par exemple un code de hachage SHA-256 pour la paire de clés de type EK.

Une fois que vous disposez de votre ID d’inscription et de votre paire de clés de type EK, vous pouvez continuer.

## <a name="create-a-dps-enrollment"></a>Créer une inscription au service Device Provisioning

Récupérez les informations de provisionnement de votre module TPM et utilisez-les pour créer une inscription individuelle dans le Service Device Provisioning.

Lorsque vous créez une inscription auprès du service Device Provisioning, vous avez la possibilité de déclarer un **État initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des différentes métriques utilisées dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](how-to-deploy-at-scale.md).

> [!TIP]
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions individuelles à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne un appareil IoT Edge.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance du Service IoT Hub Device Provisioning.

1. Sous **Paramètres**, sélectionnez **Gérer les inscriptions**.

1. Sélectionnez **Ajouter une inscription individuelle**, puis suivez cette procédure pour configurer l’inscription :

   1. Pour **Mécanisme**, sélectionnez **TPM**.

   1. Spécifiez la **paire de clés de type EK (Endorsement Key)** et l’**ID d’inscription** que vous avez copiés sur votre machine virtuelle ou votre appareil physique.

   1. Fournissez un ID pour votre appareil si vous le souhaitez. Si vous ne fournissez pas un ID d’appareil, l’ID d’inscription est utilisé.

   1. Sélectionnez **True** pour déclarer que votre machine virtuelle ou votre appareil physique est un appareil IoT Edge.

   1. Choisissez le hub IoT lié auquel vous voulez connecter votre appareil, ou sélectionnez **Lier à un nouveau hub IoT**. Vous pouvez choisir plusieurs hubs : l’appareil sera affecté à l’un d’entre eux en fonction de la stratégie d’attribution sélectionnée.

   1. Ajoutez une valeur de balise à l’**État initial du jumeau d’appareil** si vous le souhaitez. Vous pouvez utiliser des balises pour cibler des groupes d’appareils lors du déploiement de module. Pour plus d’informations, consultez [Déploiement de modules IoT Edge à grande échelle](how-to-deploy-at-scale.md).

   1. Sélectionnez **Enregistrer**.

Maintenant qu’une inscription existe pour cet appareil, le runtime IoT Edge peut provisionner automatiquement l’appareil lors de l’installation.

## <a name="install-the-iot-edge-runtime"></a>Installer le runtime IoT Edge

Dans cette section, vous allez préparer votre machine virtuelle ou votre appareil physique Linux à IoT Edge. Ensuite, vous allez installer IoT Edge.

Vous devez effectuer deux étapes sur votre appareil pour qu’il soit prêt à installer le runtime IoT Edge. Votre appareil a besoin d’un accès aux packages d’installation Microsoft, et un moteur de conteneur doit être installé.

### <a name="access-the-microsoft-installation-packages"></a>Accéder aux packages d’installation Microsoft

Votre appareil doit avoir accès aux packages d’installation Microsoft.

1. Installez la configuration du référentiel qui correspond au système d’exploitation de votre appareil.

   * **Ubuntu Sever 18.04** :

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
      ```

   * **Raspberry Pi OS Stretch** :

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
      ```

1. Copiez la liste générée dans le répertoire sources.list.d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Installez la clé publique Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trust.gpg.d/
   ```

> [!NOTE]
> Les packages logiciels Azure IoT Edge sont soumis aux termes du contrat de licence situés dans chaque package (`usr/share/doc/{package-name}` ou dans le répertoire `LICENSE`). Lisez les termes du contrat de licence avant d’utiliser un package. Le fait d’installer et d’utiliser un package revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package en question.

### <a name="install-a-container-engine"></a>Installer un moteur de conteneur

Azure IoT Edge s’appuie sur un runtime de conteneur compatible avec OCI. Dans les scénarios de production, nous vous recommandons d’utiliser le moteur Moby. Le moteur Moby est le seul moteur de conteneur officiellement pris en charge avec Azure IoT Edge. Les images conteneur Docker CE/EE sont compatibles avec le runtime Moby.

1. Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

1. Installez le moteur Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

   > [!TIP]
   > Si des erreurs surviennent lors de l’installation du moteur de conteneur Moby, vérifiez la compatibilité de votre noyau Linux avec Moby. Certains fabricants d’appareils embarqués livrent des images d’appareils qui contiennent des noyaux Linux personnalisés sans les fonctionnalités nécessaires à la compatibilité du moteur de conteneur. Exécutez la commande suivante, qui utilise le [script check-config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) fourni par Moby, pour vérifier la configuration de votre noyau.
   >
   >   ```bash
   >   curl -ssl https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   >   chmod +x check-config.sh
   >   ./check-config.sh
   >   ```
   >
   > Dans la sortie du script, vérifiez que tous les éléments sous `Generally Necessary` et `Network Drivers` sont activés. Si vous ne disposez pas de certaines fonctionnalités, activez-les en régénérant votre noyau à partir des sources et en sélectionnant les modules associés à inclure dans le noyau .config approprié. De même, si vous utilisez un générateur de configuration du noyau comme `defconfig` ou `menuconfig`, trouvez et activez les fonctionnalités respectives et recompilez votre noyau en conséquence. Une fois que vous avez déployé votre nouveau noyau modifié, exécutez à nouveau le script check-config pour vérifier que toutes les fonctionnalités requises ont été activées avec succès.

### <a name="install-iot-edge"></a>Installer IoT Edge

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Le démon de sécurité IoT Edge fournit et gère les standards de sécurité sur l’appareil IoT Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge.

La procédure de cette section représente le processus classique d’installation de la dernière version sur un appareil disposant d’une connexion Internet. Si vous devez installer une version spécifique, telle qu’une préversion, ou si vous devez installer en mode hors connexion, suivez les étapes d’installation hors connexion ou propres à une version.

1. Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

1. Installez IoT Edge versio 1.1* avec le package **libiothsm-std**.

   ```bash
   sudo apt-get install iotedge
   ```

   > [!NOTE]
   > *IoT Edge version 1.1 est la branche de support à long terme d’IoT Edge. Si vous exécutez une version antérieure, nous vous recommandons d’installer ou de mettre à jour vers le correctif le plus récent, car les versions antérieures ne sont plus prises en charge.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Le service IoT Edge fournit et gère les standards de sécurité sur l’appareil IoT Edge. Le service se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge.

Le service IoT Identity a été introduit avec la version 1.2 d’IoT Edge. Ce service gère l’approvisionnement et la gestion des identités pour IoT Edge et pour d’autres composants d’appareil qui doivent communiquer avec IoT Hub.

La procédure de cette section représente le processus classique d’installation de la dernière version sur un appareil disposant d’une connexion Internet. Si vous devez installer une version spécifique, telle qu’une préversion, ou si vous devez installer en mode hors connexion, suivez les étapes d’installation hors connexion ou propres à une version.

Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

Vérifiez les versions d’IoT Edge et le service d’identité IoT disponibles.

   ```bash
   apt list -a aziot-edge aziot-identity-service
   ```

Pour installer la dernière version d’IoT Edge et le package du service d’identité IoT, utilisez la commande suivante :

   ```bash
   sudo apt-get install aziot-edge
   ```

Ou bien, si vous choisissez d’installer une autre version d’IoT Edge que la version la plus récente, veillez à installer la même version pour les services `aziot-edge` et `aziot-identity-service`.

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-the-device-with-provisioning-information"></a>Configurer l’appareil avec des informations de provisionnement

Une fois que le runtime est installé sur votre appareil, configurez l’appareil avec les informations qu’il utilise pour se connecter au service Device Provisioning (DPS) et à IoT Hub.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Récupérez **l’Étendue de l’ID** DPS et **l’ID d’inscription** de l’appareil qui ont été recueillis.

1. Ouvrez le fichier de configuration sur votre appareil IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Recherchez la section de configuration du provisionnement dans le fichier. Supprimez les marques de commentaire des lignes du provisionnement TPM et de toutes les autres lignes de provisionnement.

   La ligne `provisioning:` ne doit pas être précédée d’un espace. Par ailleurs, les éléments imbriqués doivent être en retrait de deux espaces.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

1. Mettez à jour les valeurs de `scope_id` et de `registration_id` avec vos informations sur l’appareil et le Service Device Provisioning. `scope_id` correspond à **l’Étendue de l’ID** de la page de vue d’ensemble de votre instance DPS.

1. Si vous le souhaitez, utilisez les lignes `always_reprovision_on_startup` ou `dynamic_reprovisioning` pour configurer le comportement de reprovisionnement de votre appareil. Si un appareil est configuré pour être reprovisionné au démarrage, le provisionnement est toujours tenté avec le Service DPS dans un premier temps. En cas d’échec, il bascule vers la sauvegarde de provisionnement. Si un appareil est configuré pour se provisionner dynamiquement lui-même, IoT Edge redémarre et effectue le reprovisionnement lorsqu’un événement de reprovisionnement est détecté. Pour plus d’informations, consultez [Concepts du reprovisionnement d’appareils IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Enregistrez et fermez le fichier.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Récupérez **l’Étendue de l’ID** DPS et **l’ID d’inscription** de l’appareil qui ont été recueillis.

1. Créez un fichier de configuration pour votre appareil à partir d’un fichier de modèle fourni dans le cadre de l’installation d’IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Ouvrez le fichier de configuration sur l’appareil IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Recherchez la section des configurations de provisionnement dans le fichier. Supprimez les marques de commentaire des lignes du provisionnement TPM et de toutes les autres lignes de provisionnement.

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "<REGISTRATION_ID>"
   ```

1. Mettez à jour les valeurs de `id_scope` et de `registration_id` avec vos informations sur l’appareil et le Service Device Provisioning. `scope_id` correspond à **l’Étendue de l’ID** de la page de vue d’ensemble de votre instance DPS.

1. Si vous le souhaitez, recherchez la section auto-reprovisioning mode du fichier. Utilisez le paramètre `auto_reprovisioning_mode` pour configurer le comportement de reprovisionnement de votre appareil sur `Dynamic`, `AlwaysOnStartup` ou `OnErrorOnly`. Pour plus d’informations, consultez [Concepts du reprovisionnement d’appareils IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Enregistrez et fermez le fichier.

:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>Donner à IoT Edge l’accès au TPM

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Le runtime IoT Edge a besoin d’accéder au module TPM pour provisionner automatiquement votre appareil.

Vous pouvez accorder au TPM un accès au runtime IoT Edge en substituant les paramètres système afin que le service `iotedge`dispose des privilèges root. Si vous ne souhaitez pas élever les privilèges de service, vous pouvez également utiliser les étapes suivantes pour fournir manuellement un accès au TPM.

1. Créez une règle qui donne au runtime IoT Edge l’accès à tpm0 et à tpmrm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. Ouvrez le fichier de règles.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. Copiez les informations d’accès suivantes dans le fichier de règles. Le `tpmrm0` n’est peut-être pas présent sur les appareils utilisant un noyau antérieur à 4.12. Les appareils qui n’ont pas de tpmrm0 ignorent cette règle en toute sécurité.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="iotedge", MODE="0600"
   ```

1. Enregistrez et fermez le fichier.

1. Déclenchez le système udev pour évaluer la nouvelle règle.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. Vérifiez que la règle a bien été appliquée.

   ```bash
   ls -l /dev/tpm*
   ```

   En cas de réussite, la sortie se présente ainsi :

   ```output
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpm0
   crw------- 1 iotedge root 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   Si vous constatez que les autorisations appropriées n’ont pas été appliquées, essayez de redémarrer votre ordinateur pour actualiser udev.

1. Redémarrez le runtime IoT Edge afin qu’il récupère toutes les modifications de configuration que vous avez effectuées sur l’appareil.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Le runtime IoT Edge s’appuie sur un service de module de plateforme sécurisée (TPM) qui donne accès au module TPM d’un appareil. Le service a besoin d’accéder au module TPM pour provisionner automatiquement votre appareil.

Vous pouvez accorder au TPM en substituant les paramètres système afin que le service `aziottpm`dispose des privilèges root. Si vous ne souhaitez pas élever les privilèges de service, vous pouvez également utiliser les étapes suivantes pour fournir manuellement un accès au TPM.

1. Créez une règle qui donne au runtime IoT Edge l’accès à tpm0 et à tpmrm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

1. Ouvrez le fichier de règles.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

1. Copiez les informations d’accès suivantes dans le fichier de règles. Le `tpmrm0` n’est peut-être pas présent sur les appareils utilisant un noyau antérieur à 4.12. Les appareils qui n’ont pas de tpmrm0 ignorent cette règle en toute sécurité.

   ```input
   # allow aziottpm access to tpm0 and tpmrm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0660"
   KERNEL=="tpmrm0", SUBSYSTEM=="tpmrm", OWNER="aziottpm", MODE="0660"
   ```

1. Enregistrez et fermez le fichier.

1. Déclenchez le système udev pour évaluer la nouvelle règle.

   ```bash
   /bin/udevadm trigger --subsystem-match=tpm --subsystem-match=tpmrm
   ```

1. Vérifiez que la règle a bien été appliquée.

   ```bash
   ls -l /dev/tpm*
   ```

   En cas de réussite, la sortie se présente ainsi :

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpmrm0
   ```

   Si vous constatez que les autorisations appropriées n’ont pas été appliquées, essayez de redémarrer votre ordinateur pour actualiser udev.

1. Appliquez les modifications de configuration que vous avez apportées à l’appareil.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>Redémarrer IoT Edge et vérifier la réussite de l’installation

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Si vous ne l’avez pas déjà fait, redémarrez le runtime IoT Edge pour qu’il récupère toutes les modifications de configuration que vous avez effectuées sur l’appareil.

   ```bash
   sudo systemctl restart iotedge
   ```

Vérifiez que le runtime IoT Edge est en cours d’exécution.

   ```bash
   sudo systemctl status iotedge
   ```

Examinez les journaux d’activité du démon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Si vous constatez des erreurs de provisionnement, il est possible que les modifications de configuration ne soient pas encore prises en compte. Essayez de redémarrer le démon IoT Edge.

   ```bash
   sudo systemctl daemon-reload
   ```

Ou bien, essayez de redémarrer votre machine virtuelle pour voir si les modifications sont prises en compte à un redémarrage à zéro.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Si vous ne l’avez pas déjà fait, appliquez les modifications de configuration que vous avez effectuées sur l’appareil.

   ```bash
   sudo iotedge config apply
   ```

Vérifiez que le runtime IoT Edge est en cours d’exécution.

   ```bash
   sudo iotedge system status
   ```

Examinez les journaux d’activité du démon.

   ```cmd/sh
   sudo iotedge system logs
   ```

Si vous constatez des erreurs de provisionnement, il est possible que les modifications de configuration ne soient pas encore prises en compte. Essayez de redémarrer le démon IoT Edge.

   ```bash
   sudo systemctl daemon-reload
   ```

Ou bien, essayez de redémarrer votre machine virtuelle pour voir si les modifications sont prises en compte à un redémarrage à zéro.
:::moniker-end
<!-- end 1.2 -->

Si le runtime a été démarré correctement, vous pouvez accéder à votre hub IoT et voir que votre nouvel appareil a été automatiquement provisionné. Votre appareil est maintenant prêt à exécuter des modules IoT Edge.

Répertoriez les modules en cours d’exécution.

```cmd/sh
iotedge list
```

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service Device Provisioning a été utilisée. Dans le Portail Azure, accédez à l’instance du service Device Provisioning. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste.

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription DPS vous permet de définir les étiquettes d’ID et de jumeau d’appareil au cours du provisionnement du nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md).
