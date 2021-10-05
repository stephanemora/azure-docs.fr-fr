---
title: Créer et approvisionner des appareils IoT Edge à l’aide de clés symétriques pour Linux sur Windows - Azure IoT Edge | Microsoft Docs
description: Utiliser l’attestation de clés symétriques pour tester l’approvisionnement de Linux sur des appareils Windows à grande échelle pour Azure IoT Edge avec le service Device Provisioning
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d7d33b2f840b88226883bc9e6aff2823d7aa6cd2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700592"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-symmetric-keys"></a>Créer et approvisionner des IoT Edge pour Linux sur des appareils Windows à grande échelle à l’aide de clés symétriques 

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Cet article donne des instructions complètes pour l’approvisionnement automatique d’un ou plusieurs [IoT Edge pour Linux sur des appareils Windows](iot-edge-for-linux-on-windows.md) à l’aide de clés symétriques. Les appareils Azure IoT Edge peuvent être approvisionnés automatiquement à l’aide du [service Azure IoT Hub Device Provisioning](../iot-dps/index.yml) (DPS). Si vous ne connaissez pas le processus d’approvisionnement automatique, consultez la [présentation du provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Voici les tâches à effectuer :

1. Créer une **inscription individuelle** pour un seul appareil ou une **inscription de groupe** pour un ensemble d’appareils.
1. Déployer une machine virtuelle Linux avec le runtime IoT Edge installé et la connecter à IoT Hub.

L’attestation de clé symétrique est une approche simple pour authentifier un appareil avec une instance du service Device Provisioning. Cette méthode d’attestation représente une expérience « Hello world » pour les développeurs qui découvrent le provisionnement d’appareils ou n’ont pas d’exigences de sécurité strictes. L’attestation d’appareil avec un [Module de plateforme sécurisée (TPM)](../iot-dps/concepts-tpm-attestation.md) ou un [certificat X.509](../iot-dps/concepts-x509-attestation.md) est plus sécurisée, et doit être utilisée lorsque les exigences de sécurité sont plus strictes.

## <a name="prerequisites"></a>Prérequis

Ressources cloud :

* Un hub IoT actif.
* Une instance du service IoT Hub Device Provisioning dans Azure, liée à votre hub IoT.
  * Si vous ne disposez pas d’une instance du service Device Provisioning, vous pouvez suivre les instructions fournies dans les sections [Créer un service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) et [Lier le hub IoT et votre service Device Provisioning](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) du guide de démarrage rapide du service IoT Hub Device Provisioning.
  * Après avoir lancé l’exécution du service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge.

Une machine de développement :

* Un appareil Windows avec la configuration requise minimum suivante :
  * Windows 10 version 1809 ou ultérieure ; build 17763 ou ultérieure
  * Éditions Professionnel, Entreprise ou Serveur
  * Mémoire disponible minimale : 1 Go
  * Espace disque disponible minimal : 10 Go
  * Prise en charge de la virtualisation
    * Sur Windows 10, activez Hyper-V. Pour plus d’informations, consultez [Installation de Hyper-V sur Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).
    * Sur Windows Server, installez le rôle Hyper-V et créez un commutateur réseau par défaut. Pour plus d’informations, consultez [Virtualisation imbriquée pour Azure IoT Edge pour Linux sur Windows](nested-virtualization.md).
    * Sur une machine virtuelle, configurez la virtualisation imbriquée. Pour plus d’informations, consultez [Virtualisation imbriquée](nested-virtualization.md).
  * Prise en charge du réseau
    * Windows Server n’est pas fourni avec un commutateur par défaut. Pour pouvoir déployer EFLOW sur un appareil Windows Server, vous devez créer un commutateur virtuel.  Pour plus d’informations, consultez [Création d’un commutateur virtuel pour Linux sur Windows](how-to-create-virtual-switch.md).
    * Les versions Windows Desktop sont fournies avec un commutateur par défaut utilisable pour l’installation d’EFLOW. Si nécessaire, vous pouvez créer votre propre commutateur virtuel personnalisé.

   > [!TIP]
   > Si vous souhaitez utiliser des **modules Linux avec accélération GPU** dans votre déploiement Azure IoT Edge pour Linux sur Windows, il existe plusieurs options de configuration à prendre en compte. Vous devrez installer les pilotes adaptés à votre architecture GPU. Par ailleurs, vous aurez peut-être besoin d’un accès à une build du Programme Windows Insider. Pour déterminer vos besoins en matière de configuration et satisfaire à ces prérequis, consultez [Accélération GPU pour Azure IoT Edge pour Linux sur Windows](gpu-acceleration.md).

Vous pouvez utiliser **PowerShell** ou **Windows Admin Center** pour gérer vos appareils IoT Edge. Chaque utilitaire possède sa propre configuration requise :

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Si vous souhaitez utiliser PowerShell, suivez les étapes ci-dessous pour préparer votre appareil cible à l’installation d’Azure IoT Edge pour Linux sur Windows et au déploiement de la machine virtuelle Linux :

1. Définissez la stratégie d’exécution sur l’appareil cible sur `AllSigned`. Vous pouvez vérifier la stratégie d’exécution actuelle dans une invite PowerShell avec élévation de privilèges à l’aide de la commande suivante :

   ```powershell
   Get-ExecutionPolicy -List
   ```

   Si la stratégie d’exécution de `local machine` n’est pas `AllSigned`, vous pouvez la définir à l’aide de :

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Pour en savoir plus sur le module PowerShell d’Azure IoT Edge pour Linux sur Windows, consultez la [documentation de référence sur les fonctions PowerShell](reference-iot-edge-for-linux-on-windows-functions.md).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Si vous souhaitez utiliser Windows Admin Center, suivez les étapes ci-dessous pour télécharger et installer Windows Admin Center et installer l’extension Azure IoT Edge de Windows Admin Center :

   1. Téléchargez et exécutez le [programme d’installation de Windows Admin Center](https://aka.ms/wacdownload). Suivez les invites de l’Assistant Installation pour installer Windows Admin Center.

   1. Une fois l’installation terminée, utilisez un navigateur pris en charge pour ouvrir le Windows Admin Center. Les navigateurs pris en charge sont Microsoft Edge (Windows 10, version 1709 ou ultérieure), Google Chrome et Microsoft Edge Insider.

   1. Lors de la première utilisation de Windows Admin Center, vous êtes invité à sélectionner le certificat à utiliser. Sélectionnez **Client Windows Admin Center** comme certificat.

   1. Installez l’extension Azure IoT Edge. Sélectionnez l’icône d’engrenage en haut à droite du tableau de bord de Windows Admin Center.

      ![Sélectionnez l’icône d’engrenage en haut à droite du tableau de bord pour accéder aux paramètres, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. Dans le menu **Paramètres**, sous **Passerelle**, sélectionnez **Extensions**.

   1. Dans l’onglet **Extensions disponibles**, recherchez **Azure IoT Edge** dans la liste des extensions. Sélectionnez-le et cliquez sur l’invite **Installer** au-dessus de la liste des extensions.

   1. Une fois l’installation terminée, Azure IoT Edge doit apparaître dans la liste des extensions installées dans l’onglet **Extensions installées**.

---

## <a name="create-a-dps-enrollment"></a>Créer une inscription au service Device Provisioning

Créez une inscription pour approvisionner un ou plusieurs appareils via DPS.

Si vous envisagez d’approvisionner un seul appareil IoT Edge, créez une **inscription individuelle**. Si vous avez besoin d’approvisionner plusieurs appareils, suivez les étapes de création d’une **inscription de groupe** DPS.

Lorsque vous créez une inscription auprès du service Device Provisioning, vous avez la possibilité de déclarer un **état initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des métriques dont vous avez besoin dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](how-to-deploy-at-scale.md).

Pour plus d’informations sur les inscriptions dans le service Device Provisioning, consultez [Guide pratique pour gérer les inscriptions d’appareils](../iot-dps/how-to-manage-enrollments.md).

# <a name="individual-enrollment"></a>[Inscription individuelle](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>Créer une inscription individuelle dans le service Device Provisioning

> [!TIP]
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions individuelles à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne un appareil IoT Edge.

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
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions de groupe à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne des appareils IoT Edge. Pour une inscription de groupe, tous les appareils doivent être de IoT Edge, ou aucun d’entre eux ne peut l’être.

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

Maintenant que le groupe d’inscriptions existe, le runtime IoT Edge peut approvisionner automatiquement des appareils lors de l’installation.

#### <a name="derive-a-device-key"></a>Dériver une clé d’appareil

Chaque appareil provisionné dans le cadre d’une inscription de groupe a besoin d’une clé d’appareil dérivée pour effectuer une attestation de clé symétrique avec l’inscription lors du provisionnement.

Pour générer une clé d’appareil, utilisez la clé que vous avez copiée à partir de votre groupe d’inscriptions DPS pour calculer un [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) de l’ID d’inscription unique pour l’appareil et convertir le résultat au format Base64.

> [!IMPORTANT]
> N’incluez pas la clé primaire ou secondaire de votre inscription dans le code de votre appareil.

Sur Windows, vous pouvez utiliser PowerShell pour générer votre clé d’appareil dérivée, comme indiqué dans l’exemple suivant.

Remplacez la valeur **KEY** par la **clé primaire** que vous avez notée précédemment.

Remplacez la valeur de **REG_ID** avec l’ID d’inscription de votre appareil.

```powershell
$KEY='PASTE_YOUR_ENROLLMENT_KEY_HERE'
$REG_ID='PASTE_YOUR_REGISTRATION_ID_HERE'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

Voici un exemple de sortie d’une clé d’appareil dérivée :

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---

## <a name="install-iot-edge"></a>Installer IoT Edge

Déployez Azure IoT Edge pour Linux sur Windows sur votre appareil cible.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installez IoT Edge pour Linux sur Windows sur votre appareil cible.

> [!NOTE]
> Le processus PowerShell suivant explique comment déployer IoT Edge pour Linux sur Windows sur l’appareil local. Pour effectuer un déploiement sur un appareil cible distant avec PowerShell, vous pouvez utiliser [PowerShell distant](/powershell/module/microsoft.powershell.core/about/about_remote) afin d’établir une connexion à un appareil distant et d’exécuter ces commandes à distance sur cet appareil.

1. Dans une session PowerShell avec élévation de privilèges, exécutez chacune des commandes suivantes pour télécharger IoT Edge pour Linux sur Windows.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Installez IoT Edge pour Linux sur Windows sur votre appareil.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   Vous pouvez spécifier des répertoires personnalisés d’installation d’IoT Edge pour Linux sur Windows et VHDX en ajoutant les paramètres `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` et `VHDXDIR="<FULLY_QUALIFIED_PATH>"` à la commande d’installation.

1. Définissez la stratégie d’exécution sur l’appareil cible sur `AllSigned` si ce n’est pas déjà fait. Consultez la configuration requise des commandes PowerShell pour vérifier la stratégie d’exécution actuelle et définir la stratégie d’exécution sur `AllSigned`.

1. Créer le déploiement d’IoT Edge pour Linux sur Windows. Le déploiement crée votre machine virtuelle Linux et installe le runtime IoT Edge pour vous.

   ```powershell
   Deploy-Eflow
   ```

   >[!TIP]
   >Par défaut, la commande `Deploy-Eflow` crée votre machine virtuelle Linux avec 1 Go de RAM, 1 noyau de processeur virtuel et 16 Go d’espace disque. Toutefois, les ressources dont votre machine virtuelle a besoin dépendent fortement des charges de travail que vous déployez. Si votre machine virtuelle ne dispose pas de suffisamment de mémoire pour prendre en charge vos charges de travail, elle ne démarrera pas.
   >
   >Vous pouvez personnaliser les ressources disponibles de la machine virtuelle à l’aide des paramètres facultatifs de la commande `Deploy-Eflow`.
   >
   >Par exemple, la commande ci-dessous crée une machine virtuelle avec 4 cœurs de processeur virtuel, 4 Go de RAM et 20 Go d’espace disque :
   >
   >   ```powershell
   >   Deploy-Eflow -cpuCount 4 -memoryInMB 4096 -vmDiskSize 20
   >   ```
   >
   >Pour plus d’informations sur tous les paramètres facultatifs disponibles, consultez [Fonctions PowerShell pour IoT Edge pour Linux sur Windows](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   Vous pouvez affecter un GPU à votre déploiement pour permettre l’utilisation de modules Linux avec accélération GPU. Pour accéder à ces fonctionnalités, vous devez installer les composants requis détaillés dans [Accélération GPU pour Azure IoT Edge pour Linux sur Windows](gpu-acceleration.md).

   Pour utiliser un relais GPU, ajoutez les paramètres **gpuName**, **gpuPassthroughType** et **gpuCount** à votre commande `Deploy-Eflow`. Pour plus d’informations sur tous les paramètres facultatifs disponibles, consultez [Fonctions PowerShell pour IoT Edge pour Linux sur Windows](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   >[!WARNING]
   >L’activation du relais d’appareils matériels peut augmenter les risques liés à la sécurité. Microsoft vous recommande de recourir à un pilote d’atténuation des appareils du fournisseur de votre GPU, le cas échéant. Pour plus d’informations, consultez [Déploiement d’appareils graphiques avec la technologie DDA (Discrete Device Assignment)](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

1. Entrez « Y » pour accepter les termes du contrat de licence.

1. Entrez « O » ou « R » pour activer ou désactiver les **données de diagnostic facultatives** à votre convenance.

1. Une fois le déploiement terminé, la fenêtre PowerShell signale **Déploiement réussi**.

   ![Un déploiement réussi indique « Déploiement réussi » à la fin des messages, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/successful-powershell-deployment.png)

Une fois votre déploiement terminé, vous êtes prêt à approvisionner votre appareil.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>L’extension Azure IoT Edge pour Windows Admin Center est actuellement en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Les processus d’installation et de gestion peuvent différer de ceux des fonctionnalités généralement disponibles.

Installez Azure IoT Edge pour Linux sur Windows sur votre appareil.

1. Téléchargez le [programme d’installation Azure IoT Edge pour Linux sur Windows](https://aka.ms/AzEflowMSI).

1. Après avoir installé Azure IoT Edge pour Linux sur Windows, ouvrez Windows Admin Center.

   Sur la page d’accueil de Windows Admin Center, sous la liste des connexions figure une connexion d’hôte local qui représente le PC sur lequel Windows Admin Center est exécuté. Les autres serveurs, ordinateurs ou clusters que vous gérez s’affichent également ici.

   Vous pouvez utiliser Windows Admin Center pour installer et gérer Azure IoT Edge pour Linux sur Windows sur votre appareil local ou des appareils gérés distants. Dans ce guide, la connexion de l’hôte local servira d’appareil cible pour le déploiement d’Azure IoT Edge pour Linux sur Windows.

1. Vérifiez que votre appareil local est répertorié sous **Toutes les connexions**, comme indiqué ci-dessous.

   ![Tableau de bord initial Windows Admin Center avec l’appareil cible figurant dans la liste, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/windows-admin-center-initial-dashboard.png)

   Si vous préférez effectuer un déploiement sur un appareil cible distant plutôt que sur votre appareil local, et que l’appareil cible souhaité ne figure pas dans la liste, suivez les [instructions pour ajouter votre appareil](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

1. Sélectionnez **+ Ajouter** pour commencer à créer votre déploiement. Le déploiement crée votre machine virtuelle Linux et installe le runtime IoT Edge pour vous.

1. Dans le volet **Ajouter ou créer des ressources**, localisez la vignette **Azure IoT Edge**. Sélectionnez **Créer** pour installer une nouvelle instance d’Azure IoT Edge pour Linux sur un appareil.

   Si IoT Edge pour Linux sur Windows est déjà exécuté sur un appareil, vous pouvez sélectionner **Ajouter** pour vous connecter à cet appareil IoT Edge existant et le gérer avec Windows Admin Center.

   ![Sélectionnez Créer sur la vignette Azure IoT Edge dans Windows Admin Center, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/resource-creation-tiles.png)

1. Le volet **Créer un déploiement d’Azure IoT Edge pour Linux sur Windows** s’ouvre. Sur l’onglet **1. Prise en main**, vérifiez la configuration minimale requise, puis sélectionnez **Suivant**.

1. Passez en revue les termes du contrat de licence, cochez la case **J’accepte** et sélectionnez **Suivant**.

1. Vous pouvez activer ou désactiver les **données de diagnostic facultatives** à votre convenance.

1. Sélectionnez **Suivant : Déployer**.

   ![Sélectionnez le bouton Suivant : Déployer après avoir activé ou désactivé les données de diagnostic facultatives à votre convenance, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/select-next-deploy.png)

1. Dans l’onglet **2. Déployer**, sous **Sélectionner un appareil cible**, cliquez sur votre appareil dans la liste pour vérifier qu’il respecte la configuration minimale requise. Lorsque son état indique qu’il est pris en charge, sélectionnez **Suivant**.

   ![Sélectionnez votre appareil pour vérifier qu’il est pris en charge, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/evaluate-supported-device.png)

1. Sous l’onglet **Paramètres 2.2**, vérifiez les paramètres de configuration de votre déploiement.

   >[!NOTE]
   >IoT Edge pour Linux sur Windows utilise un commutateur par défaut qui attribue une adresse IP interne à la machine virtuelle Linux. Cette adresse IP interne ne peut pas être atteinte en dehors de l’ordinateur Windows. Vous pouvez vous connecter en local à la machine virtuelle tout en ayant une session ouverte sur l’ordinateur Windows.
   >
   >Si vous utilisez Windows Server, [configurez un commutateur par défaut](how-to-create-virtual-switch.md) avant de déployer IoT Edge pour Linux sur Windows.

   Vous pouvez affecter un GPU à votre déploiement pour permettre l’utilisation de modules Linux avec accélération GPU. Pour accéder à ces fonctionnalités, vous devez installer les composants requis détaillés dans [Accélération GPU pour Azure IoT Edge pour Linux sur Windows](gpu-acceleration.md). Si vous n’installez ces composants requis qu’à ce stade du processus de déploiement, vous devez recommencer du début.

   Deux options sont disponibles pour le relais GPU disponible : **Direct Device Assignment (DDA)** et **GPU Paravirtualization (GPU-PV)** , en fonction de l’adaptateur GPU attribué au déploiement. Vous trouverez ci-dessous des exemples de chaque méthode.

   Pour la méthode DDA, sélectionnez le nombre de processeurs GPU à allouer à votre machine virtuelle Linux.

   ![Paramètres de configuration avec un GPU DDA activé, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/gpu-passthrough-direct-device-assignment.png)

   Pour la méthode PV, aucun paramètre supplémentaire n’est nécessaire.

   ![Paramètres de configuration avec un GPU PV activé, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >L’activation du relais d’appareils matériels peut augmenter les risques liés à la sécurité. Microsoft vous recommande de recourir à un pilote d’atténuation des appareils du fournisseur de votre GPU, le cas échéant. Pour plus d’informations, consultez [Déploiement d’appareils graphiques avec la technologie DDA (Discrete Device Assignment)](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

   Une fois que vous êtes satisfait des paramètres, sélectionnez **Suivant**.

1. Dans l’onglet **2.3. Déploiement**, vous pouvez suivre la progression du déploiement. Le processus complet comprend le téléchargement du package Azure IoT Edge pour Linux sur Windows, l’installation du package, la configuration de l’appareil hôte et la configuration de la machine virtuelle Linux. Ce processus peut prendre plusieurs minutes. Voici l’illustration d’un déploiement réussi.

   ![Dans un déploiement réussi, chaque étape comporte une coche verte et la mention « Terminé », PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/successful-deployment.png)

Une fois votre déploiement terminé, vous êtes prêt à approvisionner votre appareil. Sélectionnez **Suivant : Connecter** pour passer à l’onglet **3. Connecter**, qui gère l’approvisionnement de l’appareil Azure IoT Edge.

---

## <a name="configure-the-device-with-provisioning-information"></a>Configurer l’appareil avec des informations de provisionnement

Une fois que le runtime est installé sur votre appareil, configurez l’appareil avec les informations qu’il utilise pour se connecter au service Device Provisioning (DPS) et à IoT Hub.

Préparez les informations suivantes :

* Valeur **Étendue de l’ID** du service Device Provisioning
* L’**ID d’inscription** de l’appareil que vous avez créé
* Entrez la **clé primaire** d’une inscription spécifique ou une [clé dérivée](#derive-a-device-key) pour les appareils utilisant une inscription de groupe.

Vous pouvez utiliser PowerShell ou Windows Admin Center pour provisionner votre appareil IoT Edge.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour PowerShell, exécutez la commande suivante en mettant à jour les valeurs d’espace réservé avec vos propres valeurs :

```powershell
Provision-EflowVm -provisioningType DpsSymmetricKey -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -symmKey PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Pour Windows Admin Center, procédez comme suit :

1. Dans le volet **Approvisionnement de l’appareil Azure IoT Edge**, sélectionnez **Clés symétriques (DPS)** dans le menu déroulant des méthodes d’approvisionnement.

1. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à votre instance DPS.

1. Indiquez votre ID d’étendue DPS, l’ID d’inscription de l’appareil et la clé primaire ou la clé dérivée de l’inscription dans les champs Windows Admin Center.

1. Sélectionnez **Approvisionnement avec la méthode sélectionnée**.

   ![Sélectionnez Approvisionnement avec la méthode sélectionnée après avoir renseigné les champs obligatoires pour l’approvisionnement avec clé symétrique, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/provisioning-with-selected-method-symmetric-key.png)

1. Une fois l’approvisionnement terminé, sélectionnez **Terminer**. Vous êtes redirigé vers le tableau de bord principal. Un nouvel appareil, dont le type est `IoT Edge Devices`, doit maintenant apparaître dans la liste. Vous pouvez sélectionner l’appareil IoT Edge pour vous y connecter. Une fois sur la page de **vue d’ensemble**, vous pouvez consulter la **liste des modules IoT Edge** et l’**état IoT Edge** de votre appareil.

---

## <a name="verify-successful-configuration"></a>Vérifier que la configuration a réussi

Vérifiez que IoT Edge pour Linux sur Windows a été correctement installé et configuré sur votre appareil IoT Edge.

# <a name="individual-enrollment"></a>[Inscription individuelle](#tab/individual-enrollment)

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service Device Provisioning a été utilisée. Dans le Portail Azure, accédez à l’instance du service Device Provisioning. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste.

# <a name="group-enrollment"></a>[Inscription de groupe](#tab/group-enrollment)

Vous pouvez vérifier que l’inscription de groupe que vous avez créée dans le service Device Provisioning a été utilisée. Dans le Portail Azure, accédez à l’instance du service Device Provisioning. Ouvrez les détails de l’inscription pour l’inscription de groupe que vous avez créée. Accédez à l’onglet **Enregistrements d’inscription** pour afficher tous les appareils enregistrés dans ce groupe.

---

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Connectez-vous à votre IoT Edge pour Linux sur machine virtuelle Windows en utilisant la commande suivante dans votre session PowerShell :

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >Le seul compte autorisé à accéder via SSH à la machine virtuelle est celui de l’utilisateur qui l’a créée.

1. Une fois connecté, vous pouvez vérifier la liste des modules IoT Edge en cours d’exécution à l’aide de la commande Linux suivante :

   ```bash
   sudo iotedge list
   ```

1. Si vous devez dépanner le service IoT Edge, utilisez les commandes Linux suivantes.

    1. Récupérez les journaux de service.

       ```bash
       sudo journalctl -u iotedge
       ```

    2. Utilisez l’outil `check` pour vérifier l’état de la configuration et de la connexion de l’appareil.

       ```bash
       sudo iotedge check
       ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Sélectionnez votre appareil IoT Edge dans la liste des appareils connectés dans Windows Admin Center pour vous y connecter.

1. La page de vue d’ensemble de l’appareil affiche des informations sur l’appareil :

   * La section **Liste des modules IoT Edge** affiche les modules en cours d’exécution sur l’appareil. Lors du premier démarrage du service IoT Edge, seul le module **edgeAgent** apparaît dans cette liste. Le module edgeAgent s’exécute par défaut et vous aide à installer et démarrer tous les modules supplémentaires que vous déployez sur votre appareil.

   * La section **État IoT Edge** indique l’état du service, qui doit être **actif (en cours d’exécution)** .

---

Lorsque vous créez un appareil IoT Edge, il affichera le code d’état `417 -- The device's deployment configuration is not set` dans le portail Azure. Cet état est normal et signifie que l’appareil est prêt à recevoir un déploiement de module.

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service Device Provisioning vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md).

Vous pouvez également :

* Passez à [Déployer des modules IoT Edge](how-to-deploy-modules-portal.md) pour savoir comment déployer des modules sur votre appareil.
* Découvrez comment [gérer les certificats sur votre IoT Edge pour Linux sur une machine virtuelle Windows](how-to-manage-device-certificates.md) et à transférer des fichiers du système d’exploitation hôte vers votre machine virtuelle Linux.
* Découvrez comment [configurer vos appareils IoT Edge pour qu’ils communiquent par le biais d’un serveur proxy](how-to-configure-proxy-support.md).
