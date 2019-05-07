---
title: Comment installer IoT Edge sur Kubernetes | Microsoft Docs
description: En savoir plus sur la façon d’installer IoT Edge sur Kubernetes à l’aide d’un environnement de cluster de développement local
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 66aca7be9a2df93d846d7e78bc64c93279afc2d1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160694"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Comment installer IoT Edge sur Kubernetes (version préliminaire)

IoT Edge peut intégrer avec Kubernetes à l’utiliser comme une couche d’infrastructure résilientes, hautement disponibles. Il enregistre une IoT Edge *définition de ressource personnalisé* (CRD) avec le serveur d’API Kubernetes. En outre, il fournit un *opérateur* (agent IoT Edge) qui rapproche d’état souhaité, géré par le cloud avec l’état du cluster local. 

Durée de vie de module est gérée par le Planificateur de Kubernetes, ce qui maintient la disponibilité du module et choisit de leur positionnement. IoT Edge gère la plateforme d’application edge en cours d’exécution sur le haut, réconciliation en permanence l’état souhaité spécifié dans IoT Hub avec l’état sur le cluster de bord. Le modèle d’application edge est toujours le modèle familier basé sur les itinéraires et les modules IoT Edge. L’opérateur de l’agent IoT Edge exécute *automatique* traduction vers les natifs Kubernetes construit comme pods, des déploiements, des services, etc.

Voici un diagramme d’architecture de haut niveau :

![arch de kubernetes](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Chaque composant du déploiement de périphérie est limité à un espace de noms Kubernetes spécifique à l’appareil, ce qui permet de partager les mêmes ressources de cluster entre plusieurs appareils de périphérie et leurs déploiements.

>[!NOTE]
>IoT Edge sur Kubernetes est en [version préliminaire publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Installer localement pour un environnement de test rapide

### <a name="prerequisites"></a>Conditions préalables

* Kubernetes 1,10 ou une version ultérieure. Si vous n’avez pas une configuration de cluster existante, vous pouvez utiliser [Minikube](https://kubernetes.io/docs/setup/minikube/) pour un environnement de cluster local. 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), le Gestionnaire de package de Kubernetes.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) pour afficher et interagir avec le cluster.

### <a name="setup-steps"></a>Étapes de configuration

1. Démarrer **Minikube**

    ``` shell
    minikube start
    ```

1. Initialiser le **Helm** composant serveur (*tiller*) dans votre cluster

    ``` shell
    helm init
    ```

1. Ajoutez le référentiel de IoT Edge et mettre à jour de l’installation de helm

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Créer un IoT Hub](../iot-hub/iot-hub-create-through-portal.md), [inscrire un appareil IoT Edge](how-to-register-device-portal.md)et notez sa chaîne de connexion.

1. Installer iotedged et agent IoT Edge dans votre cluster

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. Ouvrez le tableau de bord Kubernetes dans le navigateur

    ```shell
    minikube dashboard
    ```

    Sous les espaces de noms de cluster, vous verrez un pour l’appareil IoT Edge conformément à la convention *msiot -\<iothub-name >-\<edgedevice-name >*. Les pods d’agent et iotedged IoT Edge doivent être en cours d’exécution dans cet espace de noms.

1. Ajouter un module de capteur de température simulée à l’aide de la procédure décrite dans la [déployer un module](quickstart-linux.md#deploy-a-module) section du démarrage rapide. Gestion des modules IoT Edge est effectuée à partir du portail IoT Hub comme tout autre appareil IoT Edge. Apporter des modifications locales à la configuration du module via Outils Kubernetes n’est pas recommandée car ils peuvent être remplacées.

1. En quelques secondes, l’actualisation le **Pods** page sous l’espace de noms de périphérique de périmètre dans le tableau de bord répertorie le hub IoT Edge et pods de capteur simulé comme en cours d’exécution avec le hub IoT Edge pod réception des données dans IoT Hub.

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer toutes les ressources créées par le déploiement de périphérie, utilisez la commande suivante avec le nom utilisé à l’étape 5 de la section précédente.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Étapes suivantes

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Déployer comme une passerelle edge hautement disponible 

L’appareil edge dans un cluster Kubernetes peut être utilisé comme passerelle IoT pour les appareils en aval. Il peut être configuré pour qu’elles soient résistantes aux défaillances de nœud et offrir ainsi une haute disponibilité pour les déploiements edge. Consultez ce [procédure pas à pas détaillées](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) à utiliser IoT Edge dans ce scénario.