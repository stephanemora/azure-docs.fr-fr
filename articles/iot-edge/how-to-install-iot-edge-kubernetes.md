---
title: Guide pratique pour installer IoT Edge sur Kubernetes | Microsoft Docs
description: Découvrez comment installer IoT Edge sur Kubernetes avec un environnement de cluster de développement local.
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d11d23cf7d96482028a9d3738196fc5a787fec91
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510207"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Guide pratique pour installer IoT Edge sur Kubernetes (préversion)

IoT Edge peut s’intégrer à Kubernetes avec une couche d’infrastructure résiliente et hautement disponible. Il enregistre une *définition de ressource personnalisée* (CRD) IoT Edge auprès du serveur d’API Kubernetes. En outre, il fournit un *Opérateur* (agent IoT Edge) qui rapproche l’état souhaité géré par le cloud de l’état du cluster local.

La durée de vie des modules est gérée par le planificateur Kubernetes, qui maintient leur disponibilité et choisit leur positionnement. IoT Edge administre la plateforme d’applications de périphérie qui s’exécute dessus, réconciliant en permanence l’état souhaité spécifié dans IoT Hub avec l’état présent sur le cluster de périphérie. Le modèle d’application de périphérie est toujours celui qui s’appuie sur les itinéraires et les modules IoT Edge. L’opérateur de l’agent IoT Edge effectue une traduction *automatique* vers les constructions Kubernetes natives comme les pods, les déploiements, les services, etc.

Voici un diagramme de l’architecture de haut niveau :

![Arche Kubernetes](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Chacun des composants du déploiement de périphérie est limité à un espace de noms Kubernetes propre à l’appareil, ce qui permet de partager les mêmes ressources de cluster entre plusieurs appareils de périphérie et leurs déploiements.

>[!NOTE]
>IoT Edge sur Kubernetes est en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Installer en local pour créer un environnement de test rapide

### <a name="prerequisites"></a>Conditions préalables requises

* Kubernetes 1.10 ou une version ultérieure. Si vous n’avez pas de configuration de cluster, vous pouvez utiliser [Minikube](https://kubernetes.io/docs/setup/minikube/) comme environnement de cluster local.

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), le gestionnaire de package de Kubernetes.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) pour afficher le cluster et interagir avec lui.

### <a name="setup-steps"></a>Étapes de configuration

1. Lancez **Minikube**.

    ``` shell
    minikube start
    ```

1. Initialisez le composant serveur **Helm** (*tiller*) dans votre cluster.

    ``` shell
    helm init
    ```

1. Ajoutez le référentiel de IoT Edge et mettez à jour de l’installation de Helm.

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Créez un hub IoT](../iot-hub/iot-hub-create-through-portal.md), [inscrivez un appareil IoT Edge](how-to-register-device.md) et notez sa chaîne de connexion.

1. Installez iotedged et l’agent IoT Edge dans votre cluster.

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```

1. Ouvrez le tableau de bord Kubernetes dans le navigateur.

    ```shell
    minikube dashboard
    ```

    Sous les espaces de noms du cluster, il en apparaît un pour l’appareil IoT Edge qui suit la convention *msiot-\<nom-IoTHub>-\<nom-appareilEdge>* . Les pods iotedged et l’agent IoT Edge doivent être opérationnels dans cet espace de noms.

1. Ajoutez un module de capteur de température simulé suivant la procédure de la section [Déployer un module](quickstart-linux.md#deploy-a-module) du démarrage rapide. La gestion des modules IoT Edge se fait sur le portail IoT Hub comme pour n’importe quel appareil IoT Edge. Il n’est pas recommandé d’apporter des modifications locales à la configuration des modules avec les outils Kubernetes, car il y a un risque de remplacement.

1. Au bout de quelques secondes, actualisez la page **Pods** sous l’espace de noms de l’appareil de périphérie, dans le tableau de bord : le hub IoT Edge et les pods du capteur simulé apparaissent en cours d’exécution avec le pod du hub IoT Edge qui ingère les données dans IoT Hub.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer toutes les ressources créées par le déploiement de périphérie, exécutez la commande suivante avec le nom utilisé à l’étape 5 de la section précédente.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Étapes suivantes

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Déployer l’appareil comme une passerelle de périphérie hautement disponible

L’appareil de périphérie d’un cluster Kubernetes peut servir de passerelle IoT pour les appareils en aval. Il peut être configuré pour résister aux défaillances de nœud et ainsi assurer la haute disponibilité des déploiements de périphérie. Pour utiliser IoT Edge dans ce scénario, voir cette [procédure pas à pas détaillée](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes).
