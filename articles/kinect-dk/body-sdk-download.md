---
title: Téléchargement du Kit de développement logiciel (SDK) de suivi de corps d’Azure Kinect
description: Découvrez comment télécharger chaque version du Kit de développement logiciel (SDK) de capteur Azure Kinect sur Windows ou Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: conceptual
keywords: azure, kinect, sdk, mise à jour de téléchargement, dernier, disponible, installer, corps, suivi
ms.openlocfilehash: 60018df56433785f3cb723dd54cc96a8e5289b67
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654490"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Télécharger le Kit de développement logiciel (SDK) de suivi de corps d’Azure Kinect

Ce document fournit des liens pour installer chaque version du Kit de développement logiciel (SDK) de suivi de corps d’Azure Kinect.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Contenu du Kit de développement logiciel (SDK) de suivi de corps d’Azure Kinect

- En-têtes et bibliothèques pour créer une application à l’aide de la solution Azure Kinect DK.
- DLL redistribuables requises par les applications de suivi de corps utilisant la solution Azure Kinect DK.
- Exemples d’applications de suivi de corps.

## <a name="windows-download-links"></a>Liens de téléchargement Windows

Version       | Téléchargement
--------------|----------
1.1.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=102901)
1.0.1 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100942) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [msi](https://www.microsoft.com/en-us/download/details.aspx?id=100848) [nuget](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)

## <a name="linux-installation-instructions"></a>Instructions d’installation Linux

Actuellement, la seule distribution prise en charge est Ubuntu 18.04 et 20.04. Pour demander la prise en charge d’autres distributions, consultez [cette page](https://aka.ms/azurekinectfeedback).

Tout d’abord, vous devez configurer le [dépôt du package Microsoft](https://packages.microsoft.com/), en suivant les instructions reprises [ici](/windows-server/administration/linux-package-repository-for-microsoft-software).

Le package `libk4abt<major>.<minor>-dev` contient les en-têtes et les fichiers CMake à créer par rapport à `libk4abt`.
Le package `libk4abt<major>.<minor>` contient les objets partagés nécessaires pour exécuter les exécutables qui dépendent de `libk4abt`, ainsi que l’exemple de visionneuse.

Les didacticiels de base nécessitent le package `libk4abt<major>.<minor>-dev`. Pour l'installer, exécutez

`sudo apt install libk4abt<major>.<minor>-dev`

Si la commande est réussie, le Kit de développement logiciel (SDK) est prêt à être utilisé.

> [!NOTE]
> Lorsque vous installez le Kit de développement logiciel (SDK), rappelez-vous le chemin d’installation. Par exemple, « C:\Program Files\Azure Kinect Body Tracking SDK 1.0.0 ». Les exemples référencés dans les articles sont accessibles via ce chemin.
> Les exemples de suivi de corps se trouvent dans le dossier [Body-Tracking-Samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) dans le référentiel Azure-Kinect-Samples. Les exemples référencés dans les articles se trouvent ici.

## <a name="change-log"></a>Journal des modifications

### <a name="v110"></a>v1.1.0
* [Fonctionnalité] Ajout de la prise en charge de l’exécution DirectML (Windows uniquement) et TensorRT du modèle d’estimation de pose. Consultez le Forum aux questions sur les nouveaux environnements d’exécution.
* [Fonctionnalité] Ajout de `model_path` à la structure `k4abt_tracker_configuration_t`. Permet aux utilisateurs de spécifier le chemin d’accès pour le modèle d’estimation de pose. La valeur par défaut est le modèle d’estimation de pose standard `dnn_model_2_0_op11.onnx` situé dans le répertoire actif.
* [Fonctionnalité] Incluez le modèle d’estimation de pose `dnn_model_2_0_lite_op11.onnx` lite. Ce modèle stocke est environ 2 fois plus rapide au détriment d’environ 5 % de précision.
* [Fonctionnalité] Les exemples vérifiés compilent avec Visual Studio 2019 et mettent à jour les exemples pour utiliser cette version.
* [Changement cassant] Mise à jour vers les environnements d’exécution ONNX Runtime 1.6 avec prise en charge du processeur, CUDA 11.1, DirectML (Windows uniquement) et TensorRT 7.2.1. Nécessite la mise à jour du pilote Nvidia vers R455 ou une version ultérieure.
* [Changement cassant] Aucune installation de NuGet.
* [Correctif de bogue] Ajout la prise en charge des GPU de la série Nvidia RTX 30xx [Lien](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [Correctif de bogue] Ajout de la prise en charge des GPU intégrés AMD et Intel (Windows uniquement) [Lien](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1481)
* [Correctif de bogue] Mise à jour vers CUDA 11.1 [Lien](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1125)
* [Correctif de bogue] Mise à jour vers Sensor SDK 1.4.1 [Lien](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1248)

### <a name="v101"></a>v1.0.1
* [Correctif de bogue] Résolution du problème de blocage du Kit de développement logiciel (SDK) lors du chargement d’onnxruntime. dll à partir du chemin d’accès sur Windows Build 19025 ou version ultérieure : [Lien](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v1.0.0
* [Fonctionnalité] Ajout d’un wrapper C# au programme d’installation msi.
* [Correctif de bogue] Résolution du problème de non-détection correcte de la rotation la tête : [Lien](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Correctif de bogue] Résolution du problème d’utilisation de l’UC jusqu’à 100 % sur une machine Linux : [Lien](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* [Exemples] Ajout de deux exemples au référentiel. L’exemple 1 montre comment transformer les résultats de suivi de corps de l’espace de profondeur en espace de couleurs [Lien](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample) ; l’exemple 2 montre comment détecter un plan de sol [Lien](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample)

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du kit Azure Kinect DK](about-azure-kinect-dk.md)

- [Configurer Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Configurer le suivi des corps Azure Kinect](body-sdk-setup.md)