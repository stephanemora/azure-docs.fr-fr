---
title: Importer un package Edge dans la solution de supervision à distance - Azure | Microsoft Docs
description: Cet article décrit comment importer un package IoT Edge dans l’accélérateur de solution de supervision à distance
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "61442937"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Importer un package IoT Edge dans votre accélérateur de solution de supervision à distance

Un manifeste de déploiement définit les modules à déployer sur un appareil IoT Edge. Cet article suppose qu’un développeur de votre organisation a déjà créé un manifeste de déploiement. Pour en savoir plus sur la façon dont un développeur crée un manifeste, consultez l’un des articles suivants sur les procédures relatives à IoT Edge :

- [Déployer des modules Azure IoT Edge à partir du portail Azure](../iot-edge/how-to-deploy-modules-portal.md)
- [Déployer des modules Azure IoT Edge avec Azure CLI](../iot-edge/how-to-deploy-modules-cli.md)
- [Déployer des modules Azure IoT Edge à partir de Visual Studio Code](../iot-edge/how-to-deploy-modules-vscode.md)

Un développeur crée et teste un manifeste de déploiement dans un environnement de développement. Quand vous êtes prêt, vous pouvez importer le manifeste dans votre accélérateur de solution de supervision à distance.

## <a name="export-a-manifest"></a>Exporter un manifeste

Utilisez le portail Azure pour exporter le manifeste de déploiement à partir de votre environnement de développement :

1. Dans le portail Azure, accédez au hub IoT que vous utilisez pour développer et tester vos appareils IoT Edge. Cliquez sur **IoT Edge**, puis sur **Déploiements IoT Edge** : ![IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Cliquez sur le déploiement avec la configuration de déploiement que vous souhaitez utiliser. La page **Détails du déploiement** s’affiche : ![Détails du déploiement IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Cliquez sur **Télécharger le manifeste IoT Edge** : ![Télécharger le manifeste de déploiement](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Enregistrez le fichier JSON comme un fichier local appelé **deploymentmanifest.json**.

Vous disposez maintenant d’un fichier qui contient le manifeste de déploiement. Dans la section suivante, vous importerez ce manifeste en tant que package dans la solution de supervision à distance.

## <a name="import-a-package"></a>Importer un package

Suivez les étapes ci-dessous pour importer un manifeste de déploiement Edge sous forme de package dans votre solution :

1. Accédez à la page **Packages** dans l’interface utilisateur web de la supervision à distance : ![Page Packages](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Cliquez sur **+ Nouveau package**, choisissez **Manifeste Edge** comme type de package, cliquez sur **Parcourir** pour sélectionner le fichier **deploymentmanifest.json** que vous avez enregistré dans la section précédente : ![Sélectionner le manifeste](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Cliquez sur **Charger** pour ajouter le package à votre solution de supervision à distance : ![Package chargé](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Vous avez maintenant chargé un manifeste de déploiement IoT Edge en tant que package. Dans la page **Déploiements**, vous pouvez déployer ce package sur vos appareils IoT Edge connectés.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment déployer des modules sur un appareil IoT Edge à partir de la solution de supervision à distance, l’étape suivante consiste à explorer [IoT Edge](../iot-edge/about-iot-edge.md).
