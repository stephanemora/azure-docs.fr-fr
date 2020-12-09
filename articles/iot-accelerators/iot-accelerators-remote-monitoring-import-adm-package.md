---
title: Importer un package dans la solution de supervision à distance - Azure | Microsoft Docs
description: Cet article décrit comment importer un package de gestion automatique des appareils dans l’accélérateur de solution de supervision à distance
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 266e31ae9865c8fb427e06e89cd755e7ff38b27f
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073867"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Importer un package de gestion automatique des appareils dans votre accélérateur de solution de supervision à distance

Une configuration de gestion automatique des appareils définit les modifications de configuration à déployer sur un groupe d’appareils. Cet article suppose qu’un développeur de votre organisation a déjà créé une configuration de gestion automatique des appareils. Pour en savoir plus sur la façon dont un développeur crée une configuration, consultez un des articles de procédure suivants sur IoT Hub :

- [Configurer et surveiller des appareils IoT à grande échelle avec le portail Azure](../iot-hub/iot-hub-automatic-device-management.md)
- [Configurer et surveiller des appareils IoT à grande échelle avec Azure CLI](../iot-hub/iot-hub-automatic-device-management-cli.md)

Un développeur crée et teste une configuration de gestion automatique des appareils dans un environnement de développement. Quand vous êtes prêt, vous pouvez importer la configuration dans votre accélérateur de solution de supervision à distance.

## <a name="export-a-configuration"></a>Exporter une configuration

Utilisez le portail Azure pour exporter la configuration de gestion automatique des appareils à partir de votre environnement de développement :

1. Dans le portail Azure, accédez au hub IoT que vous utilisez pour développer et tester vos appareils IoT. Cliquez sur **Configuration d’appareil IoT** :

    [![Configuration d’appareil IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Cliquez sur la configuration que vous voulez utiliser. La page **Détails de la configuration de l’appareil** s’affiche :

    [![Détails la configuration d’appareil IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Cliquez sur **Télécharger le fichier de configuration** :

    [![Télécharger le fichier configuration](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Enregistrez le fichier JSON comme fichier local nommé **configuration.json**.

Vous disposez maintenant d’un fichier qui contient la configuration de la gestion automatique des appareils. Dans la section suivante, vous importez cette configuration en tant que package dans la solution de supervision à distance.

## <a name="import-a-package"></a>Importer un package

Suivez les étapes ci-dessous pour importer une configuration de gestion automatique des appareils en tant que package dans votre solution :

1. Accédez à la page **Packages** dans l’interface utilisateur web de la supervision à distance : ![Page Packages](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Cliquez sur **+ Nouveau package**, choisissez **Configuration** comme type de package, puis cliquez sur **Parcourir** pour sélectionner le fichier **configuration.json** que vous avez enregistré dans la section précédente :

    ![Sélectionner une configuration](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Cliquez sur **Charger** pour ajouter le package à votre solution de supervision à distance :

    ![Package chargé](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Vous avez maintenant chargé une configuration de gestion automatique des appareils en tant que package. Dans la page **Déploiements**, vous pouvez déployer ce package sur vos appareils connectés.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment créer un package de configuration et comment l’importer dans la solution de supervision à distance, l’étape suivante consiste à découvrir comment [gérer les appareils connectés pour la supervision à distance en bloc](iot-accelerators-remote-monitoring-bulk-configuration-update.md).