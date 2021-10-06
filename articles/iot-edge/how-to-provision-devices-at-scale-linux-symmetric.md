---
title: Créer et provisionner des appareils IoT Edge à l’aide de clés symétriques sur Linux - Azure IoT Edge | Microsoft Docs
description: Utilisez l’attestation de clé symétrique pour tester le provisionnement des appareils Linux à grande échelle pour Azure IoT Edge avec le service Device Provisioning.
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c2bae93e325a36b6db28d1a9c1e6e47ee947db39
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700941"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-symmetric-key"></a>Créer et provisionner des appareils IoT Edge à grande échelle sur Linux à l’aide de clés symétriques

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Cet article fournit des instructions complètes pour le provisionnement automatique d’un ou plusieurs appareils IoT Edge Linux à l’aide de clés symétriques. Vous pouvez provisionner automatiquement des appareils Azure IoT Edge à l’aide du [service Device Provisioning (DPS) Azure IoT Hub](../iot-dps/index.yml). Si vous ne connaissez pas le processus de provisionnement automatique, consultez la présentation du [provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Voici les tâches à effectuer :

1. Création d’une **inscription individuelle** pour un appareil unique ou d’une **inscription de groupe** pour un ensemble d’appareils
1. Installation du runtime IoT Edge et de la connexion à IoT Hub.

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>Pour une expérience simplifiée, essayez l[outil de configuration Azure IoT Edge](https://github.com/azure/iot-edge-config). Cet outil en ligne de commande, actuellement disponible en préversion publique, installe IoT Edge sur votre appareil et le provisionne à l’aide du service IoT Hub Device Provisioning et de l’attestation de clé symétrique.
:::moniker-end

L’attestation de clé symétrique est une approche simple pour authentifier un appareil avec une instance du service Device Provisioning. Cette méthode d’attestation représente une expérience « Hello world » pour les développeurs qui découvrent le provisionnement d’appareils ou n’ont pas d’exigences de sécurité strictes. L’attestation d’appareil avec un [Module de plateforme sécurisée (TPM)](../iot-dps/concepts-tpm-attestation.md) ou un [certificat X.509](../iot-dps/concepts-x509-attestation.md) est plus sécurisée, et doit être utilisée lorsque les exigences de sécurité sont plus strictes.

## <a name="prerequisites"></a>Prérequis

* Un hub IoT actif.
* Un appareil Linux physique ou virtuel faisant office d’appareil IoT Edge.
  * Vous devrez définir un **ID d’inscription** *unique* pour identifier chaque appareil. Vous pouvez utiliser l’adresse MAC, le numéro de série ou toute information unique provenant de l’appareil. Dans cet exemple, vous pouvez utiliser une combinaison d’une adresse MAC et d’un numéro de série, forment la chaîne suivante pour un ID d’inscription : `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`. Les caractères valides sont les caractères alphanumériques minuscules et les tirets (`-`).
* Une instance du service IoT Hub Device Provisioning dans Azure, liée à votre hub IoT.
  * Si vous ne disposez pas d’une instance du service Device Provisioning, vous pouvez suivre les instructions fournies dans les sections [Créer un service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) et [Lier le hub IoT et votre service Device Provisioning](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) du guide de démarrage rapide du service IoT Hub Device Provisioning.
  * Après avoir lancé l’exécution du service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge.

## <a name="create-a-dps-enrollment"></a>Créer une inscription au service Device Provisioning

Créez une inscription pour provisionner un ou plusieurs appareils via le service Device Provisioning.

Si vous envisagez de provisionner un seul appareil IoT Edge, créez une **inscription individuelle**. Si vous avez besoin de provisionner plusieurs appareils, suivez les étapes de création d’une **inscription de groupe** DPS.

Lorsque vous créez une inscription dans le service Device Provisioning, vous avez la possibilité de déclarer un **état initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des métriques dont vous avez besoin dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](how-to-deploy-at-scale.md).

Pour plus d’informations sur les inscriptions dans le service Device Provisioning, consultez [Guide pratique pour gérer les inscriptions d’appareils](../iot-dps/how-to-manage-enrollments.md).

# <a name="individual-enrollment"></a>[Inscription individuelle](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>Créer une inscription individuelle dans le service Device Provisioning

> [!TIP]
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions individuelles à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne un appareil IoT Edge.

1. Dans le [Portail Microsoft Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning.

1. Sous **Paramètres**, sélectionnez **Gérer les inscriptions**.

1. Sélectionnez **Ajouter une inscription individuelle** et suivez ces étapes pour configurer l’inscription :  

   1. Pour **Mécanisme**, sélectionnez **Clé symétrique**.

   1. Indiquez un **ID d’inscription** unique pour votre appareil.

   1. Fournissez si vous le souhaitez un **ID d’appareil loT Hub** pour votre appareil. Vous pouvez utiliser l’ID d’appareil pour cibler un appareil individuel lors du déploiement de module. Si vous ne fournissez pas un ID d’appareil, l’ID d’inscription est utilisé.

   1. Sélectionnez **Vrai** pour déclarer que cette inscription est un appareil IoT Edge.

   1. Si vous le souhaitez, ajoutez une valeur de balise à l’**état initial du jumeau d’appareil**. Vous pouvez utiliser des balises pour cibler des groupes d’appareils lors du déploiement de module. Par exemple :

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

1. Copiez la **clé primaire** de l’inscription spécifique à utiliser lors de l’installation du runtime IoT Edge.

Maintenant qu’une inscription existe pour cet appareil, le runtime IoT Edge peut provisionner automatiquement l’appareil lors de l’installation.

# <a name="group-enrollment"></a>[Inscription de groupe](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>Créer une inscription de groupe dans le service Device Provisioning

> [!TIP]
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions de groupe à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne des appareils IoT Edge. Pour une inscription de groupe, tous les appareils doivent être de IoT Edge, ou aucun d’entre eux ne peut l’être.

1. Dans le [Portail Microsoft Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning.

1. Sous **Paramètres**, sélectionnez **Gérer les inscriptions**.

1. Sélectionnez **Ajouter une inscription individuelle** et suivez ces étapes pour configurer l’inscription :  

   1. Indiquez un **nom de groupe**.

   1. Sélectionnez **Clé symétrique** comme type d’attestation.

   1. Sélectionnez **Vrai** pour déclarer que cette inscription est un appareil IoT Edge. Pour une inscription de groupe, tous les appareils doivent être de IoT Edge, ou aucun d’entre eux ne peut l’être.

   1. Si vous le souhaitez, ajoutez une valeur de balise à l’**état initial du jumeau d’appareil**. Vous pouvez utiliser des balises pour cibler des groupes d’appareils lors du déploiement de module. Par exemple :

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

1. Copiez la valeur de **clé primaire** du groupe d’inscriptions à utiliser lors de la création de clés d’appareil à utiliser avec une inscription de groupe.

Maintenant qu’il existe un groupe d’inscriptions, le runtime IoT Edge peut provisionner automatiquement des appareils lors de l’installation.

#### <a name="derive-a-device-key"></a>Dériver une clé d’appareil

Chaque appareil provisionné dans le cadre d’une inscription de groupe a besoin d’une clé d’appareil dérivée pour effectuer une attestation de clé symétrique avec l’inscription lors du provisionnement.

Pour générer une clé d’appareil, utilisez la clé que vous avez copiée à partir de votre groupe d’inscriptions DPS pour calculer un [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) de l’ID d’inscription unique pour l’appareil et convertir le résultat au format Base64.

> [!IMPORTANT]
> N’incluez pas la clé primaire ou secondaire de votre inscription dans le code de votre appareil.

Sur Linux, vous pouvez utiliser openssl pour générer votre clé d’appareil dérivée, comme indiqué dans l’exemple suivant.

Remplacez la valeur **KEY** par la **clé primaire** que vous avez notée précédemment.

Remplacez la valeur de **REG_ID** avec l’ID d’inscription de votre appareil.

```bash
KEY=PASTE_YOUR_ENROLLMENT_KEY_HERE
REG_ID=PASTE_YOUR_REGISTRATION_ID_HERE

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

Voici un exemple de sortie d’une clé d’appareil dérivée :

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

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

La procédure de cette section représente le processus classique d’installation de la dernière version sur un appareil disposant d’une connexion Internet. Si vous devez installer une version spécifique, telle qu’une préversion, ou si vous devez installer en mode hors connexion, suivez les [étapes d’installation hors connexion ou propres à une version](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional).

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

La procédure de cette section représente le processus classique d’installation de la dernière version sur un appareil disposant d’une connexion Internet. Si vous devez installer une version spécifique, telle qu’une préversion, ou si vous devez installer en mode hors connexion, suivez les [étapes d’installation hors connexion ou propres à une version](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional).

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

* Valeur **Étendue de l’ID** du service Device Provisioning
* L’**ID d’inscription** de l’appareil que vous avez créé
* Entrez la **clé primaire** d’une inscription spécifique ou une [clé dérivée](#derive-a-device-key) pour les appareils utilisant une inscription de groupe.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Ouvrez le fichier de configuration sur l’appareil IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Recherchez la section des configurations de provisionnement dans le fichier. Décommentez les lignes du provisionnement avec les clés symétriques du service Device Provisioning et assurez-vous que les autres lignes de provisionnement sont commentées.

   La ligne `provisioning:` ne doit pas être précédée d’un espace et les éléments imbriqués doivent être en retrait de deux espaces.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "PASTE_YOUR_SCOPE_ID_HERE"
     attestation:
       method: "symmetric_key"
       registration_id: "PASTE_YOUR_REGISTRATION_ID_HERE"
       symmetric_key: "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Mettez à jour les valeurs de `scope_id`, `registration_id` et `symmetric_key` avec vos informations relatives à l’appareil et à DPS.

1. Si vous le souhaitez, utilisez les lignes `always_reprovision_on_startup` ou `dynamic_reprovisioning` pour configurer le comportement de reprovisionnement de votre appareil. Si un appareil est configuré pour être reprovisionné au démarrage, le reprovisionnement est toujours tenté avec le service DPS dans un premier temps, puis, en cas d’échec, au moyen de la sauvegarde de provisionnement. Si un appareil est configuré pour se reprovisionner dynamiquement lui-même, IoT Edge redémarre et exécute le reprovisionnement si un événement de reprovisionnement est détecté. Pour plus d’informations, consultez [Concepts du reprovisionnement d’appareils IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Redémarrez le runtime IoT Edge afin qu’il récupère toutes les modifications de configuration que vous avez effectuées sur l’appareil.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

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

1. Recherchez la section **Provisioning** du fichier. Supprimez les marques de commentaire des lignes pour l’approvisionnement par clé symétrique de DPS, et assurez-vous que les autres lignes d’approvisionnement sont commentées.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "PASTE_YOUR_SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "PASTE_YOUR_REGISTRATION_ID_HERE"

   symmetric_key = "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   ```

1. Mettez à jour les valeurs de `id_scope`, `registration_id` et `symmetric_key` avec vos informations relatives à l’appareil et à DPS.

   Le paramètre de clé symétrique peut accepter la valeur d’une clé incluse, d’un URI de fichier ou d’un URI de PKCS#11. Supprimez les marques de commentaire d’une ligne de clé symétrique, en fonction du format que vous utilisez.

   Si vous utilisez des URI PKCS#11, recherchez la section **PKCS#11** dans le fichier config et fournissez des informations sur votre configuration de PKCS#11.

1. Enregistrez et fermez le fichier config.toml.

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
