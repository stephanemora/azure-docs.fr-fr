---
title: Provisionner automatiquement un appareil Azure IoT Edge avec le service Device Provisioning - Windows | Microsoft Docs
description: Utiliser un appareil simulé sur votre machine Windows pour tester le provisionnement automatique d’appareils pour Azure IoT Edge avec le service Device Provisioning
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 46970d5628df3b46ec88df998a328928f60e15b4
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090229"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>Créer et provisionner un appareil Edge avec TPM simulé sur Windows

Les appareils Azure IoT Edge peuvent être provisionnés automatiquement à l’aide du [service Device Provisioning](../iot-dps/index.yml) tout comme les appareils qui ne sont pas compatibles avec Edge. Si vous ne connaissez pas le processus de provisionnement automatique, révisez les [concepts du provisionnement automatique](../iot-dps/concepts-auto-provisioning.md) avant de poursuivre. 

Cet article montre comment tester le provisionnement automatique sur un appareil Edge simulé à l’aide des étapes suivantes : 

* Création d’une instance du service IoT Hub Device Provisioning.
* Création d’un appareil simulé sur votre machine Windows avec un module de plateforme sécurisée (TPM) simulé pour la sécurité du matériel.
* Création d’une inscription individuelle pour l’appareil.
* Installation du runtime IoT Edge et connexion de l’appareil à IoT Hub.

## <a name="prerequisites"></a>Prérequis

* Une machine de développement Windows. Cet article utilise Windows 10. 
* Un hub IoT actif. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurer le service IoT Hub Device Provisioning

Créez une nouvelle instance du service IoT Hub Device Provisioning dans Azure et liez-la à votre hub IoT. Vous pouvez suivre les instructions dans [Configurer le service IoT Hub Device Provisioning](../iot-dps/quick-setup-auto-provision.md).

Après avoir lancé l’exécution du service Device Provisioning, copiez la valeur de **Étendue de l’ID** à partir de la page de présentation. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge. 

## <a name="simulate-a-tpm-device"></a>Simuler un appareil TPM

Créez un appareil TPM simulé sur votre machine de développement Windows. Récupérez l’**ID d’inscription** et la **Paire de clés de type EK** pour votre appareil, et utilisez-les pour créer une entrée d’inscription individuelle dans le service Device Provisioning. 

Lorsque vous créez une inscription dans le service Device Provisioning, vous avez la possibilité de déclarer un **État initial du jumeau d’appareil**. Dans le jumeau d’appareil, vous pouvez définir des balises pour regrouper les appareils en fonction des métriques dont vous avez besoin dans votre solution, comme la région, l’environnement, l’emplacement ou le type d’appareil. Ces balises sont utilisées pour créer [des déploiements automatiques](how-to-deploy-monitor.md). 

Choisissez le langage du kit SDK que vous souhaitez utiliser pour créer l’appareil simulé et suivez les étapes jusqu’à la création de l’inscription individuelle. 

Lorsque vous créez l’inscription individuelle, sélectionnez **Activer** pour déclarer que cette machine virtuelle est un **appareil IoT Edge**.

Guides d’appareils simulés et d’inscriptions individuelles : 
* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.JS](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Après avoir créé l’inscription individuelle, enregistrez la valeur de l’**ID d’inscription**. Vous utilisez cette valeur lorsque vous configurez le runtime IoT Edge. 

## <a name="install-the-iot-edge-runtime"></a>Installer le runtime IoT Edge

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Ses composants s’exécutent dans des conteneurs et vous permettent de déployer des conteneurs supplémentaires sur l’appareil, pour que vous puissiez exécuter du code en périphérie. Sur les appareils exécutant Windows, vous pouvez choisir d’utiliser des conteneurs Windows ou des conteneurs Linux. Choisissez le type de conteneurs que vous souhaitez utiliser et suivez les étapes. Veillez à configurer le runtime IoT Edge pour le provisionnement automatique, et non manuel. 

Suivez les instructions pour installer le runtime IoT Edge sur l’appareil qui exécute le TPM simulé de la section précédente. 

Procurez-vous l’**Étendue de l’ID** de votre service Device Provisioning et l’**ID d’inscription** de votre appareil avant de commencer ces articles. 

* [Conteneurs Windows](how-to-install-iot-edge-windows-with-windows.md)
* [Conteneurs Linux](how-to-install-iot-edge-windows-with-linux.md)

## <a name="create-a-tpm-environment-variable"></a>Créer une variable d’environnement TPM

Sur la machine qui exécute votre appareil simulé, modifiez le Registre du service **iotedge** pour définir une variable d’environnement.

1. À partir du menu **Démarrer**, ouvrez **regedit**. 
2. Accédez à **Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\iotedge**. 
3. Sélectionnez **Edition** > **Nouveau** > **Valeur de chaînes multiples**. 
4. Entrez le nom **Environment**. 
5. Double-cliquez sur la nouvelle variable et définissez les données de valeur sur **IOTEDGE_USE_TPM_DEVICE = ON**. 
6. Cliquez sur **OK** pour enregistrer vos modifications. 

## <a name="restart-the-iot-edge-runtime"></a>Redémarrer le runtime IoT Edge

Redémarrez le runtime IoT Edge afin qu’il récupère toutes les modifications de configuration que vous avez effectuées sur l’appareil. 

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si le runtime a été démarré correctement, vous pouvez accéder à votre hub IoT et voir que votre nouvel appareil a été provisionné automatiquement et qu’il est prêt à exécuter des modules IoT Edge. 

Vérifiez l’état du service IoT Edge.

```powershell
Get-Service iotedge
```

Examinez les journaux du service des 5 dernières minutes.

```powershell
# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false}
```

Répertoriez les modules en cours d’exécution.

```powershell
iotedge list
```

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service Device Provisioning vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-monitor.md) ou [d’Azure CLI](how-to-deploy-monitor-cli.md)