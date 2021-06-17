---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 06/08/2021
ms.openlocfilehash: 6bce3c5a01d182c67525caca9b75e3a03e59a4d9
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111983220"
---
Utilisez les réponses du runtime de l’agent IoT Edge pour résoudre les erreurs liées au calcul. Voici une liste de réponses possibles :

* 200 - OK
* 400 - La configuration de déploiement a un format incorrect ou n’est pas valide.
* 417 - Aucune configuration de déploiement n’est définie pour l’appareil.
* 412 - La version de schéma dans la configuration de déploiement n’est pas valide.
* 406 - L’appareil IoT Edge est hors connexion ou n’envoie pas de rapports d’état.
* 500 - Une erreur s’est produite dans le runtime IoT Edge.

Pour plus d’informations, consultez l’article [Agent IoT Edge](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent).
