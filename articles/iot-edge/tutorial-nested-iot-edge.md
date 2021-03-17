---
title: 'Tutoriel : Créer une hiérarchie d’appareils IoT Edge - Azure IoT Edge'
description: Ce tutoriel montre comment créer une structure hiérarchique d’appareils IoT Edge à l’aide de passerelles.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: c1b30a1eafe9af92c1ef3f81773d213ccf96555c
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462029"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>Tutoriel : Créer une hiérarchie d’appareils IoT Edge (préversion)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Déployez des nœuds Azure IoT Edge sur des réseaux organisés en couches hiérarchiques. Chaque couche d’une hiérarchie est un appareil de passerelle qui gère les messages et les requêtes des appareils situés dans la couche du dessous.

>[!NOTE]
>Cette fonctionnalité nécessite IoT Edge version 1.2, qui est en préversion publique, exécutant des conteneurs Linux.

Vous pouvez structurer une hiérarchie d’appareils de sorte que seule la couche supérieure soit connectée au cloud, et que les couches inférieures puissent uniquement communiquer avec les couches Nord et Sud adjacentes. Cette structure en couches réseau est la base de la plupart des réseaux industriels, qui respectent la [norme ISA-95](https://en.wikipedia.org/wiki/ANSI/ISA-95).

L’objectif de ce tutoriel est de créer une hiérarchie d’appareils IoT Edge qui simule un environnement de production. À la fin, vous déploierez le [module Simulated Temperature Sensor](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) sur un appareil de couche inférieure sans accès à Internet en téléchargeant des images conteneur à travers la hiérarchie.

Pour atteindre cet objectif, ce tutoriel vous guide dans la création d’une hiérarchie d’appareils IoT Edge, le déploiement de conteneurs de runtime IoT Edge sur vos appareils et la configuration locale de vos appareils. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Créer et définir les relations dans une hiérarchie d’appareils IoT Edge.
> * Configurer le runtime IoT Edge sur les appareils de votre hiérarchie.
> * Installer des certificats cohérents dans votre hiérarchie d’appareils.
> * Ajouter des charges de travail aux appareils de votre hiérarchie.
> * Utiliser le [module Proxy d’API IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) pour router en toute sécurité le trafic HTTP sur un port unique à partir de vos appareils de couche inférieure.

Dans ce tutoriel, les couches réseau suivantes sont définies :

* **Couche supérieure** : les appareils IoT Edge de cette couche peuvent se connecter directement au cloud.

* **Couches inférieures** : les appareils IOT Edge se trouvant dans des couches en dessous de la couche supérieure ne peuvent pas se connecter directement au cloud. Ils doivent passer par un ou plusieurs appareils IoT Edge intermédiaires pour envoyer et recevoir des données.

Par souci de simplicité, ce tutoriel utilise une hiérarchie à deux appareils illustrée ci-dessous. Un appareil, l’**appareil de couche supérieure** dans la hiérarchie, peut se connecter directement au cloud. Cet appareil sera également appelé **appareil parent**. L’autre appareil, l’**appareil de couche inférieure** dans la hiérarchie, ne peut pas se connecter directement au cloud. Vous pouvez ajouter des appareils de couche inférieure pour représenter votre environnement de production si nécessaire. Les appareils dans les couches inférieures sont également appelés **appareils enfants**. La configuration de tout appareil de couche inférieure supplémentaire suivra la configuration l’**appareil de couche inférieure**.

![Structure de la hiérarchie du tutoriel, avec deux appareils : l’appareil de couche supérieure et l’appareil de couche inférieure](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Prérequis

Pour créer une hiérarchie d’appareils IoT Edge, vous aurez besoin des éléments suivants :

* Un ordinateur (Windows ou Linux) avec une connectivité Internet
* Un compte Azure avec un abonnement valide. Si vous n’avez pas [d’abonnement Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) de niveau gratuit ou standard dans Azure
* Azure CLI v2.3.1 avec l’extension Azure IoT v0.10.6 ou une version ultérieure installée. Ce tutoriel utilise [Azure Cloud Shell](../cloud-shell/overview.md). Si vous ne connaissez pas Azure Cloud Shell, [consultez ce guide de démarrage rapide pour plus d’informations](./quickstart-linux.md#prerequisites).
  * Pour afficher vos versions actuelles des modules et extensions Azure CLI, exécutez la commande [az version](/cli/azure/reference-index?#az_version).
* Un appareil Linux à configurer en tant qu’appareil IoT Edge pour chaque appareil dans votre hiérarchie. Ce tutoriel utilise deux appareils. Si vous n’avez pas d’appareils disponibles, vous pouvez créer des machines virtuelles Azure pour chaque appareil dans votre hiérarchie en remplaçant le texte de l’espace réservé dans la commande suivante, puis en exécutant celle-ci :

   ```azurecli-interactive
   az vm create \
    --resource-group <REPLACE_WITH_RESOURCE_GROUP> \
    --name <REPLACE_WITH_UNIQUE_NAMES_FOR_EACH_VM> \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password <REPLACE_WITH_PASSWORD>
   ```

* Vérifiez que les ports suivants sont ouverts en entrée : 8000, 443, 5671, 8883 :
  * 8000 : Utilisé pour extraire des images conteneur Docker par le biais du proxy d’API.
  * 443 : Utilisé entre les hubs de périphérie parent et enfant pour les appels d’API REST.
  * 5671, 8883 : Utilisé pour AMQP et MQTT.

  Pour plus d’informations, consultez [Guide d’ouverture de ports vers une machine virtuelle avec le portail Azure](../virtual-machines/windows/nsg-quickstart-portal.md).

>[!TIP]
>Si vous souhaitez une présentation automatisée de la configuration d’une hiérarchie d’appareils IoT Edge, vous pouvez suivre l’[exemple Azure IOT Edge pour IoT industriel](https://aka.ms/iotedge-nested-sample) scripté. Ce scénario scripté déploie des machines virtuelles Azure en tant qu’appareils préconfigurés pour simuler un environnement de fabrique.
>
>Si vous souhaitez poursuivre la création de l’exemple de hiérarchie, suivez les étapes du tutoriel ci-dessous.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Configurer votre hiérarchie d’appareils IoT Edge

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Créer une hiérarchie d’appareils IoT Edge

Les appareils IoT Edge composent les couches de votre hiérarchie. Ce tutoriel crée une hiérarchie de deux appareils IoT Edge : l’**appareil de couche supérieure**, et son enfant, l’**appareil de couche inférieure**. Vous pouvez créer des appareils enfants supplémentaires en fonction des besoins.

Pour créer vos appareils IoT Edge, vous pouvez utiliser le portail Azure ou Azure CLI.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à votre hub IoT.

1. Dans le menu du volet gauche, sous **Gestion automatique des appareils**, sélectionnez **IoT Edge**.

1. Sélectionnez **+ Ajouter un appareil IoT Edge**. Cet appareil sera l’appareil de la couche supérieure. Entrez donc un ID d’appareil unique approprié. Sélectionnez **Enregistrer**.

1. Sélectionnez **+ Ajouter un appareil IoT Edge** à nouveau. Étant donné que cet appareil sera un appareil de couche inférieure, entrez un ID d’appareil unique approprié.

1. Choisissez **Définir un appareil parent**, choisissez votre appareil de couche supérieure dans la liste des appareils, puis sélectionnez **OK**. Sélectionnez **Enregistrer**.

   ![Définition du parent de l’appareil de couche inférieure](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Dans [Azure Cloud Shell](https://shell.azure.com/), entrez la commande suivante pour créer un appareil IoT Edge dans votre hub. Cet appareil sera l’appareil de la couche supérieure. Entrez donc un ID d’appareil unique approprié :

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

   Une création d’appareil réussie génère en sortie la configuration JSON de l’appareil.

   ![Sortie JSON d’une création d’appareil réussie](./media/tutorial-nested-iot-edge/json-success-output.png)

1. Entrez la commande suivante pour créer un appareil IoT Edge de couche inférieure. Vous pouvez créer plusieurs appareils de couche inférieure si vous souhaitez plus de couches dans votre hiérarchie. Veillez à fournir des identités d’appareil uniques pour chacun d’eux.

   ```azurecli-interactive
   az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Entrez la commande suivante pour définir chaque relation parent-enfant entre l’**appareil de couche supérieure** et chaque **appareil de couche inférieure**. Veillez à exécuter cette commande pour chaque appareil de couche inférieure dans votre hiérarchie.

   ```azurecli-interactive
   az iot hub device-identity parent set --device-id {lower_layer_device_id} --parent-device-id {top_layer_device_id} --hub-name {hub_name}
   ```

   Cette commande n’a pas de sortie explicite.

---

Ensuite, prenez note de la chaîne de connexion de chaque appareil IoT Edge. Vous les utiliserez ultérieurement.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à la section **IoT Edge** de votre hub IoT.

1. Cliquez sur l’ID de l’un des appareils dans la liste des appareils.

1. Sélectionnez **Copier** dans le champ **Chaîne de connexion principale** et enregistrez-la à l’emplacement de votre choix.

1. Répétez cette opération pour tous les autres appareils.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Dans [Azure Cloud Shell](https://shell.azure.com/), pour chaque appareil, entrez la commande suivante afin de récupérer la chaîne de connexion de votre appareil, et enregistrez-la à l’emplacement de votre choix :

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

Si vous avez correctement suivi les étapes ci-dessus, vous pouvez procéder comme suit pour vérifier que vos relations parent-enfant sont correctes dans le portail Azure ou Azure CLI.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à la section **IoT Edge** de votre hub IoT.

1. Cliquez sur l’ID d’un **appareil de couche inférieure** dans la liste d’appareils.

1. Dans la page de détails de l’appareil, vous devriez voir l’identité de votre **appareil de couche supérieure** en regard du champ **Appareil parent**.

   [Appareil parent reconnu par l’appareil enfant](./media/tutorial-nested-iot-edge/lower-layer-device-parent.png)

Vous pouvez également établir les relations parent-enfant dans votre hiérarchie via votre **appareil de couche supérieure**.

1. Cliquez sur l’ID d’un **appareil de couche supérieure** dans la liste d’appareils.

1. Sélectionnez l’onglet **Gérer les appareils enfants** en haut.

1. Dans la liste d’appareils, vous devrez voir votre **appareil de couche inférieure**.

   [Appareil enfant reconnu par l’appareil parent](./media/tutorial-nested-iot-edge/top-layer-device-child.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Dans le service [Azure Cloud Shell](https://shell.azure.com/) vous pouvez vérifier si un appareil enfant a correctement établi sa relation avec l’appareil parent en obtenant l’identité de l’appareil parent reconnu par l’appareil enfant. Cela génère en sortie la configuration JSON de l’appareil parent, illustrée ci-dessus :

   ```azurecli-interactive
   az iot hub device-identity parent show --device-id {lower_layer_device_id} --hub-name {hub_name}
   ```

Vous pouvez également établir les relations parent-enfant dans votre hiérarchie en interrogeant votre **appareil de couche supérieure**.

1. Répertorier les appareils enfants que l’appareil parent connaît :

    ```azurecli-interactive
    az iot hub device-identity children list --device-id {top_layer_device_id} --hub-name {hub_name}
    ```

---

Une fois que vous êtes satisfait de la structure de votre hiérarchie, vous êtes prêt à continuer.

### <a name="create-certificates"></a>Créer des certificats

Tous les appareils d’un [scénario de passerelle](iot-edge-as-gateway.md) ont besoin d’un certificat partagé afin que vous puissiez configurer des connexions sécurisées entre eux. Effectuez les étapes suivantes pour créer des certificats de démonstration pour les deux appareils de ce scénario.

Pour créer des certificats de démonstration sur un appareil Linux, vous devez cloner les scripts de génération et les configurer pour qu’ils s’exécutent localement dans bash.

1. Clonez le référentiel Git IoT Edge, qui contient des scripts pour générer des certificats de démonstration.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. Accédez au répertoire dans lequel vous souhaitez travailler. Tous les fichiers de clés et de certificats seront créés dans ce répertoire.

1. Copiez les fichiers de configuration et de script du dépôt IoT Edge cloné dans votre répertoire de travail.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. Créez le certificat d’autorité de certification racine et un certificat intermédiaire.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Cette commande de script crée plusieurs fichiers de certificat et de clé, mais nous utilisons le fichier suivant comme **certificat d’autorité de certification racine**, pour la hiérarchie de passerelle :

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. Créez deux jeux de clés privées et de certificats d’autorité de certification d’appareils IoT Edge avec la commande suivante : un jeu pour l’appareil de couche supérieure et l’autre pour l’appareil de couche inférieure. Fournissez des noms explicites pour les certificats d’autorité de certification, afin de les distinguer l’un de l’autre.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "{top_layer_certificate_name}"
   ./certGen.sh create_edge_device_ca_certificate "{lower_layer_certificate_name}"
   ```

   Cette commande de script crée plusieurs fichiers de certificat et de clé, mais nous utilisons les paires certificat-clé suivantes sur chaque appareil IoT Edge, qui sont par ailleurs référencées dans le fichier config :

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Chaque appareil a besoin d’une copie du certificat d’autorité de certification racine et d’une copie de son propre certificat d’autorité de certification d’appareil et de sa clé privée. Vous pouvez utiliser un lecteur USB ou une [copie sécurisée des fichiers](https://www.ssh.com/ssh/scp/) pour déplacer les certificats sur chaque appareil.

1. Une fois les certificats transférés, installez l’autorité de certification racine pour chaque appareil.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   Cette commande doit indiquer en sortie qu’un certificat a été ajouté dans `/etc/ssl/certs`.

   [Message de réussite d’installation de certificat](./media/tutorial-nested-iot-edge/certificates-success-output.png)

Si vous avez correctement suivi les étapes ci-dessus, vous pouvez vérifier que vos certificats sont installés dans `/etc/ssl/certs` en accédant à ce répertoire et en recherchant vos certificats installés.

1. Accédez à `/etc/ssl/certs` :

   ```bash
   cd /etc/ssl/certs
   ```

1. Répertoriez les certificats installés `grep` pour `azure` :

   ```bash
   ll | grep azure
   ```

   Vous devriez voir un hachage de certificat lié à votre certificat d’autorité de certification racine, et celui-ci lié à la copie dans votre répertoire `usr/local/share`.

   [Résultats de la recherche de certificats Azure](./media/tutorial-nested-iot-edge/certificate-list-results.png)

Une fois que vous êtes satisfait de l’installation de vos certificats sur chaque appareil, vous êtes prêt à continuer.

### <a name="install-iot-edge-on-the-devices"></a>Installer IoT Edge sur les appareils

L’installation des images d’exécution d’IoT Edge version 1.2 permet à vos appareils d’accéder aux fonctionnalités nécessaires pour opérer en tant que hiérarchie d’appareils.

Pour installer IoT Edge, vous devez installer la configuration de référentiel appropriée, les composants requis et les ressources de mise en production nécessaires.

1. Installez la configuration du référentiel qui correspond au système d’exploitation de votre appareil.

   * **Ubuntu Server 18.04** :

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
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

1. Mettez à jour les listes de packages sur votre appareil.

   ```bash
   sudo apt-get update
   ```

1. Installez le moteur Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

1. Installez IoT Edge et le service d’identité IoT.

   ```bash
   sudo apt-get install aziot-edge
   ```

Si vous avez correctement suivi les étapes ci-dessus, vous avez vu le message de bannière d’Azure IoT Edge qui vous demande de mettre à jour le fichier de configuration d’Azure IoT Edge, `/etc/aziot/config.toml`, sur chaque appareil dans votre hiérarchie. Si c’est le cas, vous êtes prêt à continuer.

### <a name="configure-the-iot-edge-runtime"></a>Configurer le runtime IoT Edge

Outre l’approvisionnement de vos appareils, les étapes de configuration établissent une communication approuvée entre les appareils dans votre hiérarchie à l’aide des certificats que vous avez créés précédemment. Les étapes commencent également à établir la structure réseau de votre hiérarchie. L’appareil de couche supérieure conserve la connectivité Internet, ce qui lui permet d’extraire des images pour son exécution à partir du cloud, tandis que les appareils de couche inférieure passent par l’appareil de couche supérieure pour accéder à ces images.

Pour configurer l’exécution d’IoT Edge, vous devez modifier plusieurs composants du fichier de configuration. Les configurations de l’**appareil de couche supérieure** et d’un **appareil de couche inférieure** étant légèrement différentes, soyez attentif au fichier de configuration d’appareil que vous modifiez à chaque étape. La configuration du runtime IoT Edge pour vos appareils nécessite quatre étapes, toutes accomplies en modifiant le fichier de configuration IoT Edge :

* Provisionnez manuellement chaque appareil en ajoutant sa chaîne de connexion au fichier de configuration.

* Terminez la configuration des certificats de votre appareil en faisant pointer le fichier de configuration sur le certificat d’autorité de certification d’appareil, la clé privée de l’autorité de certification d’appareil et le certificat d’autorité de certification racine.

* Amorcez le système à l’aide de l’agent IoT Edge.

* Mettez à jour le paramètre **hostname** pour votre appareil de **couche supérieure**, et mettez à jour les paramètres **hostname** et **parent_hostname** pour vos appareils de **couche inférieure**.

Effectuez ces étapes et redémarrez le service IoT Edge pour configurer vos appareils.

>[!TIP]
>Lorsque vous parcourez le fichier de configuration dans Nano, vous pouvez utiliser **Ctrl + W** pour rechercher des mots clés dans le fichier.

1. Sur chaque appareil, créez un fichier de configuration basé sur le modèle inclus.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml

1. On each device, open the IoT Edge configuration file.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Pour votre appareil de **couche supérieure**, recherchez la section **hostname**. Supprimez les marques de commentaire de la ligne avec le paramètre `hostname`, et mettez à jour la valeur pour qu’elle corresponde au nom de domaine complet (FQDN) ou à l’adresse IP de l’appareil IoT Edge. Utilisez la valeur que vous choisissez de manière cohérente sur tous les appareils de votre hiérarchie.

   ```toml
   hostname = "<device fqdn or IP>"
   ```

   >[!TIP]
   >Pour coller le contenu du Presse-papiers dans Nano, appuyez sur `Shift+Right Click` ou sur `Shift+Insert`.

1. Pour tout appareil IoT Edge dans des **couches inférieures**, recherchez la section **Parent hostname**. Supprimez les marques de commentaire de la ligne avec le paramètre `parent_hostname`, et mettez à jour la valeur pour qu’elle pointe vers le FQDN ou l’adresse IP de l’appareil parent. Utilisez la valeur exacte que vous avez placée dans le champ **hostname** de l’appareil parent. Pour l’appareil IoT Edge dans la **couche supérieure**, laissez ce paramètre commenté.

   ```toml
   parent_hostname = "<parent device fqdn or IP>"
   ```

   > [!NOTE]
   > Pour les hiérarchies comportant plusieurs couches inférieures, mettez à jour le champ *parent_hostname* avec le nom de domaine complet de l’appareil de la couche située juste au-dessus.

1. Recherchez la section **Trust bundle cert** du fichier. Supprimez les marques de commentaire de la ligne avec le paramètre `trust_bundle_cert`, et mettez à jour la valeur avec le chemin d’accès à l’URI pour qu’elle pointe vers le certificat d’autorité de certification racine que partagent tous les appareils dans la hiérarchie de passerelle.

   ```toml
   trust_bundle_cert = "<root CA certificate>"
   ```

1. Recherchez la section **Provisioning** du fichier. Supprimez les marques de commentaire des lignes pour l’**approvisionnement manuel avec chaîne de connexion**. Pour chaque appareil dans votre hiérarchie, mettez à jour la valeur de **connection_string** avec la chaîne de connexion de votre appareil IoT Edge.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string: "<Device connection string>"
   ```

1. Recherchez la section **Default Edge Agent**.

   * Pour votre appareil de **couche supérieure**, mettez à jour la valeur de l’image edgeAgent en la définissant sur la préversion publique du module dans Azure Container Registry.
   
     ```toml
     [agent.config]
     image = "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4"
     ```

   * Pour chaque appareil IoT Edge de **couches inférieures**, mettez à jour l’image edgeAgent pour qu’elle pointe vers l’appareil parent suivi par le port sur lequel le proxy d’API écoute. Vous allez déployer le module proxy d’API sur l’appareil parent dans la section suivante.
   
     ```toml
     [agent.config]
     image = "<parent hostname value>:8000/azureiotedge-agent:1.2.0-rc4"
     ```

1. Recherchez la section **Edge CA certificate**. Supprimez les marques de commentaire des trois premières lignes de cette section. Ensuite, mettez à jour les deux paramètres pour qu’ils pointent vers les fichiers de certificat d’autorité de certification d’appareil et de clé privée d’autorité de certification d’appareil que vous avez créés dans la section précédente et déplacés vers l’appareil IoT Edge. Indiquez les chemins d’URI de fichier au format `file:///<path>/<filename>`, par exemple `file:///certs/iot-edge-device-ca-top-layer-device.key.pem`.

   ```yml
   [edge_ca]
   cert = "<File URI path to the device full chain CA certificate unique to this device.>"
   pk = "<File URI path to the device CA private key unique to this device.>"
   ```

   >[!NOTE]
   >Veillez à utiliser la **chaîne complète** de la voie de communication et du nom de fichier du certificat d’autorité de certification pour remplir le champ `device_ca_cert`.

1. Enregistrez et fermez le fichier.

   `CTRL + X`, `Y`, `Enter`

1. Après avoir entré les informations d’approvisionnement dans le fichier de configuration, appliquez les modifications :

   ```bash
   sudo iotedge config apply
   ```

Avant de continuer, assurez-vous que vous avez mis à jour le fichier de configuration de chaque appareil dans la hiérarchie. Selon la structure de votre hiérarchie, vous avez configuré un **appareil de couche supérieure** et un ou plusieurs **appareils de couche inférieure**.

Si vous avez correctement suivi les étapes ci-dessus, vous pouvez vérifier que vos appareils sont configurés correctement.

1. Effectuez les vérifications de configuration et de connectivité sur vos appareils :

   ```bash
   sudo iotedge check
   ```

Sur votre **appareil de couche supérieure**, attendez-vous à voir une sortie avec plusieurs évaluations de réussite et au moins un avertissement. La vérification du `latest security daemon` vous avertit qu’une autre version de IOT Edge est la dernière version stable, car IOT Edge version 1.2 est en préversion publique. Vous pouvez voir des avertissements supplémentaires concernant les stratégies de journaux et, en fonction de votre réseau, des stratégies DNS.

Sur un **appareil de couche inférieure**, attendez-vous à voir une sortie similaire à celle de l’appareil de couche supérieure, mais avec un avertissement supplémentaire indiquant que le module EdgeAgent ne peut pas être extrait du flux en amont. Cela est acceptable, car le module Proxy d’API IoT Edge et le module Registre de conteneurs Docker, via lesquels les appareils de couche inférieure extraient les images, ne sont pas encore déployés sur l’**appareil de couche supérieure**.

Voici un exemple de sortie de `iotedge check` :

[Exemple de résultats de configuration et de connectivité](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png)

Une fois que vous êtes satisfait de vos configurations sur chaque appareil, vous êtes prêt à continuer.

## <a name="deploy-modules-to-the-top-layer-device"></a>Déployer des modules sur l’appareil de couche supérieure

Les modules servent à effectuer le déploiement et l’exécution d’IoT Edge sur vos appareils, et à définir plus précisément la structure de votre hiérarchie. Le module Proxy d’API IoT Edge route en toute sécurité le trafic HTTP sur un port unique à partir de vos appareils de couche inférieure. Le module Registre Docker permet d’activer un référentiel d’images Docker auquel vos appareils de couche inférieure peuvent accéder en routant les extractions d’images vers l’appareil de couche supérieure.

Pour déployer des modules sur votre appareil de couche supérieure, vous pouvez utiliser le portail Azure ou Azure CLI.

>[!NOTE]
>Les étapes restantes pour achever la configuration de l’exécution d’IoT Edge et déployer des charges de travail ne sont pas effectuées sur vos appareils IoT Edge.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans le [portail Azure](https://ms.portal.azure.com/):

1. Accédez à votre IoT Hub.

1. Dans le menu du volet gauche, sous **Gestion automatique des appareils**, sélectionnez **IoT Edge**.

1. Cliquez sur l’ID de votre appareil de périphérie de **couche supérieure** dans la liste des appareils.

1. Dans la barre supérieure, sélectionnez **Définir des modules**.

1. Sélectionnez **Paramètres du runtime** en regard de l’icône d’engrenage.

1. Sous **Edge Hub**, dans le champ d’image, entrez `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4`.

   ![Modifier l’image du hub Edge](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Ajoutez les variables d’environnement suivantes à votre module Edge Hub :

    | Nom | Valeur |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Modifier les variables d’environnement du hub Edge](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. Sous **Agent Edge**, dans le champ d’image, entrez `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4`. Sélectionnez **Enregistrer**.

1. Ajoutez le module de registre Docker à votre appareil de couche supérieure. Sélectionnez **+ Ajouter** et choisissez **Module IoT Edge** dans la liste déroulante. Spécifiez le nom `registry` pour votre module de registre Docker, et entrez `registry:latest` pour l’URI de l’image. Ensuite, ajoutez des variables d’environnement et créez des options pour faire pointer votre module de registre local sur le registre de conteneurs Microsoft afin de télécharger des images conteneur à partir de ce registre et de servir ces images à registry:5000.

1. Sous l’onglet des variables d’environnement, entrez la paire nom-valeur de variable d’environnement suivante :

    | Nom | Valeur |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. Sous l’onglet des options de création de conteneur, entrez le code JSON suivant :

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. Ensuite, ajoutez le module de proxy d’API à votre appareil de couche supérieure. Sélectionnez **+ Ajouter** et choisissez **Module de la Place de marché** dans la liste déroulante. Recherchez `IoT Edge API Proxy` et sélectionnez le module. Le proxy d’API IoT Edge utilise le port 8000, et il est configuré pour utiliser par défaut votre module de registre nommé `registry` sur le port 5000.

1. Sélectionnez **Vérifier + créer**, puis **Créer** pour terminer le déploiement. Le runtime IoT Edge de votre appareil de couche supérieure, qui a accès à Internet, extraira et exécutera les configurations en **préversion publique** pour le hub IoT Edge et l’agent IoT Edge.

   ![Déploiement complet contenant le hub Edge, l’agent Edge, le module de registre et le module de proxy d’API](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Dans [Azure Cloud Shell](https://shell.azure.com/), entrez la commande suivante pour créer un fichier deployment.json :

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. Copiez le contenu du code JSON ci-dessous dans votre fichier deployment.json, enregistrez-le et fermez-le.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "registry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                               } 
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Entrez la commande suivante pour créer un déploiement sur votre appareil de périphérie de couche supérieure :

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

Si vous avez correctement suivi les étapes ci-dessus, votre **appareil de couche supérieure** devrait signaler les quatre modules : le module Proxy d’API IOT Edge, le module Registre de conteneurs Docker et les modules système, comme **spécifié dans le déploiement**. Quelques minutes peuvent s’écouler avant que l’appareil reçoive son nouveau déploiement et démarre les modules. Actualisez la page jusqu’à ce que le module capteur de température apparaisse comme **Signalé par l’appareil**. Une fois les modules signalés par l’appareil, vous êtes prêt à continuer.

## <a name="deploy-modules-to-the-lower-layer-device"></a>Déployer des modules sur l’appareil de couche inférieure

Les modules font également office de charges de travail de vos appareils de couche inférieure. Le module capteur de température simulé crée des exemples de données de télémétrie pour fournir un flux de données fonctionnel au travers de votre hiérarchie d’appareils.

Pour déployer des modules sur vos appareils de couche inférieure, vous pouvez utiliser le portail Azure ou Azure CLI.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans le [portail Azure](https://ms.portal.azure.com/):

1. Accédez à votre IoT Hub.

1. Dans le menu du volet gauche, sous **Gestion automatique des appareils**, sélectionnez **IoT Edge**.

1. Cliquez sur l’ID de votre appareil de couche inférieure dans la liste des appareils IoT Edge.

1. Dans la barre supérieure, sélectionnez **Définir des modules**.

1. Sélectionnez **Paramètres du runtime** en regard de l’icône d’engrenage.

1. Sous **Edge Hub**, dans le champ d’image, entrez `$upstream:8000/azureiotedge-hub:1.2.0-rc4`.

1. Ajoutez les variables d’environnement suivantes à votre module Edge Hub :

    | Nom | Valeur |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Sous **Agent Edge**, dans le champ d’image, entrez `$upstream:8000/azureiotedge-agent:1.2.0-rc4`. Sélectionnez **Enregistrer**.

1. Ajoutez le module de capteur de température. Sélectionnez **+ Ajouter** et choisissez **Module de la Place de marché** dans la liste déroulante. Recherchez `Simulated Temperature Sensor` et sélectionnez le module.

1. Sous **Modules IoT Edge**, sélectionnez le module `Simulated Temperature Sensor` que vous venez d’ajouter et mettez à jour son URI d’image pour qu’il pointe sur `$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0`.

1. Sélectionnez **Enregistrer**, **Vérifier + créer**, puis **Créer** pour terminer le déploiement.

   ![Déploiement complet contenant le hub Edge, l’agent Edge et le capteur de température simulé](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Dans [Azure Cloud Shell](https://shell.azure.com/), entrez la commande suivante pour créer un fichier deployment.json :

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. Copiez le contenu du code JSON ci-dessous dans votre fichier deployment.json, enregistrez-le et fermez-le.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc4",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Entrez la commande suivante pour créer un déploiement de modules sur votre appareil de périphérie de couche inférieure :

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

If you completed the above steps correctly, your **lower layer device** should report three modules: the temperature sensor module and the system modules, as **Specified in Deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by Device**. Once the modules are reported by the device, you are ready to continue.

## Troubleshooting

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc4
```

La valeur `azureiotedge-diagnostics` est extraite du registre de conteneurs lié au module registre. Dans ce tutoriel, elle est définie par défaut sur https://mcr.microsoft.com:.

| Name | Valeur |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Si vous utilisez un registre de conteneurs privé, vérifiez que toutes les images (par exemple, IoTEdgeAPIProxy, edgeAgent, edgeHub et diagnostics) y sont présentes.

## <a name="view-generated-data"></a>Afficher les données générées

Le module **Capteur de température simulé** que vous avez envoyé (push) génère des exemples de données d’environnement. Il envoie des messages qui incluent la température et l’humidité ambiantes, la température et la pression de la machine, ainsi qu’un horodatage.

Vous pouvez regarder les messages arriver sur votre hub IoT avec l’[extension Azure IoT Hub pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Vous pouvez aussi afficher ces messages par le biais d’[Azure Cloud Shell](https://shell.azure.com/) :

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Vous pouvez supprimer les ressources Azure et les configurations locales que vous avez créées dans cet article pour éviter des frais.

Pour supprimer les ressources :

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis sélectionnez **Groupes de ressources**.

2. Sélectionnez le nom du groupe de ressources contenant vos ressources de test de IoT Edge. 

3. Passez en revue la liste des ressources contenues dans votre groupe de ressources. Si vous souhaitez toutes les supprimer, vous pouvez sélectionner **Supprimer le groupe de ressources**. Si vous souhaitez en supprimer seulement quelques-unes, vous pouvez cliquer sur chaque ressource pour les supprimer individuellement. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez configuré deux appareils IoT Edge en tant que passerelles et défini l’un des deux comme appareil parent de l’autre. Ensuite, vous avez démontré l’extraction d’une image conteneur sur l’appareil enfant par le biais d’une passerelle.

Vous pouvez passer à d’autres didacticiels pour savoir comment Azure IoT Edge peut trouver d’autres solutions pour votre entreprise.

> [!div class="nextstepaction"]
> [Déployer un modèle Azure Machine Learning en tant que module](tutorial-deploy-machine-learning.md)
