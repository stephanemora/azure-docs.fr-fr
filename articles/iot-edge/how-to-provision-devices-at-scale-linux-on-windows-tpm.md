---
title: Approvisionner un appareil IoT Edge pour Linux sur Windows à l’aide d’un TPM - Azure IoT Edge | Microsoft Docs
description: Utiliser un TPM simulé sur un appareil Linux sur Windows afin de tester le service de provisionnement des appareils Azure pour Azure IoT Edge
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 07/08/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: d6943fa78960760a665f0abad51f0839da9d63bb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661865"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-at-scale-using-a-tpm"></a>Créer et approvisionner un appareil IoT Edge pour Linux sur Windows à grande échelle à l’aide d’un TPM

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Cet article fournit des instructions de bout en bout concernant l’approvisionnement automatique d’un appareil IoT Edge pour Linux sur Windows avec un Module de plateforme sécurisée (TPM). Vous pouvez provisionner automatiquement des appareils Azure IoT Edge à l’aide du [service Device Provisioning (DPS) Azure IoT Hub](../iot-dps/index.yml). Si vous ne connaissez pas le processus de provisionnement automatique, consultez la présentation du [provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Cet article décrit deux méthodologies. Sélectionnez votre préférence en fonction de l’architecture de votre solution :

1. Provisionnement automatique d’un appareil Linux sur Windows avec du matériel TPM physique.
1. Provisionnement automatique d’un appareil Linux sur Windows à l’aide d’un TPM simulé. Cette méthodologie est recommandée comme scénario de test uniquement, car un TPM simulé n’offre pas la même sécurité qu’un TPM physique.

Voici les tâches à effectuer :

# <a name="physical-tpm"></a>[TPM physique](#tab/physical-tpm)

* Installez IoT Edge pour Linux sur Windows.
* Récupérez les informations du module de plateforme sécurisée à partir de votre appareil.
* Création d’une inscription individuelle pour l’appareil.
* Approvisionnez votre appareil avec ses informations de module de plateforme sécurisée.

# <a name="simulated-tpm"></a>[TPM simulé](#tab/simulated-tpm)

* Installez IoT Edge pour Linux sur Windows.
* Configurez votre TPM simulé et récupérez ses informations de configuration.
* Création d’une inscription individuelle pour l’appareil.
* Approvisionnez votre appareil avec ses informations de module de plateforme sécurisée.

---

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

      ![Sélectionnez l’icône d’engrenage en haut à droite du tableau de bord pour accéder aux paramètres.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/select-gear-icon.png)

   1. Dans le menu **Paramètres**, sous **Passerelle**, sélectionnez **Extensions**.

   1. Dans l’onglet **Extensions disponibles**, recherchez **Azure IoT Edge** dans la liste des extensions. Sélectionnez-le et cliquez sur l’invite **Installer** au-dessus de la liste des extensions.

   1. Une fois l’installation terminée, Azure IoT Edge doit apparaître dans la liste des extensions installées dans l’onglet **Extensions installées**.

---

> [!NOTE]
> TPM 2.0 est requis pour utiliser l’attestation TPM avec DPS.
>
> Il est possible de créer des inscriptions DPS individuelles et non des inscriptions DPS groupées avec un module TPM.

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

   ![Un déploiement réussi indique « Déploiement réussi » à la fin des messages.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/successful-powershell-deployment.png)

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

   ![Tableau de bord initial Windows Admin Center avec l’appareil cible figurant dans la liste, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/windows-admin-center-initial-dashboard.png)

   Si vous préférez effectuer un déploiement sur un appareil cible distant plutôt que sur votre appareil local, et que l’appareil cible souhaité ne figure pas dans la liste, suivez les [instructions pour ajouter votre appareil](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

1. Sélectionnez **+ Ajouter** pour commencer à créer votre déploiement. Le déploiement crée votre machine virtuelle Linux et installe le runtime IoT Edge pour vous.

1. Dans le volet **Ajouter ou créer des ressources**, localisez la vignette **Azure IoT Edge**. Sélectionnez **Créer** pour installer une nouvelle instance d’Azure IoT Edge pour Linux sur un appareil.

   Si IoT Edge pour Linux sur Windows est déjà exécuté sur un appareil, vous pouvez sélectionner **Ajouter** pour vous connecter à cet appareil IoT Edge existant et le gérer avec Windows Admin Center.

   ![Sélectionnez Créer sur la vignette Azure IoT Edge dans Windows Admin Center.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/resource-creation-tiles.png)

1. Le volet **Créer un déploiement d’Azure IoT Edge pour Linux sur Windows** s’ouvre. Sur l’onglet **1. Prise en main**, vérifiez la configuration minimale requise, puis sélectionnez **Suivant**.

1. Passez en revue les termes du contrat de licence, cochez la case **J’accepte** et sélectionnez **Suivant**.

1. Vous pouvez activer ou désactiver les **données de diagnostic facultatives** à votre convenance.

1. Sélectionnez **Suivant : Déployer**.

   ![Sélectionnez le bouton Suivant : Déployer après avoir activé ou désactivé les données de diagnostic facultatives à votre convenance.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/select-next-deploy.png)

1. Dans l’onglet **2. Déployer**, sous **Sélectionner un appareil cible**, cliquez sur votre appareil dans la liste pour vérifier qu’il respecte la configuration minimale requise. Lorsque son état indique qu’il est pris en charge, sélectionnez **Suivant**.

   ![Sélectionnez votre appareil pour vérifier qu’il est pris en charge.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/evaluate-supported-device.png)

1. Sous l’onglet **Paramètres 2.2**, vérifiez les paramètres de configuration de votre déploiement.

   >[!NOTE]
   >IoT Edge pour Linux sur Windows utilise un commutateur par défaut qui attribue une adresse IP interne à la machine virtuelle Linux. Cette adresse IP interne ne peut pas être atteinte en dehors de l’ordinateur Windows. Vous pouvez vous connecter en local à la machine virtuelle tout en ayant une session ouverte sur l’ordinateur Windows.
   >
   >Si vous utilisez Windows Server, [configurez un commutateur par défaut](how-to-create-virtual-switch.md) avant de déployer IoT Edge pour Linux sur Windows.

   Vous pouvez affecter un GPU à votre déploiement pour permettre l’utilisation de modules Linux avec accélération GPU. Pour accéder à ces fonctionnalités, vous devez installer les composants requis détaillés dans [Accélération GPU pour Azure IoT Edge pour Linux sur Windows](gpu-acceleration.md). Si vous n’installez ces composants requis qu’à ce stade du processus de déploiement, vous devez recommencer du début.

   Deux options sont disponibles pour le relais GPU disponible : **Direct Device Assignment (DDA)** et **GPU Paravirtualization (GPU-PV)** , en fonction de l’adaptateur GPU attribué au déploiement. Vous trouverez ci-dessous des exemples de chaque méthode.

   Pour la méthode DDA, sélectionnez le nombre de processeurs GPU à allouer à votre machine virtuelle Linux.

   ![Paramètres de configuration avec un GPU DDA activé.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/gpu-passthrough-direct-device-assignment.png)

   Pour la méthode PV, aucun paramètre supplémentaire n’est nécessaire.

   ![Paramètres de configuration avec un GPU PV activé.](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >L’activation du relais d’appareils matériels peut augmenter les risques liés à la sécurité. Microsoft vous recommande de recourir à un pilote d’atténuation des appareils du fournisseur de votre GPU, le cas échéant. Pour plus d’informations, consultez [Déploiement d’appareils graphiques avec la technologie DDA (Discrete Device Assignment)](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

   Une fois que vous êtes satisfait des paramètres, sélectionnez **Suivant**.

1. Dans l’onglet **2.3. Déploiement**, vous pouvez suivre la progression du déploiement. Le processus complet comprend le téléchargement du package Azure IoT Edge pour Linux sur Windows, l’installation du package, la configuration de l’appareil hôte et la configuration de la machine virtuelle Linux. Ce processus peut prendre plusieurs minutes. Voici l’illustration d’un déploiement réussi.

   ![Dans un déploiement réussi, chaque étape comporte une coche verte et la mention « Terminé ».](./media/how-to-provision-devices-at-scale-linux-on-windows-tpm/successful-deployment.png)

1. Une fois votre déploiement terminé, vous êtes prêt à approvisionner votre appareil. Sélectionnez **Suivant : Connecter** pour passer à l’onglet **3. Connecter**, qui gère l’approvisionnement de l’appareil Azure IoT Edge.

Arrêtez de suivre l’assistant Déploiement sur la page **Approvisionnement**, car il existe des étapes à suivre pour préparer votre appareil pour l’approvisionnement avec TPM avant de pouvoir continuer. Ne refermez pas la fenêtre Windows Admin Center, car vous y reviendrez à la fin de cet article pour effectuer les étapes d’approvisionnement.

---

## <a name="enable-tpm-passthrough"></a>Activer le relais TPM

La machine virtuelle IoT Edge pour Linux sur Windows dispose d’une fonctionnalité TPM qui peut être activée ou désactivée. Par défaut, elle est désactivée. Lorsque cette fonctionnalité est activée, l’ordinateur virtuel peut accéder au module de plateforme sécurisée de l’ordinateur hôte.

1. Ouvrez une session PowerShell avec élévation de privilèges.

1. Si ce n’est encore fait, définissez la stratégie d’exécution de votre appareil sur `AllSigned` afin de pouvoir exécuter les fonctions PowerShell IoT Edge pour Linux sur Windows.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Activez la fonctionnalité TPM.

   ```powershell
   Set-EflowVmFeature -feature 'DpsTpm' -enable
   ```

## <a name="retrieve-the-tpm-information-from-your-device"></a>Récupérer les informations du module de plateforme sécurisée à partir de votre appareil

# <a name="physical-tpm"></a>[TPM physique](#tab/physical-tpm)

Pour approvisionner votre appareil, vous devez recueillir des informations de votre puce TPM et les fournir à votre instance Device Provisioning Service (DPS) afin que le service puisse reconnaître votre appareil quand il tente de se connecter.

Tout d’abord, vous devez déterminer la **Paire de clés de type EK (Endorsement Key)** , propre à chaque puce TPM et fournie par le fabricant de la puce TPM associée. Vous devez ensuite fournir un **ID d’inscription** pour votre appareil. Vous pouvez dériver un ID d’inscription unique pour votre appareil TPM, en créant par exemple un code de hachage SHA-256 pour la paire de clés de type EK.

IoT Edge pour Linux sur Windows fournit un script PowerShell pour vous aider à récupérer ces informations à partir de votre TPM. Pour utiliser le script, procédez comme suit sur votre appareil :

1. Ouvrez une session PowerShell avec élévation de privilèges.

1. Clonez le référentiel [iotedge-eflow](https://github.com/Azure/iotedge-eflow).

   ```powershell
   git clone https://github.com/Azure/iotedge-eflow.git
   ```

1. Importez le module téléchargé.

   ```powershell
   Import-Module <path>\iotedge-eflow\samples\scripts\EflowTpmProvisioningInfo.ps1
   ```

1. Exécutez la commande.

   ```powershell
   Get-EflowVmTpmProvisioningInformation
   ```

# <a name="simulated-tpm"></a>[TPM simulé](#tab/simulated-tpm)

Si vous n’avez pas de module de plateforme sécurisée (TPM) physique disponible et que vous souhaitez tester cette méthode d’approvisionnement, vous pouvez simuler un module de plateforme sécurisée sur votre appareil.

IoT Hub Device Provisioning Service fournit des exemples qui simulent un TPM et retournent pour vous la **paire de clés de type EK (Endorsement Key)** et l’**ID d’inscription**.

1. Choisissez l’un des exemples de la liste suivante, en fonction de votre langage par défaut.
1. Arrêtez de suivre les étapes de l’exemple DPS une fois que le TPM est en cours d’exécution et que vous avez collecté la **paire de clés de type EK (Endorsement Key)** et l’**ID d’inscription**. N’appuyez pas sur *Entrée* pour exécuter l’inscription dans l’exemple d’application.
1. Laissez la fenêtre hébergeant le TPM en cours d’exécution jusqu’à ce que vous ayez terminé le test de ce scénario.
1. Revenez à cet article pour créer une inscription DPS et configurer votre appareil.

Exemples de TPM simulés :

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.JS](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

---

## <a name="create-a-dps-enrollment"></a>Créer une inscription au service Device Provisioning

Utilisez les informations d’approvisionnement de votre TPM pour créer une inscription individuelle dans le service de provisionnement des appareils.

Lorsque vous créez une inscription auprès du service Device Provisioning, vous avez la possibilité de déclarer un **État initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des métriques dont vous avez besoin dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](how-to-deploy-at-scale.md).

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

Maintenant qu’une inscription existe pour cet appareil, le runtime IoT Edge peut le provisionner.

## <a name="configure-the-device-with-provisioning-information"></a>Configurer l’appareil avec des informations de provisionnement

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Ouvrez une session PowerShell avec élévation de privilèges sur l’appareil Windows.

1. Approvisionnez votre appareil à l’aide de l’**ID d’étendue** que vous avez collecté à partir de votre instance Device Provisioning Service.

   ```powershell
   Provision-EflowVM -provisioningType "DpsTpm" -scopeId "<scope id>"
   ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. À l’étape **Connecter**, approvisionnez votre appareil.

   1. Sélectionnez la méthode d’approvisionnement **DpsTpm**.
   1. Indiquez l’**ID d’étendue** que vous récupérez à partir de votre instance du service Device Provisioning.

   ![Approvisionnez votre appareil avec DPS et une attestation TPM.](./media/how-to-auto-provision-tpm-linux-on-windows/tpm-provision.png)

1. Sélectionnez **Provisionnement avec la méthode sélectionnée**.

1. Une fois que IoT Edge a été correctement installé et configuré sur votre appareil, sélectionnez **Terminer** pour quitter l’Assistant déploiement.

---

## <a name="verify-successful-configuration"></a>Vérifier que la configuration a réussi

Vérifiez que IoT Edge pour Linux sur Windows a été correctement installé et configuré sur votre appareil IoT Edge.

Si le runtime a démarré correctement, vous pouvez accéder à votre IoT Hub et commencer à déployer des modules IoT Edge sur votre appareil.

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service Device Provisioning a été utilisée. Dans le Portail Azure, accédez à l’instance du service Device Provisioning. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Utilisez les commandes suivantes sur votre appareil pour vérifier que IoT Edge a été installé et démarré correctement.

1. Connectez-vous à votre machine virtuelle IoT Edge pour Linux sur Windows en utilisant la commande suivante dans votre session PowerShell :

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

1. Sélectionnez votre appareil IoT Edge dans la liste des appareils connectés dans Windows Admin Center pour vous y connecter.

1. La page de vue d’ensemble de l’appareil affiche des informations sur l’appareil :

   * La section **Liste des modules IoT Edge** affiche les modules en cours d’exécution sur l’appareil. Lors du premier démarrage du service IoT Edge, seul le module **edgeAgent** apparaît dans cette liste. Le module edgeAgent s’exécute par défaut et vous aide à installer et démarrer tous les modules supplémentaires que vous déployez sur votre appareil.

   * La section **État IoT Edge** indique l’état du service, qui doit être **actif (en cours d’exécution)** .

---

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service Device Provisioning vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md)