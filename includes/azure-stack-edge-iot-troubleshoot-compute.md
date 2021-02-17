---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: b06b91e972fd07543cf02105360cb0400ef6b0f1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831539"
---
Utilisez les réponses du runtime de l’agent IoT Edge pour résoudre les erreurs liées au calcul. Voici une liste de réponses possibles :

* 200 - OK
* 400 - La configuration de déploiement a un format incorrect ou n’est pas valide.
* 417 - Aucune configuration de déploiement n’est définie pour l’appareil.
* 412 - La version de schéma dans la configuration de déploiement n’est pas valide.
* 406 - L’appareil IoT Edge est hors connexion ou n’envoie pas de rapports d’état.
* 500 - Une erreur s’est produite dans le runtime IoT Edge.

Pour plus d’informations, consultez l’article [Agent IoT Edge](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent).

L’erreur suivante est liée au service IoT Edge sur votre Azure Stack Edge Pro<!--/ Data Box Gateway--> appareil.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Les modules de calcul ont un état Inconnu et ne peuvent pas être utilisés

#### <a name="error-description"></a>Description de l'erreur

Tous les modules sur l’appareil affichent un état Inconnu et ne peuvent pas être utilisés. L’état Inconnu persiste lors d’un redémarrage.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Solution suggérée

Supprimez le service IoT Edge, puis redéployez le(s) module(s). Pour plus d’informations, consultez [Supprimer le service IoT Edge](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).
