---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: ad981264a99bd48e27f745a789ebe857b7f17d80
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750502"
---
Utilisez les réponses du runtime de l’agent IoT Edge pour résoudre les erreurs liées au calcul. Voici une liste de réponses possibles :

* 200 - OK
* 400 - La configuration de déploiement a un format incorrect ou n’est pas valide.
* 417 - Aucune configuration de déploiement n’est définie pour l’appareil.
* 412 - La version de schéma dans la configuration de déploiement n’est pas valide.
* 406 - L’appareil IoT Edge est hors connexion ou n’envoie pas de rapports d’état.
* 500 - Une erreur s’est produite dans le runtime IoT Edge.

Pour plus d’informations, consultez l’article [Agent IoT Edge](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent).

L’erreur suivante est liée au service IoT Edge sur votre Azure Stack Edge Pro<!--/ Data Box Gateway--> appareil.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Les modules de calcul ont un état Inconnu et ne peuvent pas être utilisés

#### <a name="error-description"></a>Description de l'erreur

Tous les modules sur l’appareil affichent un état Inconnu et ne peuvent pas être utilisés. L’état Inconnu persiste lors d’un redémarrage.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Solution suggérée

Supprimez le service IoT Edge, puis redéployez le(s) module(s). Pour plus d’informations, consultez [Supprimer le service IoT Edge](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).