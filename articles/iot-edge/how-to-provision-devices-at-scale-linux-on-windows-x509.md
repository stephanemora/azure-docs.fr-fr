---
title: Créer et provisionner des appareils IoT Edge avec des certificats X.509 sur Linux sur Windows - Azure IoT Edge | Microsoft Docs
description: Utilisez l’attestation de certificat X.509 pour tester le provisionnement d’appareils à grande échelle pour Azure IoT Edge avec le service Device Provisioning.
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 309ac9bf4810d245695ad92c316a23d4d8571ac5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700496"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-x509-certificates"></a>Créer et provisionner IoT Edge pour Linux sur des appareils Windows à grande échelle avec des certificats X.509

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Cet article fournit des instructions complètes pour le provisionnement automatique d’un ou plusieurs appareils [IoT Edge pour Linux sur Windows](iot-edge-for-linux-on-windows.md) à l’aide de certificats X.509. Vous pouvez provisionner automatiquement des appareils Azure IoT Edge à l’aide du [service Device Provisioning (DPS) Azure IoT Hub](../iot-dps/index.yml). Si vous ne connaissez pas le processus de provisionnement automatique, consultez la présentation du [provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

Voici les tâches à effectuer :

1. Génération de certificats et de clés
1. Création d’une **inscription individuelle** pour un appareil ou d’une **inscription de groupe** pour un ensemble d’appareils
1. Déploiement d’une machine virtuelle Linux avec le runtime IoT Edge installé, et connexion de cette machine à IoT Hub

Utiliser des certificats X.509 comme mécanisme d’attestation est un excellent moyen de mettre à l’échelle la production et de simplifier le provisionnement des appareils. Les certificats X.509 sont généralement organisés en une chaîne d’approbation de confiance. À partir d’un certificat racine auto-signé ou approuvé, chaque certificat de la chaîne signe le certificat inférieur suivant. Ce modèle crée une chaîne déléguée de confiance depuis le certificat racine jusqu’au certificat « feuille » final installé sur un appareil, en passant par chaque certificat intermédiaire.

## <a name="prerequisites"></a>Prérequis

* Un hub IoT actif.
* La dernière version de [Git](https://git-scm.com/download/) installée.
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
* Une instance du service IoT Hub Device Provisioning dans Azure, liée à votre hub IoT.
  * Si vous ne disposez pas d’une instance du service Device Provisioning, vous pouvez suivre les instructions fournies dans les sections [Créer un service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) et [Lier le hub IoT et votre service Device Provisioning](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) du guide de démarrage rapide du service IoT Hub Device Provisioning.
  * Après avoir lancé l’exécution du service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge.
* Si vous souhaitez utiliser des **modules Linux avec accélération GPU** dans votre déploiement Azure IoT Edge pour Linux sur Windows, il existe plusieurs options de configuration à prendre en compte. Vous devrez installer les pilotes adaptés à votre architecture GPU. Par ailleurs, vous aurez peut-être besoin d’un accès à une build du Programme Windows Insider. Pour déterminer vos besoins en matière de configuration et satisfaire à ces prérequis, consultez [Accélération GPU pour Azure IoT Edge pour Linux sur Windows](gpu-acceleration.md).

Vous pouvez utiliser **PowerShell** ou **Windows Admin Center** pour installer et gérer vos appareils IoT Edge. Chaque utilitaire a sa propre configuration requise :

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Si vous souhaitez utiliser PowerShell, suivez les étapes ci-dessous pour télécharger et installer Azure IoT Edge pour Linux sur Windows :

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

   Vous pouvez spécifier des répertoires d’installation et VHDX personnalisés en ajoutant les paramètres `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` and `VHDXDIR="<FULLY_QUALIFIED_PATH>"`à la commande d’installation.

1. Définissez la stratégie d’exécution sur l’appareil cible sur `AllSigned`. Vous pouvez vérifier la stratégie d’exécution actuelle dans une invite PowerShell avec élévation de privilèges à l’aide de :

   ```powershell
   Get-ExecutionPolicy -List
   ```

   Si la stratégie d’exécution de `local machine` n’est pas `AllSigned`, vous pouvez la définir à l’aide de :

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Pour en savoir plus sur le module PowerShell d’Azure IoT Edge pour Linux sur Windows, consultez la [documentation de référence sur les fonctions PowerShell](reference-iot-edge-for-linux-on-windows-functions.md).

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Si vous souhaitez utiliser Windows Admin Center, vérifiez que vous y avez accès et que l’extension Azure IoT Edge est installée :

   1. Téléchargez et exécutez le [programme d’installation de Windows Admin Center](https://aka.ms/wacdownload). Suivez les invites de l’Assistant Installation pour installer Windows Admin Center.

   1. Une fois l’installation terminée, utilisez un navigateur pris en charge pour ouvrir le Windows Admin Center. Les navigateurs pris en charge sont Microsoft Edge (Windows 10, version 1709 ou ultérieure), Google Chrome et Microsoft Edge Insider.

   1. Lors de la première utilisation de Windows Admin Center, vous êtes invité à sélectionner le certificat à utiliser. Sélectionnez **Client Windows Admin Center** comme certificat.

   1. Installez l’extension Azure IoT Edge. Sélectionnez l’icône d’engrenage en haut à droite du tableau de bord de Windows Admin Center.

      ![Sélectionnez l’icône d’engrenage en haut à droite du tableau de bord pour accéder aux paramètres.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. Dans le menu **Paramètres**, sous **Passerelle**, sélectionnez **Extensions**.

   1. Dans l’onglet **Extensions disponibles**, recherchez **Azure IoT Edge** dans la liste des extensions. Sélectionnez-le et cliquez sur l’invite **Installer** au-dessus de la liste des extensions.

   1. Une fois l’installation terminée, Azure IoT Edge doit apparaître dans la liste des extensions installées dans l’onglet **Extensions installées**.

---

## <a name="generate-device-identity-certificates"></a>Générer des certificats d’identité d’appareil

Le certificat d’identité d’appareil est un certificat feuille qui se connecte par le biais d’une chaîne de certificats de confiance au certificat d’autorité de certification X.509 supérieur. Le nom commun (CN) du certificat d’identité d’appareil doit être défini sur l’ID d’appareil que vous souhaitez que l’appareil ait dans votre hub IoT.

Les certificats d’identité d’appareil sont utilisés uniquement pour provisionner l’appareil IoT Edge et l’authentifier auprès d’Azure IoT Hub. Ils ne signent pas de certificats, contrairement aux certificats d’autorité de certification que l’appareil IoT Edge présente aux modules ou aux appareils de nœud terminal à des fins de vérification. Pour plus d’informations, consultez les [détails sur l’utilisation des certificats par Azure IoT Edge](iot-edge-certs.md).

Après avoir créé le certificat d’identité d’appareil, vous devez avoir deux fichiers : un fichier .cer ou .pem, qui contient la partie publique du certificat, et un fichier .cer ou .pem, qui comporte la clé privée du certificat. Si vous envisagez d’utiliser l’inscription de groupe dans le service Device Provisioning, vous avez également besoin de la partie publique d’un certificat d’autorité de certification racine ou intermédiaire dans la même chaîne de certificats de confiance.

Les fichiers suivants sont nécessaires pour configurer le provisionnement automatique avec X.509 :

* Le certificat d’identité d’appareil et son certificat de clé privée. Le certificat d’identité d’appareil est chargé dans DPS si vous créez une inscription individuelle. La clé privée est transmise au runtime IoT Edge.
* Un certificat de chaîne complète, qui doit comporter au moins l’identité d’appareil et les certificats intermédiaires qu’elle contient. Le certificat de chaîne complète est transmis au runtime IoT Edge.
* Un certificat d’autorité de certification racine ou intermédiaire issu de la chaîne d’approbation des certificats. Ce certificat est chargé dans DPS si vous créez une inscription de groupe.

> [!NOTE]
> Il existe actuellement dans libiothsm une limitation empêchant l’utilisation de certificats qui expirent le 1er janvier 2038 ou après cette date.

### <a name="use-test-certificates-optional"></a>Utiliser des certificats de test (facultatif)

Si vous n’avez pas d’autorité de certification disponible pour créer des certificats d’identité et que vous souhaitez tester ce scénario, le dépôt Git Azure IoT Edge contient des scripts que vous pouvez utiliser pour générer des certificats de test. Ces certificats, conçus uniquement pour les tests de développement, ne doivent pas être utilisés en production.

Pour créer des certificats de test, suivez les étapes décrites dans [Créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](how-to-create-test-certificates.md). Effectuez les deux sections nécessaires pour configurer les scripts de génération de certificat et créer un certificat d’autorité de certification racine. Ensuite, suivez les étapes permettant de créer un certificat d’identité d’appareil. Quand vous avez terminé, vous devez disposer de la chaîne de certificats et de la paire de clés suivantes :

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Vous avez besoin de ces deux certificats sur l’appareil IoT Edge. Si vous envisagez d’utiliser l’inscription individuelle dans le service Device Provisioning, vous devez charger le fichier .cert.pem. Si vous envisagez d’utiliser l’inscription de groupe dans le service Device Provisioning, la même chaîne de certificats de confiance doit également comporter un certificat d’autorité de certification racine ou intermédiaire à charger. Si vous recourez à des certificats de démonstration, utilisez le certificat `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` pour l’inscription de groupe.

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

      `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`

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

Les inscriptions de groupe utilisent un certificat d’autorité de certification intermédiaire ou racine à partir de la chaîne de certificats de confiance utilisée pour générer les certificats d’identité d’appareil individuels.

#### <a name="verify-your-root-certificate"></a>Vérifier votre certificat racine

Quand vous créez un groupe d’inscriptions, vous avez la possibilité d’utiliser un certificat vérifié. Vous pouvez vérifier un certificat avec le service Device Provisioning en prouvant que vous êtes propriétaire du certificat racine. Pour plus d’informations, consultez [Guide pratique pour effectuer la preuve de possession de certificats d’autorité de certification X.509](../iot-dps/how-to-verify-certificates.md).

1. Dans le [Portail Microsoft Azure](https://portal.azure.com), accédez à votre instance du service IoT Hub Device Provisioning.

1. Sélectionnez **Certificats** dans le menu de gauche.

1. Sélectionnez **Ajouter** pour ajouter un nouveau certificat.

1. Entrez un nom convivial pour votre certificat, puis accédez au fichier .cer ou .pem représentant la partie publique de votre certificat X.509.

   Si vous utilisez les certificats de démonstration, chargez le certificat `<wrkdir>\certs\azure-iot-test-only.root.ca.cert.pem`.

1. Sélectionnez **Enregistrer**.

1. Votre certificat doit maintenant être listé dans la page **Certificats**. Sélectionnez-le pour ouvrir les détails du certificat.

1. Sélectionnez **Générer le code de vérification**, puis copiez le code généré.

1. Que vous ayez fourni votre propre certificat d’autorité de certification ou utilisiez les certificats de démonstration, vous pouvez utiliser l’outil de vérification disponible dans le dépôt IoT Edge pour vérifier la preuve de possession. L’outil de vérification utilise votre certificat d’autorité de certification pour signer un nouveau certificat ayant le code de vérification fourni comme nom d’objet.

   ```powershell
   New-CACertsVerificationCert "<verification code>"
   ```

1. Dans le portail Azure, dans la même page des détails du certificat, chargez le certificat de vérification qui vient d’être généré.

1. Sélectionnez **Vérifier**.

#### <a name="create-enrollment-group"></a>Créer un groupe d’inscriptions

Pour plus d’informations sur les inscriptions dans le service Device Provisioning, consultez [Guide pratique pour gérer les inscriptions d’appareils](../iot-dps/how-to-manage-enrollments.md).

> [!TIP]
> Bien que les étapes décrites dans cet article concernent le portail Azure, vous pouvez également créer des inscriptions de groupe à l’aide d’Azure CLI. Pour plus d’informations, consultez la section relative à [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group). Dans la commande CLI, utilisez l’indicateur **edge-enabled** pour spécifier que l’inscription concerne des appareils IoT Edge. Pour une inscription de groupe, tous les appareils doivent être de IoT Edge, ou aucun d’entre eux ne peut l’être.

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

Vous êtes prêt à passer à la section suivante.

## <a name="create-a-new-iot-edge-for-linux-on-windows-deployment"></a>Créer un déploiement d’IoT Edge pour Linux sur Windows

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

1. Définissez la stratégie d’exécution sur l’appareil cible sur `AllSigned` si ce n’est pas déjà fait. Consultez la configuration requise des commandes PowerShell pour vérifier la stratégie d’exécution actuelle et définir la stratégie d’exécution sur `AllSigned`.

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

   ![Un déploiement réussi indique « Déploiement réussi » à la fin des messages.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/successful-powershell-deployment.png)

Une fois votre déploiement terminé, vous êtes prêt à approvisionner votre appareil.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>L’extension Azure IoT Edge pour Windows Admin Center est actuellement en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Les processus d’installation et de gestion peuvent différer de ceux des fonctionnalités généralement disponibles.

Sur la page d’accueil de Windows Admin Center, sous la liste des connexions figure une connexion d’hôte local qui représente le PC sur lequel Windows Admin Center est exécuté. Les autres serveurs, ordinateurs ou clusters que vous gérez s’affichent également ici.

Vous pouvez utiliser Windows Admin Center pour installer et gérer Azure IoT Edge pour Linux sur Windows sur votre appareil local ou des appareils gérés distants. Dans ce guide, la connexion de l’hôte local servira d’appareil cible pour le déploiement d’Azure IoT Edge pour Linux sur Windows.

Si vous préférez effectuer un déploiement sur un appareil cible distant plutôt que sur votre appareil local, et que l’appareil cible souhaité ne figure pas dans la liste, suivez les [instructions pour ajouter votre appareil.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

   ![Tableau de bord initial Windows Admin Center avec l’appareil cible figurant dans la liste.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/windows-admin-center-initial-dashboard.png)

1. Sélectionnez **Ajouter**.

1. Dans le volet **Ajouter ou créer des ressources**, localisez la vignette **Azure IoT Edge**. Sélectionnez **Créer** pour installer une nouvelle instance d’Azure IoT Edge pour Linux sur un appareil.

   Si IoT Edge pour Linux sur Windows est déjà exécuté sur un appareil, vous pouvez sélectionner **Ajouter** pour vous connecter à cet appareil IoT Edge existant et le gérer avec Windows Admin Center.

   ![Sélectionnez Créer sur la vignette Azure IoT Edge dans Windows Admin Center.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/resource-creation-tiles.png)

1. Le volet **Créer un déploiement d’Azure IoT Edge pour Linux sur Windows** s’ouvre. Sur l’onglet **1. Prise en main**, vérifiez la configuration minimale requise, puis sélectionnez **Suivant**.

1. Passez en revue les termes du contrat de licence, cochez la case **J’accepte** et sélectionnez **Suivant**.

1. Vous pouvez activer ou désactiver les **données de diagnostic facultatives** à votre convenance.

1. Sélectionnez **Suivant : Déployer**.

   ![Sélectionnez le bouton Suivant : Déployer après avoir activé ou désactivé les données de diagnostic facultatives à votre convenance.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-next-deploy.png)

1. Dans l’onglet **2. Déployer**, sous **Sélectionner un appareil cible**, cliquez sur votre appareil dans la liste pour vérifier qu’il respecte la configuration minimale requise. Lorsque son état indique qu’il est pris en charge, sélectionnez **Suivant**.

   ![Sélectionnez votre appareil pour vérifier qu’il est pris en charge.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/evaluate-supported-device.png)

1. Sous l’onglet **Paramètres 2.2**, vérifiez les paramètres de configuration de votre déploiement.

   >[!NOTE]
   >IoT Edge pour Linux sur Windows utilise un commutateur par défaut qui attribue une adresse IP interne à la machine virtuelle Linux. Cette adresse IP interne ne peut pas être atteinte en dehors de l’ordinateur Windows. Vous pouvez vous connecter en local à la machine virtuelle tout en ayant une session ouverte sur l’ordinateur Windows.
   >
   >Si vous utilisez Windows Server, configurez un commutateur par défaut avant de déployer IoT Edge pour Linux sur Windows.

   Vous pouvez affecter un GPU à votre déploiement pour permettre l’utilisation de modules Linux avec accélération GPU. Pour accéder à ces fonctionnalités, vous devez installer les composants requis détaillés dans [Accélération GPU pour Azure IoT Edge pour Linux sur Windows](gpu-acceleration.md). Si vous n’installez ces composants requis qu’à ce stade du processus de déploiement, vous devez recommencer du début.

   Deux options sont disponibles pour le relais GPU disponible : **Direct Device Assignment (DDA)** et **GPU Paravirtualization (GPU-PV)** , en fonction de l’adaptateur GPU attribué au déploiement. Vous trouverez ci-dessous des exemples de chaque méthode.

   Pour la méthode DDA, sélectionnez le nombre de processeurs GPU à allouer à votre machine virtuelle Linux.

   ![Paramètres de configuration avec un GPU DDA activé.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/gpu-passthrough-direct-device-assignment.png)

   Pour la méthode PV, aucun paramètre supplémentaire n’est nécessaire.

   ![Paramètres de configuration avec un GPU PV activé.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >L’activation du relais d’appareils matériels peut augmenter les risques liés à la sécurité. Microsoft vous recommande de recourir à un pilote d’atténuation des appareils du fournisseur de votre GPU, le cas échéant. Pour plus d’informations, consultez [Déploiement d’appareils graphiques avec la technologie DDA (Discrete Device Assignment)](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

   Une fois que vous êtes satisfait des paramètres, sélectionnez **Suivant**.

1. Dans l’onglet **2.3. Déploiement**, vous pouvez suivre la progression du déploiement. Le processus complet comprend le téléchargement du package Azure IoT Edge pour Linux sur Windows, l’installation du package, la configuration de l’appareil hôte et la configuration de la machine virtuelle Linux. Ce processus peut prendre plusieurs minutes. Voici l’illustration d’un déploiement réussi.

   ![Dans un déploiement réussi, chaque étape comporte une coche verte et la mention « Terminé ».](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/successful-deployment.png)

Une fois votre déploiement terminé, vous êtes prêt à approvisionner votre appareil. Sélectionnez **Suivant : Connecter** pour passer à l’onglet **3. Connecter**, qui gère l’approvisionnement de l’appareil Azure IoT Edge.

---

## <a name="configure-the-device-with-provisioning-information"></a>Configurer l’appareil avec des informations de provisionnement

Une fois que le runtime est installé sur votre appareil, configurez l’appareil avec les informations qu’il utilise pour se connecter au service Device Provisioning (DPS) et à IoT Hub.

Préparez les informations suivantes :

* La valeur **Étendue de l’ID** du service Device Provisioning. Vous pouvez récupérer cette valeur à partir de la page de présentation de votre instance du service Device Provisioning dans le portail Azure.
* Le fichier de chaîne de certificats d’identité d’appareil sur l’appareil
* Le fichier de clé d’identité d’appareil sur l’appareil

Vous pouvez utiliser PowerShell ou Windows Admin Center pour provisionner votre appareil IoT Edge.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour PowerShell, exécutez la commande suivante en mettant à jour les valeurs d’espace réservé avec vos propres valeurs :

```powershell
Provision-EflowVm -provisioningType DpsX509 -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -identityCertPath PASTE_ABSOLUTE_PATH_TO_IDENTITY_CERTIFICATE_HERE -identityPrivateKey PASTE_ABSOLUTE_PATH_TO_IDENTITY_PRIVATE_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Pour Windows Admin Center, procédez comme suit :

1. Dans le volet **Provisionnement de l’appareil Azure IoT Edge**, sélectionnez **Certificat X.509 (DPS)** dans le menu déroulant des méthodes de provisionnement.

1. Indiquez votre ID d’étendue DPS, l’ID d’inscription de l’appareil et la clé primaire ou la clé dérivée de l’inscription dans les champs Windows Admin Center.

1. Sélectionnez **Approvisionnement avec la méthode sélectionnée**.

   ![Sélectionnez Provisionnement avec la méthode sélectionnée après avoir renseigné les champs requis pour le provisionnement avec clé symétrique.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/provisioning-with-selected-method-x509-certs.png)

1. Une fois l’approvisionnement terminé, sélectionnez **Terminer**. Vous êtes redirigé vers le tableau de bord principal. Un nouvel appareil, dont le type est `IoT Edge Devices`, doit maintenant apparaître dans la liste. Vous pouvez sélectionner l’appareil IoT Edge pour vous y connecter. Une fois sur la page de **vue d’ensemble**, vous pouvez consulter la **liste des modules IoT Edge** et l’**état IoT Edge** de votre appareil.

---

## <a name="verify-successful-configuration"></a>Vérifier que la configuration a réussi

Vérifiez que IoT Edge pour Linux sur Windows a été correctement installé et configuré sur votre appareil IoT Edge.

# <a name="individual-enrollment"></a>[Inscription individuelle](#tab/individual-enrollment)

Vous pouvez vérifier que l’inscription individuelle que vous avez créée dans le service Device Provisioning a été utilisée. Dans le Portail Azure, accédez à l’instance du service Device Provisioning. Ouvrez les détails de l’inscription pour l’inscription individuelle que vous avez créée. Notez que l’état de l’inscription est **Affecté** et que l’ID de l’appareil figure dans la liste.

# <a name="group-enrollment"></a>[Inscription de groupe](#tab/group-enrollment)

Vous pouvez vérifier que l’inscription de groupe que vous avez créée dans le service Device Provisioning a été utilisée. Dans le Portail Azure, accédez à l’instance du service Device Provisioning. Ouvrez les détails de l’inscription de groupe que vous avez créée. Accédez à l’onglet **Enregistrements d’inscription** pour afficher tous les appareils enregistrés dans ce groupe.

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

Lorsque vous créez un appareil IoT Edge, il affichera le code d’état `417 -- The device's deployment configuration is not set` dans le portail Azure. Cet état est normal et signifie que l’appareil est prêt à recevoir un déploiement de module.

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service Device Provisioning vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-at-scale.md) ou [d’Azure CLI](how-to-deploy-cli-at-scale.md).

Vous pouvez également :

* Passez à [Déployer des modules IoT Edge](how-to-deploy-modules-portal.md) pour savoir comment déployer des modules sur votre appareil.
* Découvrez comment [gérer les certificats sur votre IoT Edge pour Linux sur une machine virtuelle Windows](how-to-manage-device-certificates.md) et à transférer des fichiers du système d’exploitation hôte vers votre machine virtuelle Linux.
* Découvrez comment [configurer vos appareils IoT Edge pour qu’ils communiquent par le biais d’un serveur proxy](how-to-configure-proxy-support.md).
