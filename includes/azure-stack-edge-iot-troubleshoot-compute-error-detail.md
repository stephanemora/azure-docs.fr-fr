---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 06/09/2021
ms.openlocfilehash: 86ccf634a9c33d3e8cfb8efd97e94f322fd5127f
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987813"
---
### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Les modules de calcul ont un état Inconnu et ne peuvent pas être utilisés

#### <a name="error-description"></a>Description de l'erreur

Tous les modules sur l’appareil affichent un état Inconnu et ne peuvent pas être utilisés. L’état Inconnu persiste lors d’un redémarrage.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Solution suggérée

Supprimez le service IoT Edge, puis redéployez le(s) module(s). Pour plus d’informations, consultez [Supprimer le service IoT Edge](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#remove-iot-edge-service).


### <a name="modules-show-as-running-but-are-not-working"></a>Les modules s’affichent comme étant en cours d’exécution mais ne fonctionnent pas

#### <a name="error-description"></a>Description de l'erreur

L’état d’exécution du module indique en cours d’exécution, mais vous ne voyez pas les résultats attendus. 

Cette condition peut être due à une configuration des itinéraires du module qui ne fonctionne pas, ou `edgehub` n’achemine pas les messages comme prévu. Vous pouvez vérifier les journaux `edgehub`. Si des erreurs telles que l’échec de la connexion au service IoT Hub sont signalées, elles sont le plus souvent dues à des problèmes de connectivité. Les problèmes de connectivité peuvent être liés au fait que le port AMPQ que le service IoT Hub utilise comme port par défaut pour la communication est bloqué ou que le serveur proxy web bloque ces messages.

#### <a name="suggested-solution"></a>Solution suggérée

Effectuez les étapes suivantes :
1. Pour résoudre l’erreur, accédez à la ressource IoT Hub de votre appareil, puis sélectionnez votre appareil Edge. 
1. Accédez à **Définir les modules > Paramètres d’exécution**. 
1. Ajoutez la variable d’environnement `Upstream protocol` et attribuez-lui la valeur `AMQPWS`. Les messages configurés dans ce cas sont envoyés via WebSocket via le port 443.

### <a name="modules-show-as-running-but-do-not-have-an-ip-assigned"></a>Les modules s’affichent comme étant en cours d’exécution mais n’ont pas d’adresse IP affectée

#### <a name="error-description"></a>Description de l'erreur

L’état d’exécution du module indique en cours d’exécution, mais aucune adresse IP n’est affectée à l’application en conteneur. 

Cette condition est due au fait que la plage d’adresses IP que vous avez fournie pour les adresses IP du service externe Kubernetes n’est pas suffisante. Étendez cette plage pour vous assurer que chaque conteneur ou machine virtuelle que vous avez déployé est couvert.

#### <a name="suggested-solution"></a>Solution suggérée

Procédez comme suit dans l’interface utilisateur web locale de votre appareil :
1. Accédez à la page **Calcul**. Sélectionnez le port pour lequel vous avez activé le réseau de calcul. 
1. Entrez une plage d’adresses IP statiques et contiguës pour les **adresses IP de service externe Kubernetes**. Une adresse IP est requise pour le service `edgehub`. Une adresse IP est également requise pour chaque module IoT Edge et pour chaque machine virtuelle que vous déployez. 
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

Par défaut, le service IoT est de type **équilibreur de charge** et se voit attribuer des adresses IP tournées vers l’extérieur. Si une application a besoin de pods Kubernetes dans le cluster Kubernetes pour accéder à d’autres pods du cluster, vous devrez peut-être configurer le service en tant que service d’IP de cluster au lieu d’un service d’équilibreur de charge. Pour plus d’informations, consultez [Mise en réseau Kubernetes sur votre appareil GPU Azure Stack Edge Pro](../articles/databox-online/azure-stack-edge-gpu-kubernetes-networking.md).

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