---
title: Provisionner automatiquement des appareils Windows le service Device Provisioning - Azure IoT Edge | Microsoft Docs
description: Utiliser un appareil simulé sur votre machine Windows pour tester le provisionnement automatique d’appareils pour Azure IoT Edge avec le service Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1ad8435626b35859968bdf93589f22dc81e74e02
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557697"
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

À l’issue de la section précédente, vous devez voir votre nouvel appareil répertorié en tant qu’appareil IoT Edge dans votre IoT Hub. À présent, vous devez installer le runtime IoT Edge sur votre appareil. 

Le runtime IoT Edge est déployé sur tous les appareils IoT Edge. Ses composants s’exécutent dans des conteneurs et vous permettent de déployer des conteneurs supplémentaires sur l’appareil, pour que vous puissiez exécuter du code en périphérie.  

Suivez les instructions pour installer le runtime IoT Edge sur l’appareil qui exécute le TPM simulé de la section précédente. Veillez à configurer le runtime IoT Edge pour le provisionnement automatique, et non manuel.

Procurez-vous l’**Étendue de l’ID** de votre DPS et l’**ID d’inscription** de votre appareil avant d’installer IoT Edge sur votre appareil. 

[Installer et approvisionner automatiquement IoT Edge](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si le runtime a démarré correctement, vous pouvez accéder à votre IoT Hub et commencer à déployer des modules IoT Edge sur votre appareil. Utilisez les commandes suivantes sur votre appareil pour vérifier que le runtime a été installé et démarré correctement.  

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
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Répertoriez les modules en cours d’exécution.

```powershell
iotedge list
```

## <a name="next-steps"></a>Étapes suivantes

Le processus d’inscription auprès du service Device Provisioning vous permet de définir l’ID d’appareil et les balises du jumeau d’appareil en même temps que vous provisionnez le nouvel appareil. Vous pouvez utiliser ces valeurs pour cibler des appareils individuels ou des groupes d’appareils avec la gestion d’appareils automatique. En savoir plus sur [Déployer et surveiller des modules IoT Edge à grande échelle à l’aide du portail Azure](how-to-deploy-monitor.md) ou [d’Azure CLI](how-to-deploy-monitor-cli.md)
