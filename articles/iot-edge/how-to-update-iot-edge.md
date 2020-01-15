---
title: Mettre à jour la version IoT Edge sur les appareils - Azure IoT Edge | Microsoft Docs
description: Guide pratique pour mettre à jour des appareils IoT Edge afin qu’ils exécutent les dernières versions du démon de sécurité et le runtime IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1e29420b8e029a7e1723f969753eb2081381a8e8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434157"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Mettre à jour le runtime et le démon de sécurité IoT Edge

Chaque fois que le service IoT Edge publiera de nouvelles versions, vous pourrez mettre à jour vos appareils IoT Edge pour obtenir les dernières fonctionnalités et améliorations de la sécurité. Cet article fournit des informations sur la façon de mettre à jour vos appareils IoT Edge quand une nouvelle version est disponible.

Deux composants d’un appareil IoT Edge doivent être mis à jour si vous souhaitez passer à une version plus récente. Le premier est le démon de sécurité qui s’exécute sur l’appareil et démarre les modules du runtime au démarrage de l’appareil. Le démon de sécurité ne peut être mis à jour qu’à partir de l’appareil lui-même. Le second composant est le runtime, constitué des modules de l’agent IoT Edge et du hub IoT Edge. Selon la façon dont vous structurez votre déploiement, le runtime peut être mis à jour à partir de l’appareil ou à distance.

Pour rechercher la dernière version d’Azure IoT Edge, consultez [Versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Mettre à jour le démon de sécurité

Le démon de sécurité IoT Edge est un composant natif qui doit être mis à jour à l’aide du gestionnaire de package sur l’appareil IoT Edge.

Vérifiez la version du démon de sécurité qui s’exécute sur votre appareil à l’aide de la commande `iotedge version`.

### <a name="linux-devices"></a>Appareils Linux

Sur les appareils Linux x64, utilisez apt-get ou votre gestionnaire de package approprié pour mettre à jour le démon de sécurité.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Sur les appareils Linux ARM32, utilisez les étapes de [runtime installer Azure IoT Edge sur Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) pour installer la dernière version du démon de sécurité.

### <a name="windows-devices"></a>Appareils Windows

Sur les appareils Windows, utilisez le script PowerShell pour mettre à jour le démon de sécurité. Le script extrait automatiquement la dernière version du démon de sécurité.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

La commande Update-IoTEdge supprime et met à jour le démon de sécurité de l’appareil, ainsi que les deux images conteneur du runtime. Le fichier config.yaml est conservé sur l’appareil, de même que les données du moteur de conteneur Moby (dans le cas de conteneurs Windows). Le fait de conserver les informations de configuration évite d’avoir à indiquer de nouveau la chaîne de connexion ou le service Device Provisioning de votre appareil lors du processus de mise à jour.

Si vous voulez installer une certaine version du démon de sécurité, téléchargez le fichier Microsoft-Azure-IoTEdge.cab correspondant dans les [versions d’IoT Edge](https://github.com/Azure/azure-iotedge/releases). Utilisez ensuite le paramètre `-OfflineInstallationPath` pour pointer vers l’emplacement du fichier. Pour plus d’informations, voir [Installation hors connexion](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Mettre à jour les conteneurs du runtime

La façon dont vous mettez à jour les conteneurs de l’agent IoT Edge et du hub IoT Edge diffère selon que vous utilisez des étiquettes évolutives (par exemple, 1.0) ou des étiquettes spécifiques (par exemple, 1.0.7) dans votre déploiement.

Vérifiez la version des modules de l’agent IoT Edge et du hub IoT Edge sur votre appareil à l’aide des commandes `iotedge logs edgeAgent` ou `iotedge logs edgeHub`.

  ![Rechercher la version du conteneur dans les journaux d’activité](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Comprendre les étiquettes IoT Edge

Les images de l’agent IoT Edge et du hub IoT Edge sont marquées avec la version IoT Edge à laquelle elles sont associées. Il existe deux façons d’utiliser des étiquettes avec les images de runtime :

* **Étiquettes évolutives** : utilisez uniquement les deux premières valeurs du numéro de version pour obtenir la dernière image qui correspond à ces chiffres. Par exemple, 1.0 est mis à jour à chaque nouvelle version pour pointer vers la dernière version 1.0.x. Si le runtime du conteneur sur votre appareil IoT Edge réextrait l’image, les modules de runtime sont mis à jour vers la dernière version. Cette approche est conseillée à des fins de développement. Les déploiements à partir du portail Azure adoptent par défaut des étiquettes évolutives.

* **Étiquettes spécifiques** : utilisez les trois valeurs du numéro de version pour définir explicitement la version de l’image. Par exemple, la version 1.0.7 ne change pas après sa publication initiale. Vous pouvez déclarer un nouveau numéro de version dans le manifeste de déploiement quand vous êtes prêt à effectuer une mise à jour. Cette approche est conseillée à des fins de production.

### <a name="update-a-rolling-tag-image"></a>Mettre à jour une image avec des étiquettes évolutives

Si vous utilisez des étiquettes évolutives dans votre déploiement (par exemple, mcr.microsoft.com/azureiotedge-hub:**1.0**), vous devez forcer le runtime du conteneur sur votre appareil à extraire la dernière version de l’image.

Supprimez la version locale de l’image de votre appareil IoT Edge. Sur les ordinateurs Windows, la désinstallation du démon de sécurité entraîne également la suppression des images du runtime, ce qui permet de sauter cette étape.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Vous devrez peut-être utiliser l’indicateur `-f` (forcer) pour supprimer les images.

Le service IoT Edge extrait les dernières versions des images de runtime et les démarre automatiquement sur votre appareil à nouveau.

### <a name="update-a-specific-tag-image"></a>Mettre à jour une image avec des étiquettes spécifiques

Si vous utilisez des étiquettes spécifiques dans votre déploiement (par exemple, mcr.microsoft.com/azureiotedge-hub:**1.0.8**), il vous suffit de mettre à jour l’étiquette dans votre manifeste de déploiement et d’appliquer les modifications à votre appareil.

1. Dans l’IoT Hub du portail Azure, sélectionnez votre appareil IoT Edge, puis sélectionnez **Définir les modules**.

1. Dans la section **Modules IoT Edge**, sélectionnez **Paramètres d'exécution**.

   ![Configurer les paramètres d'exécution](./media/how-to-update-iot-edge/configure-runtime.png)

1. Dans **Paramètres d’exécution**, mettez à jour la valeur de l’**image** pour **Edge Hub** avec la version souhaitée. Ne sélectionnez pas encore **Enregistrer**.

   ![Mettre à jour la version de l’image Edge Hub.](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Réduisez les paramètres **Edge Hub**, ou faites défiler la liste vers le bas et mettez à jour la valeur de l’**image** pour l’**agent Edge** avec la même version souhaitée.

   ![Mettre à jour la version de l’agent Edge Hub](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Sélectionnez **Enregistrer**.

1. Sélectionnez **Vérifier + Créer**, vérifiez le déploiement, puis sélectionnez **Créer**.

## <a name="update-to-a-release-candidate-version"></a>Mettre à jour vers une version Release Candidate

Azure IoT Edge publie régulièrement de nouvelles versions du service IoT Edge. Avant chaque version stable, il y a une ou plusieurs versions Release Candidate (RC). Les versions RC incluent toutes les fonctionnalités planifiées de la version, mais sont encore sujettes aux processus de tests et de validation requis pour une version stable. Si vous souhaitez tester très tôt une nouvelle fonctionnalité, vous pouvez installer la version RC et envoyer des commentaires via GitHub.

Les versions Release Candidate suivent la même convention de numérotation que les versions publiées, mais **-rc** et un nombre incrémentiel sont ajoutés à la fin. Vous pouvez voir les versions Release Candidate dans la même liste [Versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) que les versions stables. Cherchez, par exemple, **1.0.7-rc1** et **1.0.7-rc2**, les deux versions Release Candidate mises en place avant la version **1.0.7**. Vous pouvez également voir que les versions RC sont marquées avec des étiquettes **préversion**.

Tout comme les préversions, les versions Release Candidate ne sont pas incluses comme la version la plus récente ciblée par les programmes d’installation réguliers. Au lieu de cela, vous devez cibler manuellement les ressources de la version RC que vous souhaitez tester. Selon le système d’exploitation de votre appareil IoT Edge, utilisez les sections suivantes pour mettre à jour IoT Edge vers une version spécifique :

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Étapes suivantes

Déterminez les dernières [versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Restez informé des dernières mises à jour et annonces en consultant le [blog Internet of Things](https://azure.microsoft.com/blog/topics/internet-of-things/)