---
title: Provisionnement de l’agent Device Update pour Azure IoT Hub | Microsoft Docs
description: Provisionnement de l’agent Device Update pour Azure IoT Hub
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 01ce213f71fc2ac070ca0b09780bd45ede4e61c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097752"
---
# <a name="device-update-agent"></a>Agent Device Update

Device Update pour IoT Hub prend en charge deux formes de mise à jour : l’une basée sur une image et l’autre sur un package. 

* Les mises à jour basées sur une image offrent un niveau de confiance plus élevé dans l’état final de l’appareil. Il est généralement plus facile de répliquer les résultats d’une mise à jour basée sur une image entre un environnement de préproduction et un environnement de production, car cela ne présente pas les mêmes défis que les packages et leurs dépendances. En raison de leur nature atomique, il est également possible d’adopter aisément un modèle de basculement A/B. 
* Les mises à jour basées sur un package sont des mises à jour ciblées qui modifient uniquement un composant ou une application spécifique sur l’appareil. Cela réduit par conséquent la consommation de bande passante et favorise une réduction du temps de téléchargement et d’installation des mises à jour. Les mises à jour de package permettent généralement de réduire les temps d’arrêt des appareils lors de l’application d’une mise à jour et d’éviter une surcharge liée à la création d’images. 

Suivez les liens ci-dessous pour apprendre à générer, exécuter et modifier l’agent Device Update.

## <a name="build-the-device-update-agent"></a>Générer l’agent Device Update

Suivez ces instructions pour [générer](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) l’agent Device Update à partir de la source.

## <a name="run-the-device-update-agent"></a>Exécuter l’agent Device Update

Une fois l’agent correctement généré, il est temps d’[exécuter](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) cet agent.

## <a name="modifying-the-device-update-agent"></a>Modification de l’agent Device Update

À présent, apportez les modifications nécessaires pour incorporer l’agent dans votre image.  Découvrez comment [modifier](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) l’agent Device Update pour obtenir des instructions.

### <a name="troubleshooting-guide"></a>Guide de résolution des problèmes

Si vous rencontrez des problèmes, consultez le [Guide de dépannage](troubleshoot-device-update.md) de Device Update pour IoT Hub pour résoudre les éventuels problèmes et collecter les informations nécessaires à fournir à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Utilisez les images et binaires prédéfinis fournis ci-dessous pour une illustration simple de Device Update pour IoT Hub.  

[Mise à jour d’image : Démarrer avec l’image de référence Yocto Raspberry Pi 3 B+](device-update-raspberry-pi.md)

[Mise à jour d’image : Démarrer avec l’agent de référence du simulateur Ubuntu (18.04 x64)](device-update-simulator.md)

[Mise à jour du package : Démarrer avec l’agent de package Ubuntu Server 18.04 x64](device-update-ubuntu-agent.md)

