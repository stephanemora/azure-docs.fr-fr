---
title: 'Didacticiel : Configurer un appareil IoT Edge - Machine Learning sur Azure IoT Edge'
description: Dans ce tutoriel, vous allez configurer une machine virtuelle Azure exécutant Linux en tant qu’appareil Azure IoT Edge servant de passerelle transparente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5be7b66a51113121ed755d8ad9cea3518577f2e7
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706951"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Didacticiel : Configurer un appareil IoT Edge

> [!NOTE]
> Cet article fait partie d’une série décrivant l’utilisation d’Azure Machine Learning sur IoT Edge. Si vous êtes arrivé directement à cet article, nous vous encourageons à commencer par le [premier article](tutorial-machine-learning-edge-01-intro.md) de la série afin d’obtenir de meilleurs résultats.

Dans cet article, nous configurons une machine virtuelle Azure exécutant Linux comme un appareil Azure IoT Edge servant de passerelle transparente. La configuration d’une passerelle transparente permet aux appareils de se connecter à Azure IoT Hub à travers la passerelle sans savoir que la passerelle existe. De la même façon, l’utilisateur qui interagit avec les appareils dans IoT Hub n’a pas conscience de l’appareil de passerelle intermédiaire. En fin de compte, la passerelle transparente nous permet d’utiliser une analytique de périphérie dans notre système en ajoutant des modules IoT Edge à la passerelle.

Les étapes décrites dans cet article sont généralement effectuées par un développeur de cloud.

## <a name="generate-certificates"></a>Générer des certificats

Pour qu’un appareil fonctionne comme une passerelle, il doit être capable de se connecter de manière sécurisée aux appareils en aval. Azure IoT Edge vous permet d’utiliser une infrastructure à clé publique (PKI) pour configurer des connexions sécurisées entre les appareils. Dans ce cas, nous autorisons un appareil en aval à se connecter à un appareil IoT Edge faisant office de passerelle transparente. Pour assurer un niveau raisonnable de sécurité, l’appareil en aval doit confirmer l’identité de l’appareil IoT Edge. Pour plus d’informations sur la façon dont les appareils IoT Edge utilisent des certificats, consultez [Détails d’utilisation des certificats Azure IoT Edge](iot-edge-certs.md).

Dans cette section, nous créons les certificats auto-signés à l’aide d’une image Docker que nous devons générer et exécuter. Nous avons choisi d’utiliser une image Docker pour effectuer cette étape, car elle réduit considérablement le nombre d’étapes nécessaires pour créer les certificats sur la machine de développement Windows. Consultez [Générer des certificats avec Windows](how-to-create-transparent-gateway.md#generate-certificates-with-windows) pour plus d’informations sur la génération de certificats sans utiliser de conteneur. [Générer des certificats avec Linux](how-to-create-transparent-gateway.md#generate-certificates-with-linux) contient l’ensemble d’instructions que nous avons automatisées avec l’image Docker.

1. Connectez-vous à votre machine virtuelle de développement.

2. Ouvrez une invite de ligne de commande et exécutez la commande suivante pour créer un répertoire sur la machine virtuelle.

    ```cmd
    mkdir c:\edgeCertificates
    ```

3. Démarrez **Docker pour Windows** à partir du menu Démarrer de Windows.

4. Ouvrez Visual Studio Code.

5. Sélectionnez **Fichier** > **Ouvrir le dossier...** et choisissez **C:\\source\\IoTEdgeAndMlSample\\CreateCertificates**.

6. Cliquez avec le bouton droit sur le fichier dockerfile et choisissez **Générer l’image**.

7. Dans la boîte de dialogue, acceptez la valeur par défaut du nom d’imagine et l’étiquette : **createcertificates:latest**.

8. Attendez la fin de la génération.

    > [!NOTE]
    > Vous risquez de voir un avertissement indiquant qu’une clé publique est manquante. Vous pouvez ignorer cet avertissement. De même, vous voyez un avertissement de sécurité qui vous recommande de vérifier/réinitialiser les autorisations sur votre image. Vous pouvez l’ignorer pour cette image.

9. Dans la fenêtre de terminal Visual Studio Code, exécutez le conteneur createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker vous invite à accéder au lecteur **c:\\** . Sélectionnez **Partager**.

11. À l’invite, indiquez vos informations d’identification.

12. Dès que le conteneur a fini son exécution, recherchez les fichiers suivants dans **c:\\edgeCertificates** :

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Charger des certificats dans Azure Key Vault

Pour stocker nos certificats de manière sécurisée et les rendre accessibles sur plusieurs appareils, nous les chargeons dans Azure Key Vault. Comme vous pouvez le voir dans la liste ci-dessus, nous avons deux types de fichiers de certificat : PFX et PEM. Les fichiers PFX correspondent aux certificats de coffre de clés à charger dans Key Vault. Les fichiers PEM sont en texte intégral et correspondent aux secrets de coffre de clés. Nous utilisons le coffre de clés associé à l’espace de travail Azure Machine Learning que nous avons créé en exécutant les [notebooks Azure](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre espace de travail Azure Machine Learning.

2. Dans la page Vue d’ensemble de l’espace de travail Azure Machine Learning, recherchez le nom du **coffre de clés**.

    ![Copier le nom du coffre de clés](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Sur votre machine de développement, chargez les certificats dans Key Vault. Remplacez **\<subscriptionId\>** et **\<keyvaultname\>** par les informations de votre ressource.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Si vous y êtes invité, connectez-vous à Azure.

5. Le script s’exécute pendant quelques minutes et génère la liste des nouvelles entrées Key Vault.

    ![Sortie du script Key Vault](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Créer un appareil IoT Edge

Pour connecter un appareil Azure IoT Edge à un hub IoT, nous créons d’abord une identité pour l’appareil dans le hub. Nous prenons la chaîne de connexion de l’identité d’appareil dans le cloud et l’utilisons pour configurer le runtime sur notre appareil IoT Edge. Une fois que l’appareil a été configuré et se connecte au hub, nous pouvons déployer des modules et envoyer des messages. Nous pouvons aussi changer la configuration de l’appareil IoT Edge physique en changeant la configuration de l’identité d’appareil correspondante dans le hub IoT.

Pour ce tutoriel, nous créons la nouvelle identité d’appareil à l’aide de Visual Studio Code. Vous pouvez également effectuer ces étapes dans le [portail Azure](how-to-register-device.md#register-in-the-azure-portal) ou avec [Azure CLI](how-to-register-device.md#register-with-the-azure-cli).

1. Sur votre machine de développement, ouvrez Visual Studio Code.

2. Ouvrez le cadre **Appareils Azure IoT Hub** dans la vue de l’Explorateur Visual Studio Code.

3. Cliquez sur les points de suspension et sélectionnez **Créer un appareil IoT Edge**.

4. Donnez un nom à l’appareil. Par souci de commodité, nous utilisons **aaTurbofanEdgeDevice**, afin qu’il apparaisse avant le reste des appareils clients que nous avons créés précédemment dans l’atelier d’appareil pour envoyer les données de test.

5. Le nouvel appareil apparaît dans la liste des appareils.

    ![Voir le nouvel appareil aaTurbofanEdgeDevice dans l’Explorateur VS Code](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Déployer une machine virtuelle Azure

Nous utilisons l’image [Azure IoT Edge sur Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) de la Place de marché Azure pour créer notre appareil IoT Edge dans ce tutoriel. L’image Azure IoT Edge sur Ubuntu installe le dernier runtime Azure IoT Edge et ses dépendances au démarrage. Nous déployons la machine virtuelle à l’aide d’un script PowerShell, `Create-EdgeVM.ps1`, d’un modèle Resource Manager, `IoTEdgeVMTemplate.json`, et d’un script d’interpréteur de commandes, `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Activer le déploiement par programmation

Pour utiliser l’image de la place de marché dans un déploiement scripté, nous devons activer le déploiement par programmation de l’image.

1. Connectez-vous au portail Azure.

1. Sélectionnez **Tous les services**.

1. Dans la barre de recherche, entrez et sélectionnez **Place de marché**.

1. Dans la barre de recherche, entrez et sélectionnez **Azure IoT Edge sur Ubuntu**.

1. Sélectionnez le lien hypertexte **Vous voulez déployer par programmation ? Commencez**.

1. Sélectionnez le bouton **Activer**, puis **Enregistrer**.

    ![Activer le déploiement par programmation des machines virtuelles](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Vous voyez une notification de réussite.

### <a name="create-virtual-machine"></a>Créer une machine virtuelle

Ensuite, exécutez le script afin de créer la machine virtuelle pour votre appareil IoT Edge.

1. Ouvrez une fenêtre PowerShell et accédez au répertoire **EdgeVM**.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Exécutez le script suivant pour créer la machine virtuelle.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Quand vous y êtes invité, indiquez des valeurs pour chaque paramètre. Pour l’abonnement, le groupe de ressources et l’emplacement, nous vous recommandons d’utiliser les mêmes valeurs que celles utilisées pour toutes les ressources tout au long de ce tutoriel.

    * **ID d’abonnement Azure** : disponible dans le portail Azure
    * **Nom du groupe de ressources** : un nom facile à mémoriser pour regrouper les ressources dans ce tutoriel
    * **Emplacement** : Emplacement Azure où la machine virtuelle est créée. Par exemple, westus2 ou northeurope. Pour plus d’informations, consultez tous les [Emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername** : nom du compte administrateur à utiliser pour la connexion à la machine virtuelle
    * **AdminPassword** : mot de passe à définir pour AdminUsername sur la machine virtuelle

4. Pour que le script puisse configurer la machine virtuelle, vous devez vous connecter à Azure avec les informations d’identification associées à l’abonnement Azure que vous utilisez.

5. Le script vérifie les informations pour la création de votre machine virtuelle. Sélectionnez **y** ou **Entrée** pour continuer.

6. Pendant plusieurs minutes, le script exécute les étapes suivantes :

    * Créer le groupe de ressources s’il n’existe pas déjà
    * Créer la machine virtuelle
    * Ajouter des exceptions NSG pour la machine virtuelle sur les ports 22 (SSH), 5671 (AMQP), 5672 (AMPQ) et 443 (SSL)
    * Installer [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

7. Le script génère la chaîne de connexion SSH permettant de se connecter à la machine virtuelle. Copiez la chaîne de connexion pour la prochaine étape.

    ![Copiez la chaîne de connexion SSH pour la machine virtuelle](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Se connecter à votre appareil IoT Edge

Les différentes sections suivantes configurent la machine virtuelle Azure que nous avons créée. La première étape consiste à se connecter à la machine virtuelle.

1. Ouvrez une invite de commandes et collez la chaîne de connexion SSH que vous avez copiée à partir de la sortie du script. Entrez vos propres informations pour le nom d’utilisateur, le suffixe et la région en fonction des valeurs que vous avez fournies au script PowerShell dans la section précédente.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Quand vous êtes invité à valider l’authenticité de l’hôte, tapez **yes** et sélectionnez **Entrée**.

3. Quand vous y êtes invité, indiquez votre mot de passe.

4. Ubuntu affiche un message de bienvenue, puis une invite de type `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Télécharger les certificats de coffre de clés

Plus haut dans cet article, nous avons chargé des certificats dans Key Vault pour les rendre accessibles à notre appareil IoT Edge et notre appareil de nœud terminal (appareil en aval qui utilise l’appareil IoT Edge comme passerelle pour communiquer avec IoT Hub). Nous décrivons l’appareil de nœud terminal plus loin dans le tutoriel. Dans cette section, téléchargez les certificats sur l’appareil IoT Edge.

1. À partir de la session SSH sur la machine virtuelle Linux, connectez-vous à Azure avec Azure CLI.

    ```bash
    az login
    ```

1. Vous êtes invité à ouvrir un navigateur sur <https://microsoft.com/devicelogin> et fournir un code unique. Vous pouvez effectuer ces étapes sur votre machine locale. Fermez la fenêtre de navigateur dès que vous êtes authentifié.

1. Quand l’authentification est correcte, la machine virtuelle Linux se connecte et liste vos abonnements Azure.

1. Sélectionnez l’abonnement Azure à utiliser pour les commandes Azure CLI.

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. Créez un répertoire sur la machine virtuelle pour les certificats.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Téléchargez les certificats que vous avez stockés dans le coffre de clés : new-edge-device-full-chain.cert.pem, new-edge-device.key.pem et azure-iot-test-only.root.ca.cert.pem

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Mettre à jour la configuration de l’appareil IoT Edge

Le runtime IoT Edge utilise le fichier /etc/iotedge/config.yaml pour conserver sa configuration. Nous devons mettre à jour trois informations dans ce fichier :

* **Chaîne de connexion d’appareil** : chaîne de connexion de l’identité de cet appareil dans IoT Hub
* **Certificats :** certificats à utiliser pour les connexions établies avec les appareils en aval
* **Nom d’hôte :** nom de domaine complet (FQDN) de l’appareil IoT Edge de la machine virtuelle.

L’image *Azure IoT Edge sur Ubuntu* que nous avons utilisée pour créer la machine virtuelle IoT Edge est fournie avec un script d’interpréteur de commandes qui met à jour config.yaml avec la chaîne de connexion.

1. Dans Visual Studio Code, cliquez avec le bouton droit sur l’appareil IoT Edge, puis sélectionnez **Copier la chaîne de connexion de l’appareil**.

    ![Copier la chaîne de connexion à partir de Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Dans votre session SSH, exécutez la commande pour mettre à jour le fichier config.yaml avec la chaîne de connexion de votre appareil.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Par la suite, nous mettons à jour les certificats et le nom d’hôte en modifiant directement config.yaml.

1. Ouvrez le fichier config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Mettez à jour la section des certificats de config.yaml en supprimant le `#` du début et en définissant le chemin pour que le fichier ressemble à l’exemple suivant :

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Vérifiez que « certificates: » n’est pas précédé d’un espace et que chaque certificat est précédé de deux espaces.

    Dans nano, cliquez avec le bouton droit pour coller le contenu de votre Presse-papiers à la position actuelle du curseur. Pour remplacer la chaîne, utilisez les touches de direction pour accéder à la chaîne à remplacer, supprimez la chaîne, puis cliquez avec le bouton droit pour coller à partir de la mémoire tampon.

3. Dans le portail Azure, accédez à votre machine virtuelle. Copiez le nom DNS (FQDN de la machine) à partir de la section **Vue d’ensemble**.

4. Collez le FQDN dans la section du nom d’hôte de config.yaml. Vérifiez que le nom est en minuscules.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Enregistrez et fermez le fichier (`Ctrl + X`, `Y`, `Enter`).

6. Redémarrez le démon iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Vérifiez l’état du démon IoT Edge (après la commande, tapez « :q » pour quitter).

    ```bash
    systemctl status iotedge
    ```

8. Si vous voyez des erreurs (texte en couleur avec le préfixe « \[ERROR\] ») dans l’état, examinez les journaux du démon pour avoir des informations détaillées sur l’erreur.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Étapes suivantes

Nous venons de terminer la configuration d’une machine virtuelle Azure comme passerelle transparente d’Azure IoT Edge. Nous avons d’abord généré des certificats de test que nous avons chargés dans Azure Key Vault. Ensuite, nous avons utilisé un script et un modèle Resource Manager pour déployer la machine virtuelle avec l’image « Ubuntu Server 16.04 LTS + Azure IoT Edge runtime » de la Place de marché Azure. Le script a installé Azure CLI ([Installer Azure CLI avec apt](https://docs.microsoft.com/cli/azure/install-azure-cli-apt)). Une fois la machine virtuelle opérationnelle, nous nous sommes connectés via SSH, nous nous sommes connectés à Azure, avons téléchargé des certificats à partir du coffre de clés et modifié la configuration du runtime IoT Edge en mettant à jour le fichier config.yaml. Pour plus d’informations sur l’utilisation d’IoT Edge comme passerelle, consultez [Comment un appareil IoT Edge peut être utilisé comme une passerelle](iot-edge-as-gateway.md). Pour plus d’informations sur la configuration d’un appareil IoT Edge comme passerelle transparente, consultez [Configurer un appareil IoT Edge comme passerelle transparente](how-to-create-transparent-gateway.md).

Passez à l’article suivant pour générer des modules IoT Edge.

> [!div class="nextstepaction"]
> [Créer et déployer des modules IoT Edge personnalisés](tutorial-machine-learning-edge-06-custom-modules.md)
