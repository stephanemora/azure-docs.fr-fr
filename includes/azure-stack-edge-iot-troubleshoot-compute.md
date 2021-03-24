---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 03/02/2021
ms.openlocfilehash: 57415ec76a3e8d9fc3c160b47668d3419ff6ea5c
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622293"
---
Utilisez les réponses du runtime de l’agent IoT Edge pour résoudre les erreurs liées au calcul. Voici une liste de réponses possibles :

* 200 - OK
* 400 - La configuration de déploiement a un format incorrect ou n’est pas valide.
* 417 - Aucune configuration de déploiement n’est définie pour l’appareil.
* 412 - La version de schéma dans la configuration de déploiement n’est pas valide.
* 406 - L’appareil IoT Edge est hors connexion ou n’envoie pas de rapports d’état.
* 500 - Une erreur s’est produite dans le runtime IoT Edge.

Pour plus d’informations, consultez l’article [Agent IoT Edge](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent).

L’erreur suivante est liée au service IoT Edge sur votre appareil Azure Stack Edge Pro.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Les modules de calcul ont un état Inconnu et ne peuvent pas être utilisés

#### <a name="error-description"></a>Description de l'erreur

Tous les modules sur l’appareil affichent un état Inconnu et ne peuvent pas être utilisés. L’état Inconnu persiste lors d’un redémarrage.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Solution suggérée

Supprimez le service IoT Edge, puis redéployez le(s) module(s). Pour plus d’informations, consultez [Supprimer le service IoT Edge](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).


### <a name="modules-show-as-running-but-are-not-working"></a>Les modules s’affichent comme étant en cours d’exécution mais ne fonctionnent pas

#### <a name="error-description"></a>Description de l'erreur

L’état d’exécution du module indique en cours d’exécution mais les résultats attendus ne sont pas visibles. 

Cette condition peut être due à un problème de configuration d’itinéraire de module qui ne fonctionne pas ou au fait que `edgehub` n’achemine pas les messages comme prévu. Vous pouvez vérifier les journaux `edgehub`. Si des erreurs telles que l’échec de la connexion au service IoT Hub sont signalées, elles sont le plus souvent dues à des problèmes de connectivité. Les problèmes de connectivité peuvent être liés au fait que le port AMPQ utilisé comme port par défaut par IoT Hub service pour la communication soit bloqué ou que le serveur proxy web bloque ces messages.

#### <a name="suggested-solution"></a>Solution suggérée

Effectuez les étapes suivantes :
1. Pour résoudre l’erreur, accédez à la ressource IoT Hub de votre appareil, puis sélectionnez votre appareil Edge. 
1. Accédez à **Définir les modules > Paramètres d’exécution**. 
1. Ajoutez la variable d’environnement `Upstream protocol` et attribuez-lui la valeur `AMQPWS`. Les messages configurés dans ce cas sont envoyés via WebSocket via le port 443.

### <a name="modules-show-as-running-but-do-not-have-an-ip-assigned"></a>Les modules s’affichent comme étant en cours d’exécution mais n’ont pas d’adresse IP affectée

#### <a name="error-description"></a>Description de l'erreur

L’état d’exécution du module indique en cours d’exécution mais aucune adresse IP n’est affectée à l’application en conteneur. 

Cette condition est due au fait que la plage d’adresses IP que vous avez fournie pour les adresses IP de service externe Kubernetes n’est pas suffisante. Vous devez étendre cette plage pour vous assurer que chaque conteneur ou machine virtuelle que vous avez déployée est couvert.

#### <a name="suggested-solution"></a>Solution suggérée

Procédez comme suit dans l’interface utilisateur web locale de votre appareil :
1. Accédez à la page **Calcul**. Sélectionnez le port pour lequel vous avez activé le réseau de calcul. 
1. Entrez une plage d’adresses IP statiques et contiguës pour les **adresses IP de service externe Kubernetes**. Une adresse IP est requise pour le service `edgehub`. Une adresse IP est également requise pour chaque module IoT Edge et pour chaque machine virtuelle que vous déployez. 
1. Sélectionnez **Appliquer**. La plage d’adresses IP modifiée doit prendre effet immédiatement.

Pour plus d’informations, consultez [Modifier les adresses IP de service externe pour les conteneurs](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#change-external-service-ips-for-containers).