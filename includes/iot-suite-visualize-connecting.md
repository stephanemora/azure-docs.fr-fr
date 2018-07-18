---
title: Fichier Include
description: Fichier Include
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 5702c6e9c9d75c6cccb82f1c57684ef7b9898c34
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34666007"
---
## <a name="view-device-telemetry"></a>Voir la télémétrie de vos appareils

Vous pouvez voir les données de télémétrie envoyées à partir de votre appareil dans la page **Appareils** de la solution.

1. Sélectionnez l’appareil que vous avez configuré dans la liste des appareils de la page **Appareils**. Un panneau affiche des informations sur votre appareil, notamment un tracé de la télémétrie de l’appareil :

    ![Voir les détails de l’appareil](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Choisissez **Pression** pour modifier l’affichage des données de télémétrie :

    ![Voir les données de télémétrie de la pression](media/iot-suite-visualize-connecting/devicespressure.png)

1. Pour afficher les informations de diagnostic relatives à votre appareil, faites défiler la liste jusqu'à **Diagnostics** :

    ![Voir les diagnostics de l’appareil](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agir sur votre appareil

Pour appeler des méthodes sur vos appareils, utilisez la page **Appareils** dans la solution Monitoring à distance. Par exemple, dans la solution Monitoring à distance, les appareils **Condenseur** implémentent une méthode **FirmwareUpdate**.

1. Choisissez **Appareils** pour accéder à la page **Appareils** dans la solution.

1. Sélectionnez l’appareil que vous avez configuré dans la liste des appareils de la page **Appareils** :

    ![Sélectionner votre appareil physique](media/iot-suite-visualize-connecting/devicesselect.png)

1. Pour afficher une liste des méthodes que vous pouvez appeler sur votre appareil, choisissez **Travaux**, puis **Run method** (Exécuter une méthode). Pour planifier un travail à exécuter sur plusieurs appareils, vous pouvez sélectionner plusieurs appareils dans la liste. Le volet **Travaux** affiche les types de méthodes communes à tous les appareils que vous avez sélectionnés.

1. Sélectionnez **FirmwareUpdate**, définissez le nom de la tâche par **UpdatePhysicalChiller**. Définissez la **version du microprogramme** sur **2.0.0**, définissez **l’URI du microprogramme** sur **http://contoso.com/updates/firmware.bin**, puis sélectionnez **Appliquer** :

    ![Planifier la mise à jour du microprogramme](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Une série de messages s’affiche dans la console exécutant votre code de périphérique lorsque le périphérique simulé traite la méthode.

1. Lorsque la mise à jour est terminée, la nouvelle version du microprogramme s’affiche sur la page **Périphériques** :

    ![Mise à jour effectuée](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> Pour suivre l’état de la tâche dans la solution, choisissez **Afficher**.

## <a name="next-steps"></a>Étapes suivantes

L’article [Personnaliser l’accélérateur de la solution Monitoring à distance](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) décrit quelques méthodes permettant de personnaliser l’accélérateur de solution.