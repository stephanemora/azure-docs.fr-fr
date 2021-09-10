---
title: Installer Azure IoT Edge pour Linux sur Windows | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur des appareils Windows
author: kgremban
ms.reviewer: fcabrera
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 6169d3b0f99b2044fbe6076283e176d4dc1a76a7
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272436"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device"></a>Installer et provisionner Azure IoT Edge pour Linux sur un appareil Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Le runtime Azure IoT Edge est ce qui transforme un appareil en appareil IoT Edge. Le runtime peut être déployé sur des appareils allant des simples ordinateurs aux serveurs industriels. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud. Pour en savoir plus, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Azure IoT Edge pour Linux sur Windows permet d’installer IoT Edge sur des machines virtuelles Linux qui s’exécutent sur des appareils Windows. La version Linux d’Azure IoT Edge et tout module Linux déployé en même temps sont exécutés sur la machine virtuelle. À partir de là, les applications Windows, le code, le runtime IoT Edge et les modules peuvent interagir librement.

Cet article répertorie les étapes à suivre pour configurer IoT Edge sur un appareil Windows. Ces étapes déploient une machine virtuelle Linux contenant le runtime IoT Edge à exécuter sur votre appareil Windows, puis approvisionnent l’appareil avec son identité d’appareil IoT Hub.

>[!NOTE]
>IoT Edge pour Linux sur Windows constitue l’expérience recommandée pour utiliser Azure IoT Edge dans un environnement Windows. Toutefois, les conteneurs Windows sont toujours disponibles. Si vous préférez utiliser des conteneurs Windows, consultez [Installation et gestion d’Azure IoT Edge avec des conteneurs Windows](how-to-install-iot-edge-windows-on-windows.md).

## <a name="prerequisites"></a>Prérequis

* Un compte Azure avec un abonnement valide. Si vous n’avez pas [d’abonnement Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) de niveau gratuit ou standard dans Azure

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

* Si vous souhaitez installer et gérer un appareil IoT Edge avec Windows Admin Center, vérifiez que vous avez accès à Windows Admin Center et que l’extension Azure IoT Edge est installée :

   1. Téléchargez et exécutez le [programme d’installation de Windows Admin Center](https://aka.ms/wacdownload). Suivez les invites de l’Assistant Installation pour installer Windows Admin Center.

   1. Une fois l’installation terminée, utilisez un navigateur pris en charge pour ouvrir le Windows Admin Center. Les navigateurs pris en charge sont Microsoft Edge (Windows 10, version 1709 ou ultérieure), Google Chrome et Microsoft Edge Insider.

   1. Lors de la première utilisation de Windows Admin Center, vous êtes invité à sélectionner le certificat à utiliser. Sélectionnez **Client Windows Admin Center** comme certificat.

   1. Installez l’extension Azure IoT Edge. Sélectionnez l’icône d’engrenage en haut à droite du tableau de bord de Windows Admin Center.

      ![Sélectionnez l’icône d’engrenage en haut à droite du tableau de bord pour accéder aux paramètres.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. Dans le menu **Paramètres**, sous **Passerelle**, sélectionnez **Extensions**.

   1. Dans l’onglet **Extensions disponibles**, recherchez **Azure IoT Edge** dans la liste des extensions. Sélectionnez-le et cliquez sur l’invite **Installer** au-dessus de la liste des extensions.

   1. Une fois l’installation terminée, Azure IoT Edge doit apparaître dans la liste des extensions installées dans l’onglet **Extensions installées**.

* Si vous souhaitez utiliser des **modules Linux avec accélération GPU** dans votre déploiement Azure IoT Edge pour Linux sur Windows, il existe plusieurs options de configuration à prendre en compte. Vous devrez installer les pilotes adaptés à votre architecture GPU. Par ailleurs, vous aurez peut-être besoin d’un accès à une build du Programme Windows Insider. Pour déterminer vos besoins en matière de configuration et satisfaire à ces prérequis, consultez [Accélération GPU pour Azure IoT Edge pour Linux sur Windows](gpu-acceleration.md).

## <a name="choose-your-provisioning-method"></a>Choisir votre méthode d’approvisionnement

Azure IoT Edge pour Linux sur Windows prend en charge les méthodes d’approvisionnement suivantes :

* **Provisionnement manuel** pour un seul appareil.

  * Pour préparer le provisionnement manuel, suivez la procédure décrite dans [Inscription d’un appareil IoT Edge dans IoT Hub](how-to-register-device.md). Choisissez l’authentification par clé symétrique ou l’authentification par certificat X.509, puis revenez à cet article pour installer et provisionner IoT Edge.

* **Provisionnement automatique** avec le Service IoT Hub Device Provisioning (DPS) pour un ou plusieurs appareils.

  * Choisissez la méthode d’authentification que vous souhaitez utiliser, puis suivez la procédure de l’article correspondant pour configurer une instance DPS et créer une inscription permettant de provisionner votre ou vos appareils. Pour plus d’informations sur les types d’inscription, consultez l’article [Concepts du service Azure IoT Hub Device Provisioning](../iot-dps/concepts-service.md#enrollment).

    * [Provisionnement d’un appareil IoT Edge avec le Service DPS et des clés symétriques](how-to-auto-provision-symmetric-keys.md)
    * [Provisionnement d’un appareil IoT Edge avec le Service DPS et des certificats X.509](how-to-auto-provision-x509-certs.md)
    * [Provisionnement d’un appareil IoT Edge avec le Service DPS et une attestation TPM](how-to-auto-provision-tpm-linux-on-windows.md)

## <a name="create-a-new-deployment"></a>Créer un déploiement

Déployez Azure IoT Edge pour Linux sur Windows sur votre appareil cible.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Si vous ne l’avez pas déjà fait, installez IoT Edge pour Linux sur Windows sur votre appareil cible.

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

1. Définissez la stratégie d’exécution sur l’appareil cible sur `AllSigned` si ce n’est pas déjà fait. Vous pouvez vérifier la stratégie d’exécution actuelle dans une invite PowerShell avec élévation de privilèges à l’aide de :

   ```powershell
   Get-ExecutionPolicy -List
   ```

   Si la stratégie d’exécution de `local machine` n’est pas `AllSigned`, vous pouvez la définir à l’aide de :

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Créer le déploiement d’IoT Edge pour Linux sur Windows.

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

   Pour utiliser un relais GPU, vous devez ajouter les paramètres **gpuName**, **gpuPassthroughType** et **gpuCount** à votre commande `Deploy-Eflow`. Pour plus d’informations sur tous les paramètres facultatifs disponibles, consultez [Fonctions PowerShell pour IoT Edge pour Linux sur Windows](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow).

   >[!WARNING]
   >L’activation du relais d’appareils matériels peut augmenter les risques liés à la sécurité. Microsoft vous recommande de recourir à un pilote d’atténuation des appareils du fournisseur de votre GPU, le cas échéant. Pour plus d’informations, consultez [Déploiement d’appareils graphiques avec la technologie DDA (Discrete Device Assignment)](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

1. Entrez « Y » pour accepter les termes du contrat de licence.

1. Entrez « O » ou « R » pour activer ou désactiver les **données de diagnostic facultatives** à votre convenance.

1. Une fois le déploiement terminé, la fenêtre PowerShell signale **Déploiement réussi**.

   ![Un déploiement réussi indique « Déploiement réussi » à la fin des messages](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment-2.png)

Une fois votre déploiement terminé, vous êtes prêt à approvisionner votre appareil.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>L’extension Azure IoT Edge pour Windows Admin Center est actuellement en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Les processus d’installation et de gestion peuvent différer de ceux des fonctionnalités généralement disponibles.

Sur la page d’accueil de Windows Admin Center, sous la liste des connexions figure une connexion d’hôte local qui représente le PC sur lequel Windows Admin Center est exécuté. Les autres serveurs, ordinateurs ou clusters que vous gérez s’affichent également ici.

Vous pouvez utiliser Windows Admin Center pour installer et gérer Azure IoT Edge pour Linux sur Windows sur votre appareil local ou des appareils gérés distants. Dans ce guide, la connexion de l’hôte local servira d’appareil cible pour le déploiement d’Azure IoT Edge pour Linux sur Windows.

Si vous préférez effectuer un déploiement sur un appareil cible distant plutôt que sur votre appareil local, et que l’appareil cible souhaité ne figure pas dans la liste, suivez les [instructions pour ajouter votre appareil.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

   ![Tableau de bord initial Windows Admin Center avec l’appareil cible figurant dans la liste](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Sélectionnez **Ajouter**.

1. Dans le volet **Ajouter ou créer des ressources**, localisez la vignette **Azure IoT Edge**. Sélectionnez **Créer** pour installer une nouvelle instance d’Azure IoT Edge pour Linux sur un appareil.

   Si IoT Edge pour Linux sur Windows est déjà exécuté sur un appareil, vous pouvez sélectionner **Ajouter** pour vous connecter à cet appareil IoT Edge existant et le gérer avec Windows Admin Center.

   ![Sélectionnez Créer sur la vignette Azure IoT Edge dans Windows Admin Center](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Le volet **Créer un déploiement d’Azure IoT Edge pour Linux sur Windows** s’ouvre. Sur l’onglet **1. Prise en main**, vérifiez la configuration minimale requise, puis sélectionnez **Suivant**.

1. Passez en revue les termes du contrat de licence, cochez la case **J’accepte** et sélectionnez **Suivant**.

1. Vous pouvez activer ou désactiver les **données de diagnostic facultatives** à votre convenance.

1. Sélectionnez **Suivant : Déployer**.

   ![Sélectionnez le bouton Suivant : Déployer après avoir activé ou désactivé les données de diagnostic facultatives à votre convenance](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. Dans l’onglet **2. Déployer**, sous **Sélectionner un appareil cible**, cliquez sur votre appareil dans la liste pour vérifier qu’il respecte la configuration minimale requise. Lorsque son état indique qu’il est pris en charge, sélectionnez **Suivant**.

   ![Sélectionnez votre appareil pour vérifier qu’il est pris en charge](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Sous l’onglet **Paramètres 2.2**, vérifiez les paramètres de configuration de votre déploiement.

   >[!NOTE]
   >IoT Edge pour Linux sur Windows utilise un commutateur par défaut qui attribue une adresse IP interne à la machine virtuelle Linux. Cette adresse IP interne ne peut pas être atteinte en dehors de l’ordinateur Windows. Vous pouvez vous connecter en local à la machine virtuelle tout en ayant une session ouverte sur l’ordinateur Windows.
   >
   >Si vous utilisez Windows Server, configurez un commutateur par défaut avant de déployer IoT Edge pour Linux sur Windows.

   Vous pouvez affecter un GPU à votre déploiement pour permettre l’utilisation de modules Linux avec accélération GPU. Pour accéder à ces fonctionnalités, vous devez installer les composants requis détaillés dans [Accélération GPU pour Azure IoT Edge pour Linux sur Windows](gpu-acceleration.md). Si vous n’installez ces composants requis qu’à ce stade du processus de déploiement, vous devez recommencer du début.

   Deux options sont disponibles pour le relais GPU disponible : **Direct Device Assignment (DDA)** et **GPU Paravirtualization (GPU-PV)** , en fonction de l’adaptateur GPU attribué au déploiement. Vous trouverez ci-dessous des exemples de chaque méthode.

   Pour la méthode DDA, sélectionnez le nombre de processeurs GPU à allouer à votre machine virtuelle Linux.

   ![Paramètres de configuration avec un GPU DDA activé.](./media/how-to-install-iot-edge-on-windows/gpu-passthrough-direct-device-assignment.png)

   Pour la méthode PV, aucun paramètre supplémentaire n’est nécessaire.

   ![Paramètres de configuration avec un GPU PV activé.](./media/how-to-install-iot-edge-on-windows/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >L’activation du relais d’appareils matériels peut augmenter les risques liés à la sécurité. Microsoft vous recommande de recourir à un pilote d’atténuation des appareils du fournisseur de votre GPU, le cas échéant. Pour plus d’informations, consultez [Déploiement d’appareils graphiques avec la technologie DDA (Discrete Device Assignment)](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

   Une fois que vous êtes satisfait des paramètres, sélectionnez **Suivant**.

1. Dans l’onglet **2.3. Déploiement**, vous pouvez suivre la progression du déploiement. Le processus complet comprend le téléchargement du package Azure IoT Edge pour Linux sur Windows, l’installation du package, la configuration de l’appareil hôte et la configuration de la machine virtuelle Linux. Ce processus peut prendre plusieurs minutes. Voici l’illustration d’un déploiement réussi.

   ![Dans un déploiement réussi, chaque étape comporte une coche verte et la mention « Terminé »](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Une fois votre déploiement terminé, vous êtes prêt à approvisionner votre appareil. Sélectionnez **Suivant : Connecter** pour passer à l’onglet **3. Connecter**, qui gère l’approvisionnement de l’appareil Azure IoT Edge.

---

## <a name="provision-your-device"></a>Approvisionner votre appareil

Choisissez une méthode pour approvisionner votre appareil et suivez les instructions de la section appropriée. Cet article décrit la procédure à suivre pour provisionner manuellement un appareil avec des clés symétriques ou des certificats X.509. Si vous utilisez le provisionnement automatique avec le Service DPS, suivez les liens correspondant pour mener à bien la procédure.

Vous pouvez utiliser Windows Admin Center ou une session PowerShell avec élévation de privilèges pour approvisionner vos appareils.

* Provisionnement manuel :

  * [Provisionnement manuel avec la chaîne de connexion de l’appareil IoT Edge](#manual-provisioning-using-the-connection-string)
  * [Provisionnement manuel avec des certificats X.509](#manual-provisioning-using-x509-certificates)

* Provisionnement automatique :

  * [Provisionnement automatique avec le Service Device Provisioning (DPS) et des clés symétriques](how-to-auto-provision-symmetric-keys.md?tabs=eflow#configure-the-device-with-provisioning-information)
  * [Provisionnement automatique avec le Service DPS et des certificats X.509](how-to-auto-provision-x509-certs.md?tabs=eflow#configure-the-device-with-provisioning-information)
  * [Provisionnement automatique avec le Service DPS et une attestation TPM](how-to-auto-provision-tpm-linux-on-windows.md#configure-the-device-with-provisioning-information)

### <a name="manual-provisioning-using-the-connection-string"></a>Provisionnement manuel avec la chaîne de connexion

Cette section traite du provisionnement manuel de l’appareil Azure IoT Edge avec sa chaîne de connexion.

Si vous ne l’avez pas déjà fait, suivez la procédure décrite dans [Inscription d’un appareil IoT Edge dans IoT Hub](how-to-register-device.md) pour inscrire votre appareil et récupérer sa chaîne de connexion.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Exécutez la commande suivante dans une session PowerShell avec élévation de privilèges sur votre appareil cible. Remplacez le texte de l’espace réservé par vos propres valeurs.

```powershell
Provision-EflowVm -provisioningType ManualConnectionString -devConnString "<CONNECTION_STRING_HERE>"
```

Pour plus d’informations sur la commande `Provision-EflowVM`, consultez [Fonctions PowerShell pour IoT Edge pour Linux sur Windows](reference-iot-edge-for-linux-on-windows-functions.md#provision-eflowvm).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Dans le volet **Approvisionnement de l’appareil Azure IoT Edge**, sélectionnez **Chaîne de connexion (manuel)** dans le menu déroulant des méthodes d’approvisionnement.

1. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à l’onglet **IoT Edge** de votre IoT Hub.

1. Cliquez sur l’ID d’appareil de votre appareil. Copiez le champ **Chaîne de connexion principale**.

1. Indiquez la **Chaîne de connexion d’appareil** que vous avez récupérée sur IoT Hub après avoir inscrit l’appareil.

1. Sélectionnez **Provisionnement avec la méthode sélectionnée**.

   ![Choisissez Approvisionnement avec la méthode sélectionnée après avoir collé la chaîne de connexion de votre appareil](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. Une fois l’approvisionnement terminé, sélectionnez **Terminer**. Vous êtes redirigé vers le tableau de bord principal. Un nouvel appareil, dont le type est `IoT Edge Devices`, apparaît maintenant dans la liste. Vous pouvez sélectionner l’appareil IoT Edge pour vous y connecter. Une fois sur la page de **vue d’ensemble**, vous pouvez consulter la **liste des modules IoT Edge** et l’**état IoT Edge** de votre appareil.

---

### <a name="manual-provisioning-using-x509-certificates"></a>Provisionnement manuel avec des certificats X.509

Cette section traite du provisionnement manuel de l’appareil Azure IoT Edge avec des certificats X.509.

Si vous ne l’avez pas déjà fait, suivez la procédure décrite dans [Inscription d’un appareil IoT Edge dans IoT Hub](how-to-register-device.md) pour préparer les certificats nécessaires et inscrire votre appareil. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Préparez le certificat d’identité d’appareil et la clé privée correspondante sur votre appareil cible. Prenez connaissance du chemin absolu des deux fichiers.

Exécutez la commande suivante dans une session PowerShell avec élévation de privilèges sur votre appareil cible. Remplacez le texte de l’espace réservé par vos propres valeurs.

```powershell
Provision-EflowVm -provisioningType ManualX509 -iotHubHostname "<HUB HOSTNAME>" -deviceId "<DEVICE ID>" -identityCertPath "<ABSOLUTE PATH TO IDENTITY CERT>" -identityPrivKeyPath "<ABSOLUTE PATH TO PRIVATE KEY>"
```

Pour plus d’informations sur la commande `Provision-EflowVM`, consultez [Fonctions PowerShell pour IoT Edge pour Linux sur Windows](reference-iot-edge-for-linux-on-windows-functions.md#provision-eflowvm).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Dans le volet **Provisionnement de l’appareil Azure IoT Edge**, sélectionnez **ManualX509** dans le menu déroulant des méthodes de provisionnement.

   ![Choix du provisionnement manuel avec des certificats X.509](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-manual-x509.png)

1. Indiquez les paramètres requis :

   * **Nom d’hôte IoT Hub** : nom du hub IoT auprès duquel cet appareil est inscrit.
   * **ID de l’appareil** : nom sous lequel cet appareil est inscrit.
   * **Fichier de certificat** : chargez le certificat d’identité d’appareil, qui sera déplacé vers la machine virtuelle et utilisé pour provisionner l’appareil.
   * **Fichier de clé privée** : chargez le fichier de clé privée correspondant, qui sera déplacé vers la machine virtuelle et utilisé pour provisionner l’appareil.

1. Sélectionnez **Provisionnement avec la méthode sélectionnée**.

1. Une fois l’approvisionnement terminé, sélectionnez **Terminer**. Vous êtes redirigé vers le tableau de bord principal. Un nouvel appareil, dont le type est `IoT Edge Devices`, apparaît maintenant dans la liste. Vous pouvez sélectionner l’appareil IoT Edge pour vous y connecter. Une fois sur la page de **vue d’ensemble**, vous pouvez consulter la **liste des modules IoT Edge** et l’**état IoT Edge** de votre appareil.

---

## <a name="verify-successful-configuration"></a>Vérifier que la configuration a réussi

Vérifiez que IoT Edge pour Linux sur Windows a été correctement installé et configuré sur votre appareil IoT Edge.

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

    1. Si vous avez besoin de résoudre les problèmes du service, récupérez les journaux d’activité de ce dernier.

       ```bash
       sudo journalctl -u iotedge
       ```

    2. Utilisez l’outil `check` pour vérifier l’état de la configuration et de la connexion de l’appareil.

       ```bash
       sudo iotedge check
       ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

> [!NOTE]
> Si vous utilisez des fonctions publiques PowerShell IoT Edge pour Linux sur Windows, veillez à définir la stratégie d’exécution de l’appareil cible sur `AllSigned`. Assurez-vous que tous les prérequis des [fonctions PowerShell pour IoT Edge pour Linux sur Windows](reference-iot-edge-for-linux-on-windows-functions.md) sont remplis.

1. Sélectionnez votre appareil IoT Edge dans la liste des appareils connectés dans Windows Admin Center pour vous y connecter.

1. La page de vue d’ensemble de l’appareil affiche des informations sur l’appareil :

   * La section **Liste des modules IoT Edge** affiche les modules en cours d’exécution sur l’appareil. Lors du premier démarrage du service IoT Edge, seul le module **edgeAgent** apparaît dans cette liste. Le module edgeAgent s’exécute par défaut et vous aide à installer et démarrer tous les modules supplémentaires que vous déployez sur votre appareil.

   * La section **État IoT Edge** indique l’état du service, qui doit être **actif (en cours d’exécution)** .

---

Lorsque vous créez un appareil IoT Edge, il affichera le code d’état `417 -- The device's deployment configuration is not set` dans le portail Azure. Cet état est normal et signifie que l’appareil est prêt à recevoir un déploiement de module.

## <a name="next-steps"></a>Étapes suivantes

* Passez à [Déployer des modules IoT Edge](how-to-deploy-modules-portal.md) pour savoir comment déployer des modules sur votre appareil.
* Découvrez comment [gérer les certificats sur votre IoT Edge pour Linux sur une machine virtuelle Windows](how-to-manage-device-certificates.md) et à transférer des fichiers du système d’exploitation hôte vers votre machine virtuelle Linux.
* Découvrez comment [configurer vos appareils IoT Edge pour qu’ils communiquent par le biais d’un serveur proxy](how-to-configure-proxy-support.md).
