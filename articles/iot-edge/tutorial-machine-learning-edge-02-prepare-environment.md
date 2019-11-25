---
title: 'Didacticiel : Configurer un environnement - Machine Learning sur Azure IoT Edge'
description: 'Didacticiel : Préparez votre environnement pour le développement et le déploiement de modules pour l’apprentissage automatique à la périphérie.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2ea4248ebaedd318e4112e41169f72bc80b1120f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114078"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Didacticiel : Configurer un environnement - Machine Learning sur Azure IoT Edge

> [!NOTE]
> Cet article fait partie d’une série d’un tutoriel décrivant l’utilisation d’Azure Machine Learning sur IoT Edge. Si vous êtes arrivé directement à cet article, nous vous encourageons à commencer par le [premier article](tutorial-machine-learning-edge-01-intro.md) de la série afin d’obtenir de meilleurs résultats.

Cet article du tutoriel Azure Machine Learning sur IoT Edge vous aide à préparer votre environnement pour le développement et le déploiement. Tout d’abord, configurez une machine de développement avec tous les outils dont vous avez besoin. Ensuite, créez les ressources cloud nécessaires dans Azure.

## <a name="set-up-a-development-machine"></a>Configurer une machine de développement

Cette étape est généralement effectuée par un développeur cloud. Certains logiciels peuvent également être utiles pour un scientifique des données.

Au cours de cet article, nous effectuons diverses tâches de développement, notamment le codage, la compilation, la configuration et le déploiement de modules IoT Edge et d’appareils IoT. Pour faciliter l’utilisation, nous avons créé un script PowerShell qui crée une machine virtuelle Azure avec plusieurs des prérequis déjà configurés. La machine virtuelle que vous créez doit pouvoir gérer la [virtualisation imbriquée](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization), c’est pourquoi nous avons choisi la taille de machine [Standard_D8s_v3](../virtual-machines/windows/sizes-general.md#dsv3-series-1).

La machine virtuelle de développement sera mise en place avec :

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Desktop pour Windows](https://www.docker.com/products/docker-desktop)
* [Git pour Windows](https://gitforwindows.org/)
* [GIT Credential Manager pour Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [SDK .NET Core](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [Extensions de VS Code](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Outils IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

La machine de développement n’est pas strictement nécessaire - tous les outils de développement peuvent être exécutés sur une machine locale. Cependant, nous recommandons fortement d’utiliser la machine virtuelle pour assurer des règles du jeu équitables.

Il faut environ 30 minutes pour créer et configurer la machine virtuelle.

### <a name="get-the-script"></a>Obtenir le script

Cloner ou télécharger le script PowerShell à partir du référentiel d’exemples [Machine Learning et IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample).

### <a name="create-an-azure-virtual-machine"></a>Créer une machine virtuelle Azure

Le répertoire DevVM contient les fichiers nécessaires à la création d’une machine virtuelle Azure appropriée pour compléter ce tutoriel.

1. Ouvrez PowerShell en tant qu’administrateur, puis naviguez jusqu’au répertoire dans lequel vous avez téléchargé le code. Nous utiliserons `<srcdir>` pour faire référence au répertoire racine de votre source.

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. Exécutez la commande suivante pour permettre l’exécution des scripts. Cliquez sur **Oui à tout** lorsque vous y êtes invité.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Exécutez Create-AzureDevVM.ps1 à partir de ce répertoire.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * À l’invite, fournissez les informations suivantes :
      * **ID d’abonnement Azure** : Votre ID d’abonnement, disponible dans le Portail Azure
      * **Nom du groupe de ressources** : Nom d’un groupe de ressources nouveau ou existant dans Azure
      * **Emplacement** : Choisissez un emplacement Azure où la machine virtuelle sera créée. Par exemple, westus2 ou northeurope. Pour plus d’informations, consultez [Emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/).
      * **AdminUsername** : Fournissez un nom facile à mémoriser pour le compte administrateur que vous souhaitez créer et utiliser sur la machine virtuelle.
      * **AdminPassword** : Définissez un mot de passe pour le compte administrateur sur la machine virtuelle.

    * Si vous n’avez pas installé Azure PowerShell, le script installera le [module Az Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0).

    * Vous serez invité à vous connecter à Azure.

    * Le script confirme les informations pour la création de votre machine virtuelle. Appuyez sur `y` ou `Enter` pour continuer.

Pendant plusieurs minutes, le script exécute les étapes suivantes :

* Créer le groupe de ressources s’il n’existe pas
* Déployer la machine virtuelle
* Activer Hyper-V sur la machine virtuelle
* Installer les logiciels nécessaires au développement et clonez le référentiel d’exemples
* Redémarrez la machine virtuelle.
* Créer un fichier RDP sur votre bureau pour vous connecter à la machine virtuelle

### <a name="set-auto-shutdown-schedule"></a>Définir la planification d’arrêt automatique

Pour vous aider à réduire les coûts, la machine a été créée avec une planification d’arrêt automatique définie sur 1900 PST. Il se peut que vous deviez mettre à jour cette planification en fonction de votre localisation et de votre planification. Pour mettre à jour la planification d’arrêt :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Accédez à votre machine virtuelle dans le groupe de ressources que vous avez fourni dans la section précédente.

3. Sélectionnez **Arrêt automatique** dans le navigateur latéral.

4. Entrez une nouvelle heure d’arrêt dans **Arrêt planifié** ou changez de **Fuseau horaire** puis cliquez sur **Enregistrer**.

### <a name="connect-and-configure-development-machine"></a>Connecter et configurer la machine de développement

Maintenant que nous avons créé une machine virtuelle, nous devons terminer l’installation du logiciel nécessaire pour compléter le tutoriel.

#### <a name="start-a-remote-desktop-session"></a>Démarrer une session Bureau à distance

1. Le script de création de machine virtuelle a créé un fichier RDP sur votre bureau.

2. Double-cliquez sur le fichier portant le nom **\<nom_machine_virtuelle_Azure\>.rdp**.

3. Une boîte de dialogue vous indique que l’éditeur de la connexion distante est inconnu. Cliquez sur la case **Ne pas me redemander pour les connexions à cet ordinateur** puis sélectionnez **Connecter**.

4. Lorsque vous y êtes invité, fournissez le mot de passe de l’administrateur que vous avez utilisé lors de l’exécution du script pour configurer la machine et cliquez sur **OK**.

5. Une invite vous demande d’accepter le certificat de la machine virtuelle. Sélectionnez **Ne pas me redemander pour les connexions à cet ordinateur** puis choisissez **Oui**.

#### <a name="install-visual-studio-code-extensions"></a>Installer les extensions Visual Studio Code

Maintenant que vous êtes connecté à la machine de développement, ajoutez quelques extensions utiles à Visual Studio Code pour faciliter l’expérience de développement.

1. Dans une fenêtre PowerShell, accédez à **C:\\source\\IoTEdgeAndMlSample\\DevVM**.

2. Autorisez l’exécution de scripts sur la machine virtuelle en l’indiquant explicitement.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Exécutez le script.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. Le script s’exécutera pendant quelques minutes en installant des extensions de code VS :

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Configurer IoT Hub et le stockage

Ces étapes sont généralement effectuées par un développeur de cloud.

Azure IoT Hub est le cœur de toute application IoT. Il gère la communication sécurisée entre les appareils IoT et le cloud. C’est le principal point de coordination pour l’exploitation de la solution d’apprentissage automatique IoT Edge.

* IoT Hub utilise des itinéraires pour diriger les données entrantes des appareils IoT vers d’autres services en aval. Nous tirerons parti des itinéraires IoT Hub pour envoyer les données d’appareil à Stockage Azure où elles pourront être utilisées par Azure Machine Learning pour former notre classifieur de durée de vie restante.

* Plus loin dans le tutoriel, nous utiliserons IoT Hub pour configurer et gérer notre appareil Azure IoT Edge.

Dans cette section, vous utilisez un script pour créer un hub IoT Azure et un compte Stockage Azure. Ensuite, vous configurez un itinéraire qui achemine les données reçues par le hub vers un conteneur Azure Storage Blob utilisant le Portail Azure. Ces étapes prennent environ 10 minutes.

### <a name="create-cloud-resources"></a>Créer des ressources cloud

1. Sur votre machine de développement, ouvrez une fenêtre PowerShell.

1. Accédez au répertoire IoTHub.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Exécutez le script de création. Utilisez les mêmes valeurs pour l’ID d’abonnement, la localisation et le groupe de ressources que lors de la création de la machine virtuelle de développement.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * Vous serez invité à vous connecter à Azure.
    * Le script confirme les informations pour la création de votre compte Hub et Stockage. Appuyez sur `y` ou `Enter` pour continuer.

L’exécution du script prend deux minutes. Une fois terminé, le script affiche le nom du hub et du compte de stockage.

### <a name="review-route-to-storage-in-iot-hub"></a>Passer en revue l’itinéraire vers le stockage dans IoT Hub

Dans le cadre de la création du hub IoT, le script que nous avons exécuté à la section précédente a également créé un point de terminaison personnalisé et un itinéraire. Les itinéraires IoT Hub se composent d’une expression de requête et d’un point de terminaison. Si un message correspond à l’expression, les données sont envoyées jusqu’au point final associé via l’itinéraire. Les points de terminaison peuvent être des Event Hubs, des files d’attente de Service Bus et des rubriques. Dans ce cas, le point de terminaison est un conteneur d’objets Blob dans un compte de stockage. Nous allons utiliser le Portail Azure pour passer en revue l’itinéraire créé par notre script.

1. Ouvrez le [portail Azure](https://portal.azure.com).

1. Choisissez tous les services dans le navigateur de gauche, tapez IoT dans la zone de recherche et sélectionnez **IoT Hub**.

1. Choisissez l’IoT Hub créé à l’étape précédente.

1. Dans le navigateur de côté IoT Hub, choisissez **Routage des messages**.

1. La page de routage des messages comporte deux onglets, **Itinéraires**et **Points de terminaison personnalisés**. Sélectionnez l’onglet **Points de terminaison personnalisés**.

1. Sous **Stockage d’objets Blob**, sélectionnez **turbofanDeviceStorage**.

1. Notez que ce point de terminaison pointe vers un conteneur blob appelé **devicedata** dans le compte de stockage créé à la dernière étape, qui est nommé **iotedgeandml\<suffixe unique\>** .

1. Notez également que le **format de nom de fichier Blob** par défaut a été remplacé par un format personnalisé dans lequel la partition est le dernier élément du nom. Ce format est plus pratique pour les opérations de fichier que nous effectuerons avec Azure Notebooks plus loin dans le tutoriel.

1. Fermez le panneau des détails du point d’extrémité pour revenir à la page **Routage des messages**.

1. Sélectionnez l’onglet **Itinéraires**.

1. Sélectionnez l’itinéraire **turbofanDeviceDataToStorage**.

1. Notez que le point de terminaison de l’itinéraire est le point de terminaison personnalisé **turbofanDeviceStorage**.

1. Examinez la requête de routage, qui est définie sur **true**. Cela signifie que tous les messages de télémétrie de l’appareil correspondront à cet itinéraire et que tous les messages seront envoyés au point de terminaison **turbofanDeviceStorage**.

1. Fermez les détails de routage.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons créé un hub IoT et configuré un itinéraire vers un compte Stockage Azure. Dans l’article suivant, nous enverrons les données d’un ensemble d’appareils simulés via IoT Hub vers le compte de stockage. Plus tard dans le tutoriel, après avoir configuré notre appareil et nos modules IoT Edge, nous revisiterons les itinéraires et examinerons un peu plus la requête de routage.

Pour plus d’informations sur les étapes décrites dans cette partie du tutoriel Machine Learning sur IoT Edge, consultez :

* [Notions de base d’Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/)
* [Configurer le routage des messages avec IoT Hub](../iot-hub/tutorial-routing.md)
* [Créer un IoT Hub à l’aide du Portail Azure](../iot-hub/iot-hub-create-through-portal.md)

Passez à l’article suivant pour créer un appareil simulé à surveiller.

> [!div class="nextstepaction"]
> [Générer les données d’appareil](tutorial-machine-learning-edge-03-generate-data.md)
