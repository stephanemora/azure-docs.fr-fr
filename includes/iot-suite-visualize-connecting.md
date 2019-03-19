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
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56988076"
---
## <a name="view-device-telemetry"></a>Afficher la télémétrie d’appareil

Vous pouvez afficher les données de télémétrie envoyées à partir de votre appareil le **Device Explorer** page dans la solution.

1. Sélectionnez l’appareil que vous avez configuré dans la liste des appareils sur le **Device Explorer** page. Un panneau affiche des informations sur votre périphérique, y compris un tracé de la télémétrie du périphérique :

    ![Consultez les détails du périphérique](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Choisissez **Pression** pour modifier l’affichage des données de télémétrie :

    ![Télémétrie les données de télémétrie de la pression](media/iot-suite-visualize-connecting/devicespressure.png)

1. Pour afficher les informations de diagnostic relatives à votre périphérique, faites défiler la liste jusqu'à **Diagnostics** :

    ![Afficher les diagnostics du périphérique](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agir sur votre périphérique

Pour appeler des méthodes sur vos appareils, utilisez le **Device Explorer** page dans la solution de surveillance à distance. Par exemple, dans la solution de surveillance à distance **Chiller** appareils implémentent un **redémarrer** (méthode).

1. Choisissez **appareils** pour accéder à la **Device Explorer** page dans la solution.

1. Sélectionnez l’appareil que vous avez configuré dans la liste des appareils sur le **Device Explorer** page :

    ![Sélectionner votre appareil réel](media/iot-suite-visualize-connecting/devicesselect.png)

1. Pour afficher une liste des méthodes que vous pouvez appeler sur votre appareil, choisissez **travaux**, puis **méthodes**. Pour planifier un travail à exécuter sur plusieurs appareils, vous pouvez sélectionner plusieurs appareils dans la liste. Le volet **Travaux** affiche les types de méthodes communes à tous les appareils que vous avez sélectionnés.

1. Choisissez **redémarrer**, la valeur est le nom du travail **RebootPhysicalChiller** , puis **appliquer**:

    ![Planifier la mise à jour du microprogramme](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Une série de messages s’affiche dans la console exécutant votre code de périphérique lorsque le périphérique simulé traite la méthode.

> [!NOTE]
> Pour suivre l’état de la tâche dans la solution, choisissez **afficher l’état de travail**.

## <a name="next-steps"></a>Étapes suivantes

L’article [Personnaliser l’accélérateur de la solution Monitoring à distance](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) décrit quelques méthodes permettant de personnaliser l’accélérateur de solution.