---
title: Provisionner automatiquement des appareils Windows le service Device Provisioning et TPM - Azure IoT Edge | Microsoft Docs
description: Utiliser l’approvisionnement automatique des appareils pour IoT Edge pour Linux sur Windows avec le service Service Provisioning Service et l’attestation TPM
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 07/08/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 963a1f874bb66a923ebee04768fb238a5aecd4ee
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532807"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-with-tpm-attestation"></a>Créer et approvisionner un appareil IoT Edge pour Linux sur Windows avec attestation TPM

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Les appareils Azure IoT Edge peuvent être provisionnés à l’aide du [service Device Provisioning](../iot-dps/index.yml) tout comme les appareils qui ne sont pas compatibles avec Edge. Si vous ne connaissez pas le processus de provisionnement automatique, révisez la présentation du [provisionnement](../iot-dps/about-iot-dps.md#provisioning-process) avant de poursuivre.

DPS prend en charge l’attestation Trusted Platform Module (TPM) pour les appareils IoT Edge uniquement pour l’inscription individuelle, et non l’inscription de groupe.

Cet article explique comment utiliser l’approvisionnement automatique sur un appareil exécutant IoT Edge pour Linux sur Windows en effectuant les étapes suivantes :

* Installez IoT Edge pour Linux sur Windows.
* Récupérez les informations du module de plateforme sécurisée à partir de votre appareil.
* Création d’une inscription individuelle pour l’appareil.
* Approvisionnez votre appareil avec ses informations de module de plateforme sécurisée.

## <a name="prerequisites"></a>Configuration requise

* Un appareil Windows. Pour connaître les versions de Windows prises en charge, consultez [Systèmes d’exploitation](support.md#operating-systems).
* Un hub IoT actif.
* Une instance du service IoT Hub Device Provisioning dans Azure, liée à votre hub IoT.
  * Si vous ne disposez pas d’une instance du service Device Provisioning, suivez les instructions indiquées dans [Configurer le service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md).
  * Après avoir lancé l’exécution du service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous approvisionnez l’appareil IoT Edge.

> [!NOTE]
> TPM 2.0 est requis lors de l'utilisation de l'attestation TPM avec DPS, et il ne peut être utilisé que pour créer des inscriptions individuelles, et non groupées.

## <a name="install-iot-edge-for-linux-on-windows"></a>Installer IoT Edge pour Linux sur Windows

Les étapes d’installation de cette section sont abrégées pour mettre en évidence les étapes spécifiques du scénario de configuration du module de plateforme sécurisée (TPM). Pour obtenir des instructions plus détaillées, y compris les conditions préalables et les étapes d’installation à distance, consultez [Installer et approvisionner Azure IoT Edge pour Linux sur un appareil Windows](how-to-install-iot-edge-on-windows.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Ouvrez une session PowerShell avec élévation de privilèges sur l’appareil Windows.

1. Téléchargez IoT Edge pour Linux sur Windows.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Installez IoT Edge pour Linux sur Windows sur votre appareil.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

1. Pour que le déploiement s’exécute correctement, vous devez définir la stratégie d’exécution sur l’appareil sur `AllSigned` si ce n’est pas déjà fait.

   1. Vérifiez la stratégie d’exécution actuelle.

      ```powershell
      Get-ExecutionPolicy -List
      ```

   1. Si la stratégie d’exécution de `local machine` n’est pas `AllSigned`, mettez-la à jour.

      ```powershell
      Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
      ```

1. Déployez IoT Edge pour Linux sur Windows.

   ```powershell
   Deploy-Eflow
   ```

1. Entrez `Y` pour accepter les termes du contrat de licence.

1. Entrez `O` ou `R` pour activer ou désactiver les **données de diagnostic facultatives** à votre convenance.

1. La sortie indique **Déploiement réussi** une fois que IoT Edge pour Linux sur Windows a été déployé avec succès sur votre appareil.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>L’extension Azure IoT Edge pour Windows Admin Center est actuellement en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Les processus d’installation et de gestion peuvent différer de ceux des fonctionnalités généralement disponibles.

1. Disposez de Windows Admin Center configuré avec l’extension **Azure IoT Edge**.

1. Dans la page des connexions Windows Admin Center, sélectionnez **Ajouter**.

1. Dans le volet **Ajouter ou créer des ressources** situé dans la vignette **Azure IoT Edge**. Sélectionnez **Créer** pour installer une nouvelle instance d’Azure IoT Edge pour Linux sur un appareil.

1. Suivez les étapes de l’assistant de déploiement pour installer et configurer IoT Edge pour Linux sur Windows.

   1. Dans les étapes **Prise en main**, passez en revue les conditions préalables, acceptez les termes du contrat de licence et choisissez d’envoyer ou non les données de diagnostic.

   1. Dans les étapes de **déploiement**, choisissez votre appareil et ses paramètres de configuration. Observez ensuite la progression à mesure que IoT Edge est déployé sur votre appareil.

   1. Sélectionnez **Suivant** pour passer à l’étape **Connecter** dans laquelle vous fournissez les informations de configuration de votre appareil.

1. Arrêtez de suivre l’assistant Déploiement sur la page **Approvisionnement**, car il existe des étapes à suivre pour préparer votre appareil pour l’approvisionnement avec TPM avant de pouvoir continuer. Ne refermez pas la fenêtre Windows Admin Center, car vous y reviendrez à la fin de cet article pour effectuer les étapes d’approvisionnement.

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

Pour approvisionner votre appareil, vous devez recueillir des informations de votre puce TPM et les fournir à votre instance Device Provisioning Service (DPS) afin que le service puisse reconnaître votre appareil quand il tente de se connecter.

Tout d’abord, vous devez déterminer la **Paire de clés de type EK (Endorsement Key)** , propre à chaque puce TPM et fournie par le fabricant de la puce TPM associée. Vous devez ensuite fournir un **ID d’inscription** pour votre appareil. Vous pouvez dériver un ID d’inscription unique pour votre appareil TPM, en créant par exemple un code de hachage SHA-256 pour la paire de clés de type EK.

IoT Edge pour Linux sur Windows fournit un fonction PowerShell pour vous aider à récupérer ces informations à partir de votre module de plateforme sécurisée. Pour utiliser le script, procédez comme suit sur votre appareil :

1. Ouvrez une session PowerShell avec élévation de privilèges.

1. Exécutez la commande.

   ```powershell
   Get-EflowVmTpmProvisioningInfo
   ```

### <a name="simulate-a-tpm-optional"></a>Simuler un module TPM (facultatif)

Si vous n’avez pas de module de plateforme sécurisée (TPM) physique disponible et que vous souhaitez tester cette méthode d’approvisionnement, vous pouvez simuler un module de plateforme sécurisée sur votre appareil.

IoT Hub Device Provisioning Service fournit des exemples qui simulent un module de plateforme sécurisée et retournent la paire de clés de type EK (Endorsement Key) et l’ID d’inscription.

1. Choisissez l’un des exemples de la liste suivante, en fonction de votre langage par défaut.
1. Arrêtez de suivre les étapes de l’exemple DPS une fois que le module de plateforme sécurisée est en cours d’exécution et que vous avez collecté la clé de type EK et l’ID d’inscription. N’appuyez pas sur *Entrée* pour exécuter l’inscription dans l’exemple d’application.
1. Conservez la fenêtre hébergeant le module de plateforme sécurisée en cours d’exécution jusqu’à ce que vous ayez entièrement terminé le test de ce scénario.
1. Revenez à cet article pour créer une inscription DPS et configurer votre appareil.

Exemples de TPM simulés :

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.JS](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

## <a name="create-a-device-enrollment-entry"></a>Créer une entrée d’inscription d’appareil

Le service Device Provisioning prend en charge deux types d’inscriptions. Les groupes d’inscription permettent d’inscrire plusieurs appareils connexes. Les inscriptions individuelles permettent d’inscrire un seul appareil. L’attestation TPM ne prend en charge que les inscriptions individuelles, car un groupe d’appareils ne peut pas partager les informations du module de plateforme sécurisée. Par conséquent, cet article présente les inscriptions individuelles.

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et accédez à votre instance du service Device Provisioning.

1. Dans le menu du service Device Provisioning, sélectionnez **Gérer les inscriptions**. Sélectionnez le bouton **Ajouter une inscription individuelle** en haut.

1. Dans le volet **Ajouter une inscription**, entrez les informations suivantes :

   * Sélectionnez **TPM** comme mécanisme d’attestation d’identité.
   * Entrez l’ID d’inscription et la paire de clés de type EK (Endorsement Key) pour votre appareil TPM notés plus tôt.
   * Sélectionnez un hub IoT lié à votre service d’approvisionnement.
   * Si vous le souhaitez, entrez un ID d’appareil unique. Si vous choisissez de ne pas en fournir un, l’ID d’inscription est utilisé à la place pour identifier l’appareil.
   * Sélectionnez **Vrai** pour déclarer que cette inscription est un appareil IoT Edge.

1. Sélectionnez **Enregistrer** pour enregistrer votre inscription individuelle.

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
