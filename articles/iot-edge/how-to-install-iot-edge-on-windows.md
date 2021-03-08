---
title: Installer Azure IoT Edge pour Linux sur Windows | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur des appareils Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: c12dbeb81fd2b3d67863b5b84fa30cf77e165549
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123188"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Installer et approvisionner Azure IoT Edge pour Linux sur un appareil Windows (version préliminaire)

Le runtime Azure IoT Edge est ce qui transforme un appareil en appareil IoT Edge. Le runtime peut être déployé sur des appareils allant des simples ordinateurs aux serveurs industriels. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud. Pour en savoir plus, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Azure IoT Edge pour Linux sur Windows vous permet d’utiliser Azure IoT Edge sur des appareils Windows à l’aide de machines virtuelles Linux. La version Linux d’Azure IoT Edge et tout module Linux déployé en même temps sont exécutés sur la machine virtuelle. À partir de là, les applications Windows, le code, le runtime IoT Edge et les modules peuvent interagir librement.

Cet article répertorie les étapes à suivre pour configurer IoT Edge sur un appareil Windows. Ces étapes déploient une machine virtuelle Linux contenant le runtime IoT Edge à exécuter sur votre appareil Windows, puis approvisionnent l’appareil avec son identité d’appareil IoT Hub.

>[!NOTE]
>IoT Edge pour Linux sur Windows est en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>Si IoT Edge pour Linux sur Windows est l’expérience recommandée pour l’utilisation d’Azure IoT Edge dans un environnement Windows, les conteneurs Windows sont toujours disponibles. Si vous préférez utiliser des conteneurs Windows, consultez le guide pratique concernant [l’installation et la gestion d’Azure IoT Edge pour Windows](how-to-install-iot-edge-windows-on-windows.md).

## <a name="prerequisites"></a>Prérequis

* Un compte Azure avec un abonnement valide. Si vous n’avez pas [d’abonnement Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) de niveau gratuit ou standard dans Azure

* Un appareil Windows avec la configuration requise minimum suivante :

  * Windows 10 version 1809 ou ultérieure ; build 17763 ou ultérieure
  * Éditions Professionnel, Entreprise ou Serveur
  * RAM minimale : 4 Go (8 Go recommandés)
  * Stockage minimal : 10 Go

* Accès à la version Insider du Windows Admin Center avec l’extension Azure IoT Edge pour Windows Admin Center installée :  <!-- The link below needs the language localization to work; otherwise broken -->
   1. Découvrez [Windows Insider Preview](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewserver).

   1. Dans la liste déroulante des versions préliminaires, sélectionnez **Windows Admin Center Preview - Build 2012**, puis **Confirmer**.

      ![Sélectionnez Windows Admin Center Preview - Build 2012 dans le menu déroulant des versions préliminaires disponibles.](./media/how-to-install-iot-edge-on-windows/select-windows-admin-center-preview-build.png)

   1. Dans la liste déroulante **Sélectionner la langue**, sélectionnez **Anglais**, puis **Confirmer**.

   1. Sélectionnez **Télécharger maintenant** pour télécharger le fichier *WindowsAdminCenterPreview2012.msi*.

   1. Exécutez le fichier *WindowsAdminCenterPreview2012.msi* et suivez les invites de l’assistant d’installation pour installer Windows Admin Center. Une fois installé, ouvrez Windows Admin Center.

   1. Lors de la première utilisation de Windows Admin Center, vous êtes invité à sélectionner le certificat à utiliser. Sélectionnez **Client Windows Admin Center** comme certificat.

   1. Il est temps d’installer l’extension Azure IoT Edge. Sélectionnez l’icône d’engrenage en haut à droite du tableau de bord de Windows Admin Center.

      ![Sélectionnez l’icône d’engrenage en haut à droite du tableau de bord pour accéder aux paramètres.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. Dans le menu **Paramètres**, sous **Passerelle**, sélectionnez **Extensions**.

   1. Accédez à l’onglet **Flux** et sélectionnez **Ajouter**.

   1. Entrez https://aka.ms/wac-insiders-feed dans la zone de texte et sélectionnez **Ajouter**.

   1. Une fois le flux ajouté, accédez à l’onglet **Extensions disponibles**. La mise à jour de la liste des extensions peut prendre un certain temps.

   1. Dans l’onglet **Extensions disponibles**, recherchez **Azure IoT Edge** dans la liste des extensions. Sélectionnez-le et cliquez sur l’invite **Installer** au-dessus de la liste des extensions.

   1. Une fois l’installation terminée, Azure IoT Edge doit apparaître dans la liste des extensions installées dans l’onglet **Extensions installées**.

## <a name="choose-your-provisioning-method"></a>Choisir votre méthode d’approvisionnement

Azure IoT Edge pour Linux sur Windows prend en charge les méthodes d’approvisionnement suivantes :

* Approvisionnement manuel à l’aide de la chaîne de connexion de votre appareil IoT Edge. Pour utiliser cette méthode, inscrivez votre appareil et récupérez une chaîne de connexion en suivant les étapes de la rubrique [Inscrire un appareil IoT Edge dans IoT Hub](how-to-register-device.md).
  * Choisissez l’option d’authentification par clé symétrique, les certificats auto-signés X.509 n’étant pas pris en charge par IoT Edge pour Linux sur Windows.
* Approvisionnement automatique à l’aide du service Device Provisioning (DPS) et des clés symétriques. En savoir plus sur la [création et l’approvisionnement d’un appareil IoT Edge avec DPS et les clés symétriques](how-to-auto-provision-symmetric-keys.md).
* Approvisionnement automatique à l’aide du DPS et des certificats X.509. En savoir plus sur la [création et l’approvisionnement d’un appareil IoT Edge avec DPS et les certificats X.509](how-to-auto-provision-x509-certs.md).

L’approvisionnement manuel est plus facile à mettre en place pour un petit nombre d’appareils. Le service Device Provisioning est utile pour approvisionner de nombreux appareils.

Si vous envisagez d’utiliser l’une des méthodes DPS pour approvisionner vos appareils, suivez les étapes décrites dans l’article correspondant mentionné ci-dessus pour créer une instance du service Device Provisioning, lier votre instance DPS à votre IoT Hub et créer une inscription au service Device Provisioning. Vous pouvez créer une *inscription individuelle* pour un seul appareil ou une *inscription de groupe* pour un groupe d’appareils. Pour plus d’informations sur les types d’inscription, consultez l’article [Concepts du service Azure IoT Hub Device Provisioning](../iot-dps/concepts-service.md#enrollment).

## <a name="create-a-new-deployment"></a>Créer un déploiement

Créez votre déploiement d’Azure IoT Edge pour Linux sur Windows sur votre appareil cible.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Sur la page d’accueil de Windows Admin Center, sous la liste des connexions figure une connexion d’hôte local qui représente l’ordinateur sur lequel vous exécutez Windows Admin Center. Les autres serveurs, ordinateurs ou clusters que vous gérez s’affichent également ici.

Vous pouvez utiliser Windows Admin Center pour installer et gérer Azure IoT Edge pour Linux sur Windows sur votre appareil local ou des appareils gérés distants. Dans ce guide, la connexion de l’hôte local servira d’appareil cible pour le déploiement d’Azure IoT Edge pour Linux sur Windows.

Si vous préférez effectuer un déploiement sur un appareil cible distant plutôt que sur votre appareil local, et que l’appareil cible souhaité ne figure pas dans la liste, suivez les [instructions pour ajouter votre appareil.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

   ![Tableau de bord initial Windows Admin Center avec l’appareil cible figurant dans la liste](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Sélectionnez **Ajouter**.

1. Dans le volet **Ajouter ou créer des ressources**, localisez la vignette **Azure IoT Edge**. Sélectionnez **Créer** pour installer une nouvelle instance d’Azure IoT Edge pour Linux sur un appareil.

   Si IoT Edge pour Linux sur Windows est déjà exécuté sur un appareil, vous pouvez sélectionner **Ajouter** pour vous connecter à cet appareil IoT Edge existant et le gérer avec Windows Admin Center.

   ![Sélectionnez Créer sur la vignette Azure IoT Edge dans Windows Admin Center](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Le volet **Créer un déploiement d’Azure IoT Edge pour Linux sur Windows** s’ouvre. Dans l’onglet **1. Prise en main**, vérifiez que votre appareil cible respecte la configuration minimale requise, puis sélectionnez **Suivant**.

1. Passez en revue les termes du contrat de licence, cochez la case **J’accepte** et sélectionnez **Suivant**.

1. Vous pouvez activer ou désactiver les **données de diagnostic facultatives** à votre convenance.

1. Sélectionnez **Suivant : Déployer**.

   ![Sélectionnez le bouton Suivant : Déployer après avoir activé ou désactivé les données de diagnostic facultatives à votre convenance](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. Dans l’onglet **2. Déployer**, sous **Sélectionner un appareil cible**, cliquez sur votre appareil dans la liste pour vérifier qu’il respecte la configuration minimale requise. Lorsque son état indique qu’il est pris en charge, sélectionnez **Suivant**.

   ![Sélectionnez votre appareil pour vérifier qu’il est pris en charge](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Acceptez les paramètres par défaut dans l’onglet **2.2. Paramètres**.

1. Dans l’onglet **2.3. Déploiement**, vous pouvez suivre la progression du déploiement. Le processus complet comprend le téléchargement du package Azure IoT Edge pour Linux sur Windows, l’installation du package, la configuration de l’appareil hôte et la configuration de la machine virtuelle Linux. Ce processus peut prendre plusieurs minutes. Voici l’illustration d’un déploiement réussi.

   ![Dans un déploiement réussi, chaque étape comporte une coche verte et la mention « Terminé »](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Une fois votre déploiement terminé, vous êtes prêt à approvisionner votre appareil. Sélectionnez **Suivant : Connecter** pour passer à l’onglet **3. Connecter**, qui gère l’approvisionnement de l’appareil Azure IoT Edge.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Si vous ne l’avez pas déjà fait, installez IoT Edge pour Linux sur Windows sur votre appareil cible.

> [!NOTE]
> Le processus PowerShell suivant décrit comment créer un déploiement d’hôte local Azure IoT Edge pour Linux sur Windows. Pour créer un déploiement sur un appareil cible distant à l’aide de PowerShell, vous pouvez utiliser [PowerShell distant](/powershell/module/microsoft.powershell.core/about/about_remote) pour établir une connexion à un appareil distant et exécuter ces commandes à distance sur cet appareil.

1. Dans une session PowerShell avec élévation de privilèges, exécutez chacune des commandes suivantes pour télécharger IoT Edge pour Linux sur Windows.

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzureEdgeForLinuxOnWindowsMSI" -OutFile $msiPath
   ```

1. Installez IoT Edge pour Linux sur Windows sur votre appareil.

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > Vous pouvez spécifier des répertoires VHDX et d’installation IoT Edge pour Linux sur Windows personnalisés en ajoutant les paramètres INSTALLDIR="<FULLY_QUALIFIED_PATH>" et VHDXDIR="<FULLY_QUALIFIED_PATH>" à la commande d’installation ci-dessus.

1. Pour que le déploiement s’exécute correctement, vous devez définir la stratégie d’exécution sur l’appareil cible sur `AllSigned` si ce n’est pas déjà fait. Vous pouvez vérifier la stratégie d’exécution actuelle dans une invite PowerShell avec élévation de privilèges à l’aide de :

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Si la stratégie d’exécution de `local machine` n’est pas `AllSigned`, vous pouvez la définir à l’aide de :

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Créer le déploiement d’IoT Edge pour Linux sur Windows.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   > [!NOTE]
   > Vous pouvez exécuter cette commande sans paramètres ou éventuellement personnaliser le déploiement avec des paramètres. Pour voir ce qu’ils signifient, consultez les [informations de référence sur le script PowerShell IoT Edge pour Linux sur Windows](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow).

1. Entrez « Y » pour accepter les termes du contrat de licence.

1. Entrez « O » ou « R » pour activer ou désactiver les **données de diagnostic facultatives** à votre convenance. Voici l’illustration d’un déploiement réussi.

   ![Un déploiement réussi indique « Déploiement réussi » à la fin des messages](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

Une fois votre déploiement terminé, vous êtes prêt à approvisionner votre appareil.

---

Pour approvisionner votre appareil, vous pouvez suivre les liens ci-dessous pour accéder à la section correspondant à la méthode d’approvisionnement sélectionnée :

* [Option n°1 : Approvisionnement manuel à l’aide de la chaîne de connexion de votre appareil IoT Edge](#option-1-provisioning-manually-using-the-connection-string)
* [Option n°2 : Approvisionnement automatique à l’aide du service Device Provisioning (DPS) et des clés symétriques](#option-2-provisioning-via-dps-using-symmetric-keys)
* [Option 3 : Approvisionnement automatique à l’aide du service DPS et des certificats X.509](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>Approvisionner votre appareil

Choisissez une méthode pour approvisionner votre appareil et suivez les instructions de la section appropriée. Vous pouvez utiliser Windows Admin Center ou une session PowerShell avec élévation de privilèges pour approvisionner vos appareils.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>Option 1 : Approvisionnement manuel à l’aide de la chaîne de connexion

Cette section traite de la configuration manuelle de votre appareil à l’aide de la chaîne de connexion de votre appareil Azure IoT Edge.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Dans le volet **Approvisionnement de l’appareil Azure IoT Edge**, sélectionnez **Chaîne de connexion (manuel)** dans le menu déroulant des méthodes d’approvisionnement.

1. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à l’onglet **IoT Edge** de votre IoT Hub.

1. Cliquez sur l’ID d’appareil de votre appareil. Copiez le champ **Chaîne de connexion principale**.

1. Collez-le dans le champ Chaîne de connexion de l’appareil dans Windows Admin Center. Sélectionnez ensuite **Approvisionnement avec la méthode sélectionnée**.

   ![Choisissez Approvisionnement avec la méthode sélectionnée après avoir collé la chaîne de connexion de votre appareil](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. Une fois l’approvisionnement terminé, sélectionnez **Terminer**. Vous êtes redirigé vers le tableau de bord principal. Un nouvel appareil, dont le type est `IoT Edge Devices`, doit maintenant apparaître dans la liste. Vous pouvez sélectionner l’appareil IoT Edge pour vous y connecter. Une fois sur la page de **vue d’ensemble**, vous pouvez consulter la **liste des modules IoT Edge** et l’**état IoT Edge** de votre appareil.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à l’onglet **IoT Edge** de votre IoT Hub.

1. Cliquez sur l’ID d’appareil de votre appareil. Copiez le champ **Chaîne de connexion principale**.

1. Collez sur le texte d’espace réservé dans la commande suivante et exécutez celle-ci dans une session PowerShell avec élévation des privilèges sur votre appareil cible.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>Option n°2 : Approvisionnement via DPS à l’aide de clés symétriques

Cette section traite de l’approvisionnement automatique de votre appareil à l’aide de DPS et de clés symétriques.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Dans le volet **Approvisionnement de l’appareil Azure IoT Edge**, sélectionnez **Clés symétriques (DPS)** dans le menu déroulant des méthodes d’approvisionnement.

1. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à votre instance DPS.

1. Dans l’onglet **Vue d’ensemble**, copiez la valeur du paramètre **Étendue de l’ID**. Collez-la dans le champ ID de l’étendue dans Windows Admin Center.

1. Dans l’onglet **Gérer les inscriptions** du portail Azure, sélectionnez l’inscription que vous avez créée. Copiez la valeur du paramètre **Clé primaire** dans les détails de l’inscription. Collez-la dans le champ Clé symétrique dans Windows Admin Center.

1. Indiquez l’ID d’inscription de votre appareil dans le champ ID d’inscription dans Windows Admin Center.

1. Sélectionnez **Approvisionnement avec la méthode sélectionnée**.

   ![Sélectionnez Approvisionnement avec la méthode sélectionnée après avoir renseigné les champs requis pour l’approvisionnement avec clé symétrique](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. Une fois l’approvisionnement terminé, sélectionnez **Terminer**. Vous êtes redirigé vers le tableau de bord principal. Un nouvel appareil, dont le type est `IoT Edge Devices`, doit maintenant apparaître dans la liste. Vous pouvez sélectionner l’appareil IoT Edge pour vous y connecter. Une fois sur la page de **vue d’ensemble**, vous pouvez consulter la **liste des modules IoT Edge** et l’**état IoT Edge** de votre appareil.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Copiez la commande ci-après dans un éditeur de texte. Remplacez le texte d’espace réservé par vos informations en suivant les indications.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à votre instance DPS.

1. Dans l’onglet **Vue d’ensemble**, copiez la valeur du paramètre **Étendue de l’ID**. Collez-la sur le texte d’espace réservé approprié dans la commande.

1. Dans l’onglet **Gérer les inscriptions** du portail Azure, sélectionnez l’inscription que vous avez créée. Copiez la valeur du paramètre **Clé primaire** dans les détails de l’inscription. Collez-la sur le texte d’espace réservé approprié dans la commande.

1. Fournissez l’ID d’inscription de l’appareil pour remplacer le texte d’espace réservé approprié dans la commande.

1. Exécutez la commande dans une session PowerShell avec élévation de privilèges sur l’appareil cible.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>Option 3 : Approvisionnement via DPS à l’aide de certificats X.509

Cette section traite de l’approvisionnement automatique de votre appareil à l’aide de DPS et de certificats X.509.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Dans le volet **Approvisionnement de l’appareil Azure IoT Edge**, sélectionnez **Certificat X.509 (DPS)** dans le menu déroulant des méthodes d’approvisionnement.

1. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à votre instance DPS.

1. Dans l’onglet **Vue d’ensemble**, copiez la valeur du paramètre **Étendue de l’ID**. Collez-la dans le champ ID de l’étendue dans Windows Admin Center.

1. Indiquez l’ID d’inscription de votre appareil dans le champ ID d’inscription dans Windows Admin Center.

1. Chargez vos fichiers de certificat et de clé privée.

1. Sélectionnez **Approvisionnement avec la méthode sélectionnée**.

   ![Sélectionnez Approvisionnement avec la méthode sélectionnée après avoir renseigné les champs requis pour l’approvisionnement avec certificat X.509](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. Une fois l’approvisionnement terminé, sélectionnez **Terminer**. Vous êtes redirigé vers le tableau de bord principal. Un nouvel appareil, dont le type est `IoT Edge Devices`, doit maintenant apparaître dans la liste. Vous pouvez sélectionner l’appareil IoT Edge pour vous y connecter. Une fois sur la page de **vue d’ensemble**, vous pouvez consulter la **liste des modules IoT Edge** et l’**état IoT Edge** de votre appareil.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Copiez la commande ci-après dans un éditeur de texte. Remplacez le texte d’espace réservé par vos informations en suivant les indications.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. Dans le [portail Azure](https://ms.portal.azure.com/), accédez à votre instance DPS.

1. Dans l’onglet **Vue d’ensemble**, copiez la valeur du paramètre **Étendue de l’ID**. Collez-la sur le texte d’espace réservé approprié dans la commande.

1. Fournissez l’ID d’inscription de l’appareil pour remplacer le texte d’espace réservé approprié dans la commande.

1. Remplacez le texte d’espace réservé approprié par le chemin d’accès source absolu de votre fichier de certificat.

1. Remplacez le texte d’espace réservé approprié par le chemin d’accès source absolu de votre fichier de clé privée.

1. Exécutez la commande dans une session PowerShell avec élévation de privilèges sur l’appareil cible.

---

## <a name="verify-successful-configuration"></a>Vérifier que la configuration a réussi

Vérifiez que IoT Edge pour Linux sur Windows a été correctement installé et configuré sur votre appareil IoT Edge.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Sélectionnez votre appareil IoT Edge dans la liste des appareils connectés dans Windows Admin Center pour vous y connecter.

1. La page de vue d’ensemble de l’appareil affiche des informations sur l’appareil :

    1. La section **Liste des modules IoT Edge** affiche les modules en cours d’exécution sur l’appareil. Lors du premier démarrage du service IoT Edge, seul le module **edgeAgent** apparaît dans cette liste. Le module edgeAgent s’exécute par défaut et vous aide à installer et démarrer tous les modules supplémentaires que vous déployez sur votre appareil.
    1. La section **État IoT Edge** indique l’état du service, qui doit être **actif (en cours d’exécution)** .

1. Si vous devez résoudre des problèmes sur le service IoT Edge, utilisez l’outil **Command Shell** sur la page de l’appareil pour vous connecter de manière sécurisée (SSH) à la machine virtuelle et exécuter les commandes Linux.

    1. Si vous avez besoin de résoudre les problèmes du service, récupérez les journaux d’activité de ce dernier.

       ```bash
       journalctl -u iotedge
       ```

    2. Utilisez l’outil `check` pour vérifier l’état de la configuration et de la connexion de l’appareil.

       ```bash
       sudo iotedge check
       ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Connectez-vous à votre IoT Edge pour Linux sur machine virtuelle Windows en utilisant la commande suivante dans votre session PowerShell :

   ```azurepowershell-interactive
   Ssh-EflowVm
   ```

1. Une fois connecté, vous pouvez vérifier la liste des modules IoT Edge en cours d’exécution à l’aide de la commande Linux suivante :

   ```bash
   iotedge list
   ```

1. Si vous devez dépanner le service IoT Edge, utilisez les commandes Linux suivantes.

    1. Si vous avez besoin de résoudre les problèmes du service, récupérez les journaux d’activité de ce dernier.

       ```bash
       journalctl -u iotedge
       ```

    2. Utilisez l’outil `check` pour vérifier l’état de la configuration et de la connexion de l’appareil.

       ```bash
       sudo iotedge check
       ```

---

## <a name="next-steps"></a>Étapes suivantes

Passez à [Déployer des modules IoT Edge](how-to-deploy-modules-portal.md) pour savoir comment déployer des modules sur votre appareil.
