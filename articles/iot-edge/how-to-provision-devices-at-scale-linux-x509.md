---
title: Créer et provisionner des appareils IoT Edge à grande échelle à l’aide de certificats X.509 sur Linux - Azure IoT Edge | Microsoft Docs
description: Utilisez des certificats X.509 pour tester le provisionnement d’appareils à grande échelle pour Azure IoT Edge avec le service Device Provisioning.
author: v-tcassi
ms.author: v-tcassi
ms.date: 08/12/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 2dc45c3b088dc25a39496280b1e612a27c9dd69d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700676"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-x509-certificates"></a>Créer et provisionner des appareils IoT Edge à grande échelle sur Linux à l’aide de certificats X.509

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Cet article fournit des instructions complètes pour le provisionnement automatique d’un ou plusieurs appareils IoT Edge Linux à l’aide de certificats X.509. Vous pouvez provisionner automatiquement des appareils Azure IoT Edge à l’aide du [service Device Provisioning (DPS) Azure IoT Hub](../iot-dps/index.yml). Si vous ne connaissez pas le processus de provisionnement automatique, consultez la [présentation du provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Voici les tâches à effectuer :

1. Génération de certificats et de clés
1. Création d’une **inscription individuelle** pour un seul appareil ou d’une **inscription de groupe** pour un ensemble d’appareils
1. Installation du runtime IoT Edge et inscription de l’appareil auprès d’IoT Hub

Utiliser des certificats X.509 comme mécanisme d’attestation est un excellent moyen de mettre à l’échelle la production et de simplifier le provisionnement des appareils. Les certificats X.509 sont généralement organisés en une chaîne d’approbation de confiance. À partir d’un certificat racine auto-signé ou approuvé, chaque certificat de la chaîne signe le certificat inférieur suivant. Ce modèle crée une chaîne déléguée de confiance depuis le certificat racine jusqu’au certificat « feuille » final installé sur un appareil, en passant par chaque certificat intermédiaire.

## <a name="prerequisites"></a>Prérequis

* Un hub IoT actif.
* Un appareil Linux physique ou virtuel faisant office d’appareil IoT Edge.
* La dernière version de [Git](https://git-scm.com/download/) installée.
* Une instance du service IoT Hub Device Provisioning dans Azure, liée à votre hub IoT.
  * Si vous ne disposez pas d’une instance du service Device Provisioning, vous pouvez suivre les instructions fournies dans les sections [Créer un service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) et [Lier le hub IoT et votre service Device Provisioning](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) du guide de démarrage rapide du service IoT Hub Device Provisioning.
  * Après avoir lancé l’exécution du service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge.

## <a name="generate-device-identity-certificates"></a>Générer des certificats d’identité d’appareil

Le certificat d’identité d’appareil est un certificat feuille qui se connecte par le biais d’une chaîne de certificats de confiance au certificat d’autorité de certification X.509 supérieur. Le nom commun (CN) du certificat d’identité d’appareil doit être défini sur l’ID d’appareil que vous souhaitez que l’appareil ait dans votre hub IoT.

Les certificats d’identité d’appareil sont utilisés uniquement pour provisionner l’appareil IoT Edge et l’authentifier auprès d’Azure IoT Hub. Ils ne signent pas de certificats, contrairement aux certificats d’autorité de certification que l’appareil IoT Edge présente aux modules ou aux appareils de nœud terminal à des fins de vérification. Pour plus d’informations, consultez les [détails sur l’utilisation des certificats par Azure IoT Edge](iot-edge-certs.md).

Après avoir créé le certificat d’identité d’appareil, vous devez avoir deux fichiers : un fichier .cer ou .pem, qui contient la partie publique du certificat, et un fichier .cer ou .pem, qui comporte la clé privée du certificat. Si vous envisagez d’utiliser l’inscription de groupe dans le service Device Provisioning, vous avez également besoin de la partie publique d’un certificat d’autorité de certification racine ou intermédiaire dans la même chaîne de certificats de confiance.

Les fichiers suivants sont nécessaires pour configurer le provisionnement automatique avec X.509 :

* Le certificat d’identité d’appareil et son certificat de clé privée. Le certificat d’identité d’appareil est chargé dans DPS si vous créez une inscription individuelle. La clé privée est transmise au runtime IoT Edge.
* Un certificat de chaîne complète, qui doit comporter au moins l’identité d’appareil et les certificats intermédiaires qu’elle contient. Le certificat de chaîne complète est transmis au runtime IoT Edge.
* Un certificat d’autorité de certification racine ou intermédiaire issu de la chaîne d’approbation des certificats. Ce certificat est chargé dans DPS si vous créez une inscription de groupe.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Il existe actuellement dans libiothsm une limitation empêchant l’utilisation de certificats qui expirent le 1er janvier 2038 ou après cette date.

:::moniker-end
<!-- end 1.1 -->

### <a name="use-test-certificates-optional"></a>Utiliser des certificats de test (facultatif)

Si vous n’avez pas d’autorité de certification disponible pour créer des certificats d’identité et que vous souhaitez tester ce scénario, le dépôt Git Azure IoT Edge contient des scripts que vous pouvez utiliser pour générer des certificats de test. Ces certificats, conçus uniquement pour les tests de développement, ne doivent pas être utilisés en production.

Pour créer des certificats de test, suivez les étapes décrites dans [Créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](how-to-create-test-certificates.md). Effectuez les deux sections nécessaires pour configurer les scripts de génération de certificat et créer un certificat d’autorité de certification racine. Ensuite, suivez les étapes permettant de créer un certificat d’identité d’appareil. Quand vous avez terminé, vous devez disposer de la chaîne de certificats et de la paire de clés suivantes :

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Vous avez besoin de ces deux certificats sur l’appareil IoT Edge. Si vous envisagez d’utiliser l’inscription individuelle dans le service Device Provisioning, vous devez charger le fichier .cert.pem. Si vous envisagez d’utiliser l’inscription de groupe dans le service Device Provisioning, la même chaîne de certificats de confiance doit également comporter un certificat d’autorité de certification racine ou intermédiaire à charger. Si vous recourez à des certificats de démonstration, utilisez le certificat `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem` pour l’inscription de groupe.

## <a name="create-a-dps-enrollment"></a>Créer une inscription au service Device Provisioning

Utilisez vos certificats et clés générés afin de créer une inscription dans le service Device Provisioning pour un ou plusieurs appareils IoT Edge.

Si vous envisagez de provisionner un seul appareil IoT Edge, créez une **inscription individuelle**. Si vous avez besoin de provisionner plusieurs appareils, suivez les étapes de création d’une **inscription de groupe** DPS.

Lorsque vous créez une inscription auprès du service Device Provisioning, vous avez la possibilité de déclarer un **État initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des métriques dont vous avez besoin dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](how-to-deploy-at-scale.md).

Pour plus d’informations sur les inscriptions dans le service Device Provisioning, consultez [Guide pratique pour gérer les inscriptions d’appareils](../iot-dps/how-to-manage-enrollments.md).

# <a name="individual-enrollment"></a>[Inscription individuelle](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>Créer une inscription individuelle dans le service Device Provisioning

Les inscriptions individuelles prennent la partie publique du certificat d’identité d’un appareil et la font correspondre avec le certificat sur l’appareil.

> [!TIP]
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions individuelles à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne un appareil IoT Edge.

1. Dans le [Portail Microsoft Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning.

1. Sous **Paramètres**, sélectionnez **Gérer les inscriptions**.

1. Sélectionnez **Ajouter une inscription individuelle** et suivez ces étapes pour configurer l’inscription :  

   * **Mécanisme** : sélectionnez **X.509**.

   * **Fichier .pem ou .cer du certificat principal** : chargez le fichier public à partir du certificat d’identité d’appareil. Si vous avez utilisé les scripts pour générer un certificat de test, choisissez le fichier suivant :

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **ID de l’appareil IoT Hub** : Fournissez un ID pour votre appareil si vous le souhaitez. Vous pouvez utiliser l’ID d’appareil pour cibler un appareil individuel lors du déploiement de module. Si vous ne fournissez pas d’ID d’appareil, le nom commun (CN) dans le certificat X.509 est utilisé.

   * **Appareil IoT Edge** : Sélectionnez **Vrai** pour déclarer que cette inscription est un appareil IoT Edge.

   * **Sélectionnez les hubs IoT auxquels cet appareil peut être attribué** : choisissez le hub IoT lié auquel vous voulez connecter votre appareil. Vous pouvez choisir plusieurs hubs : l’appareil sera affecté à l’un d’entre eux en fonction de la stratégie d’allocation sélectionnée.

   * **État initial du jumeau d’appareil** : ajoutez une valeur d’étiquette destinée au jumeau d’appareil si vous le souhaitez. Vous pouvez utiliser des étiquettes pour cibler des groupes d’appareils lors du déploiement automatique. Par exemple :

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

1. Sélectionnez **Enregistrer**.

Maintenant qu’une inscription existe pour cet appareil, le runtime IoT Edge peut provisionner automatiquement l’appareil lors de l’installation.

# <a name="group-enrollment"></a>[Inscription de groupe](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>Créer une inscription de groupe dans le service Device Provisioning

Les inscriptions de groupe utilisent un certificat d’autorité de certification intermédiaire ou racine à partir de la chaîne de certificats de confiance pour vérifier les certificats d’identité d’appareil individuels.

#### <a name="verify-your-root-certificate"></a>Vérifier votre certificat racine

Quand vous créez un groupe d’inscriptions, vous avez la possibilité d’utiliser un certificat vérifié. Vous pouvez vérifier un certificat avec le service Device Provisioning en prouvant que vous êtes propriétaire du certificat racine. Pour plus d’informations, consultez [Guide pratique pour effectuer la preuve de possession de certificats d’autorité de certification X.509](../iot-dps/how-to-verify-certificates.md).

1. Dans le [Portail Microsoft Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning.

1. Sélectionnez **Certificats** dans le menu de gauche.

1. Sélectionnez **Ajouter** pour ajouter un nouveau certificat.

1. Entrez un nom convivial pour votre certificat, puis accédez au fichier .cer ou .pem représentant la partie publique de votre certificat X.509.

   Si vous utilisez les certificats de démonstration, chargez le certificat `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem`.

1. Sélectionnez **Enregistrer**.

1. Votre certificat doit maintenant être listé dans la page **Certificats**. Sélectionnez-le pour ouvrir les détails du certificat.

1. Sélectionnez **Générer le code de vérification**, puis copiez le code généré.

1. Que vous ayez fourni votre propre certificat d’autorité de certification ou utilisiez les certificats de démonstration, vous pouvez utiliser l’outil de vérification disponible dans le dépôt IoT Edge pour vérifier la preuve de possession. L’outil de vérification utilise votre certificat d’autorité de certification pour signer un nouveau certificat ayant le code de vérification fourni comme nom d’objet.

   ```bash
   ./certGen.sh create_verification_certificate <verification code>
   ```

1. Dans le portail Azure, dans la même page des détails du certificat, chargez le certificat de vérification qui vient d’être généré.

1. Sélectionnez **Vérifier**.

#### <a name="create-enrollment-group"></a>Créer un groupe d’inscriptions

Pour plus d’informations sur les inscriptions dans le service Device Provisioning, consultez [Guide pratique pour gérer les inscriptions d’appareils](../iot-dps/how-to-manage-enrollments.md).

> [!TIP]
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions de groupe à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne des appareils IoT Edge. Pour une inscription de groupe, tous les appareils doivent être de IoT Edge, ou aucun d’entre eux ne peut l’être.

1. Dans le [Portail Microsoft Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning.

1. Sous **Paramètres**, sélectionnez **Gérer les inscriptions**.

1. Sélectionnez **Ajouter un groupe d’inscriptions** et suivez les étapes ci-après pour configurer l’inscription :

   * **Nom du groupe** : fournissez un nom facile à retenir pour cette inscription de groupe.

   * **Type d’attestation** : Sélectionnez **Certificate**.

   * **Appareil IoT Edge** : sélectionnez **VRAI**. Pour une inscription de groupe, tous les appareils doivent être de IoT Edge, ou aucun d’entre eux ne peut l’être.

   * **Type de certificat** : sélectionnez **Certificat d’autorité de certification**.

   * **Certificat principal** : choisissez votre certificat dans la liste déroulante.

   * **Sélectionnez les hubs IoT auxquels cet appareil peut être attribué** : choisissez le hub IoT lié auquel vous voulez connecter votre appareil. Vous pouvez choisir plusieurs hubs : l’appareil sera affecté à l’un d’entre eux en fonction de la stratégie d’allocation sélectionnée.

   * **État initial du jumeau d’appareil** : ajoutez une valeur d’étiquette destinée au jumeau d’appareil si vous le souhaitez. Vous pouvez utiliser des étiquettes pour cibler des groupes d’appareils lors du déploiement automatique. Par exemple :

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

1. Sélectionnez **Enregistrer**.

Maintenant qu’il existe une inscription pour ces appareils, le runtime IoT Edge peut les provisionner automatiquement lors de l’installation.

---

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

Préparez les informations suivantes :

* La valeur **Étendue de l’ID** du service Device Provisioning. Vous pouvez récupérer cette valeur à partir de la page de présentation de votre instance du service Device Provisioning dans le portail Azure.
* Le fichier de chaîne de certificats d’identité d’appareil sur l’appareil
* Le fichier de clé d’identité d’appareil sur l’appareil

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Ouvrez le fichier de configuration sur l’appareil IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Recherchez la section des configurations de provisionnement dans le fichier. Supprimez les marques de commentaire des lignes pour l’approvisionnement de certificat DPS X.509, et assurez-vous que les autres lignes d’approvisionnement sont commentées.

   La ligne `provisioning:` ne doit pas être précédée d’un espace et les éléments imbriqués doivent être en retrait de deux espaces.

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
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Mettez à jour les valeurs de `scope_id`, `identity_cert` et `identity_pk` avec vos informations sur l’appareil et le service Device Provisioning.

   Quand vous ajoutez le certificat X.509 et les informations de clé au fichier config.yaml, les chemins doivent être fournis en tant qu’URI de fichier. Par exemple :

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Si vous le souhaitez, fournissez le `registration_id` de l'appareil, qui doit correspondre au nom commun du certificat d'identité. Si vous laissez cette ligne commentée, le nom commun est appliqué automatiquement.

1. Si vous le souhaitez, utilisez les lignes `always_reprovision_on_startup` ou `dynamic_reprovisioning` pour configurer le comportement de reprovisionnement de votre appareil. Si un appareil est configuré pour être reprovisionné au démarrage, le reprovisionnement est toujours tenté avec le service DPS dans un premier temps, puis, en cas d’échec, au moyen de la sauvegarde de provisionnement. Si un appareil est configuré pour se reprovisionner dynamiquement lui-même, IoT Edge redémarre et exécute le reprovisionnement si un événement de reprovisionnement est détecté. Pour plus d’informations, consultez [Concepts du reprovisionnement d’appareils IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Enregistrez et fermez le fichier config.yaml.

1. Redémarrez le runtime IoT Edge afin qu’il récupère toutes les modifications de configuration que vous avez effectuées sur l’appareil.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Créez un fichier de configuration pour votre appareil à partir d’un fichier de modèle fourni dans le cadre de l’installation d’IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Ouvrez le fichier de configuration sur l’appareil IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Recherchez la section **Provisioning** du fichier. Supprimez les marques de commentaire des lignes pour l’approvisionnement de certificat X.509, et assurez-vous que les autres lignes d’approvisionnement sont commentées.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   registration_id = "<REGISTRATION ID>"

   identity_cert = "<DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<DEVICE IDENTITY PRIVATE KEY>"
   ```

1. Mettez à jour la valeur de `id_scope` avec l'ID d'étendue que vous avez copié à partir de votre instance de DPS.

1. Fournissez un `registration_id` pour l'appareil ; il s'agira de l'ID de l'appareil dans IoT Hub. L'ID d'inscription doit correspondre au nom commun du certificat d'identité.

1. Mettez à jour les valeurs d'`identity_cert` et `identity_pk` avec les informations de votre certificat et de votre clé.

   La valeur du certificat d’identité peut être fournie sous la forme d’un URI de fichier ou être émise de manière dynamique à l’aide de l’autorité de certification EST ou locale. Supprimez les marques de commentaire d’une seule ligne, selon le format que vous choisissez d’utiliser.

   La valeur de la clé privée d’identité peut être fournie sous la forme d’un URI de fichier ou d’un URI PKCS#11. Supprimez les marques de commentaire d’une seule ligne, selon le format que vous choisissez d’utiliser.

   Si vous utilisez des URI PKCS#11, recherchez la section **PKCS#11** dans le fichier config et fournissez des informations sur votre configuration de PKCS#11.

1. Enregistrez et fermez le fichier.

1. Appliquez les modifications de configuration que vous avez apportées à IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si le runtime a démarré correctement, vous pouvez accéder à votre IoT Hub et commencer à déployer des modules IoT Edge sur votre appareil.

# <a name="individual-enrollment"></a>[Inscription individuelle](#tab/individual-enrollment)

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service Device Provisioning a été utilisée. Dans le Portail Azure, accédez à l’instance du service Device Provisioning. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste.

# <a name="group-enrollment"></a>[Inscription de groupe](#tab/group-enrollment)

Vous pouvez vérifier que l’inscription de groupe que vous avez créée dans le service Device Provisioning a été utilisée. Dans le Portail Azure, accédez à l’instance du service Device Provisioning. Ouvrez les détails de l’inscription de groupe que vous avez créée. Accédez à l’onglet **Enregistrements d’inscription** pour afficher tous les appareils enregistrés dans ce groupe.

---

Utilisez les commandes suivantes sur votre appareil pour vérifier que IoT Edge a été installé et démarré correctement.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Vérifiez l’état du service IoT Edge.

```cmd/sh
systemctl status iotedge
```

Consultez les journaux d’activité de service.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Répertoriez les modules en cours d’exécution.

```cmd/sh
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Vérifiez l’état du service IoT Edge.

```cmd/sh
sudo iotedge system status
```

Consultez les journaux d’activité de service.

```cmd/sh
sudo iotedge system logs
```

Répertoriez les modules en cours d’exécution.

```cmd/sh
sudo iotedge list
```

:::moniker-end

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service Device Provisioning vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md).
