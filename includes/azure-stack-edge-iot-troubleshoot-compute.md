---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 03/23/2021
ms.openlocfilehash: 0d912d0ac3f0fcf4c52116e67909038a1973304b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105502"
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

Supprimez le service IoT Edge, puis redéployez le(s) module(s). Pour plus d’informations, consultez [Supprimer le service IoT Edge](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#remove-iot-edge-service).


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

Pour plus d’informations, consultez [Modifier les adresses IP de service externe pour les conteneurs](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#change-external-service-ips-for-containers).

### <a name="configure-static-ips-for-iot-edge-modules"></a>Configurer des adresses IP statiques pour les modules IoT Edge

#### <a name="problem-description"></a>Description du problème

Kubernetes attribue des adresses IP dynamiques à chaque module IoT Edge sur votre appareil GPU Azure Stack Edge Pro. Une méthode est nécessaire pour configurer des adresses IP statiques pour les modules.

#### <a name="suggested-solution"></a>Solution suggérée

Vous pouvez spécifier des adresses IP fixes pour vos modules IoT Edge à l’aide de la section K8s-experimental, comme décrit ci-dessous : 

```yaml
{
  "k8s-experimental": {
    "serviceOptions" : {
      "loadBalancerIP" : "100.23.201.78",
      "type" : "LoadBalancer"
    }
  }
}
```
### <a name="expose-kubernetes-service-as-cluster-ip-service-for-internal-communication"></a>Exposer le service Kubernetes comme service IP de cluster pour la communication interne

#### <a name="problem-description"></a>Description du problème

Par défaut, le service IoT est de type équilibreur de charge et se voit attribuer des adresses IP tournées vers l’extérieur. Il se peut que vous ne souhaitiez pas une adresse IP tournée vers l’extérieur pour votre application. Vous devrez peut-être exposer les pods au sein du cluster Kubernetes pour l’accès en tant qu’autres pods et non en tant que service d’équilibreur de charge exposé à l’extérieur. 

#### <a name="suggested-solution"></a>Solution suggérée

Vous pouvez utiliser les options de création via la section K8s-experimental. L’option de service suivante doit fonctionner avec des liaisons de port.

```yaml
{
"k8s-experimental": {
  "serviceOptions" : {
    "type" : "ClusterIP"
    }
  }
}
```