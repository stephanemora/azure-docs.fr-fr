---
title: Vue d’ensemble d’un cluster Kubernetes sur un appareil Microsoft Azure Stack Edge | Microsoft Docs
description: Décrit comment Kubernetes est implémenté sur votre appareil Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: be7e01644d7d1f232c50b1efbadf100671a262fe
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268106"
---
# <a name="kubernetes-on-your-azure-stack-edge-gpu-device"></a>Kubernetes sur votre appareil Azure Stack Edge avec GPU

Kubernetes est une plateforme open source populaire qui permet d’orchestrer des applications en conteneur. Cet article fournit une vue d’ensemble de Kubernetes, puis décrit le fonctionnement de Kubernetes sur votre appareil Azure Stack Edge. 

## <a name="about-kubernetes"></a>À propos de Kubernetes 

Kubernetes fournit une plateforme simple et fiable pour gérer les applications basées sur un conteneur et leurs composants de stockage et de mise en réseau associés. Kubernetes vous permet de créer, livrer et mettre à l’échelle rapidement des applications en conteneur.

En tant que plateforme ouverte, Kubernetes vous permet de créer des applications avec vos langage de programmation, système d’exploitation, bibliothèques ou bus de messagerie préférés. Pour planifier et déployer des mises en production, Kubernetes peut s’intégrer avec des outils d’intégration et de livraison continues existants.

Pour plus d’informations, consultez [fonctionnement de Kubernetes](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s).

## <a name="kubernetes-on-azure-stack-edge"></a>Kubernetes sur Azure Stack Edge

Sur votre appareil Azure Stack Edge, vous pouvez créer un cluster Kubernetes en configurant le calcul. Lorsque le rôle de calcul est configuré, le cluster Kubernetes, y compris les nœuds principal et Worker, sont déployés et configurés pour vous. Ce cluster est ensuite utilisé pour le déploiement de la charge de travail via `kubectl`, IoT Edge ou Azure Arc.

L’appareil Azure Stack Edge est disponible en tant que configuration à un nœud qui constitue le cluster d’infrastructure. Le cluster Kubernetes est séparé du cluster d’infrastructure, et déployé par-dessus le cluster d’infrastructure. Le cluster d’infrastructure fournit le stockage persistant pour votre appareil Azure Stack Edge, tandis que le cluster Kubernetes est uniquement responsable de l’orchestration de l’application. 

Dans ce cas, le cluster Kubernetes a un nœud principal et un nœud Worker. Les nœuds Kubernetes dans un cluster sont des machines virtuelles qui exécutent vos applications et flux de travail cloud. 

Le nœud principal Kubernetes est chargé de maintenir l’état souhaité pour votre cluster. Le nœud principal contrôle également le nœud Worker qui, à son tour, exécute les applications en conteneur. 

Le diagramme suivant illustre l’implémentation de Kubernetes sur un appareil Azure Stack Edge à 1 nœud. L’appareil à 1 nœud n’est pas hautement disponible et, en cas de défaillance du nœud unique, l’appareil s’arrête. Le cluster Kubernetes s’arrête également.

![Architecture Kubernetes pour appareil Azure Stack Edge à 1 nœud](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

Pour plus d’informations sur l’architecture de cluster Kubernetes, accédez à [Concepts de base de Kubernetes](https://kubernetes.io/docs/concepts/architecture/).


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>Approvisionnement du volume de stockage

Pour prendre en charge les charges de travail d’application, vous pouvez monter des volumes de stockage pour les données persistantes sur vos partages d’appareils Azure Stack Edge. Les volumes statiques et dynamiques peuvent être utilisés. 

Pour plus d’informations, consultez les options d’approvisionnement de stockage pour les applications dans [Stockage Kubernetes pour votre appareil Azure Stack Edge](azure-stack-edge-gpu-kubernetes-storage.md).

## <a name="networking"></a>Mise en réseau

La mise en réseau Kubernetes vous permet de configurer la communication au sein de votre réseau Kubernetes, y compris la mise en réseau entre conteneurs, la mise en réseau entre pods, la mise en réseau entre pod et service, et la mise en réseau entre Internet et service. Pour plus d’informations, consultez le modèle de mise en réseau dans [Mise en réseau Kubernetes pour votre appareil Azure Stack Edge](azure-stack-edge-gpu-kubernetes-networking.md).

## <a name="updates"></a>Mises à jour

À mesure que de nouvelles versions de Kubernetes deviennent disponibles, votre cluster peut être mis à niveau à l’aide des mises à jour standard disponibles pour votre appareil Azure Stack Edge. Pour plus d’informations sur la procédure de mise à niveau, consultez [Appliquer des mises à jour pour votre Azure Stack Edge](azure-stack-edge-gpu-install-update.md).

## <a name="access-monitoring"></a>Accès, surveillance

Le cluster Kubernetes sur votre appareil Azure Stack Edge autorise le contrôle d’accès en fonction du rôle (RBAC). Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle pour le cluster Kubernetes sur votre appareil Azure Stack Edge](azure-stack-edge-gpu-kubernetes-rbac.md).

Vous pouvez également surveiller l’intégrité de votre cluster et de vos ressources via le tableau de bord Kubernetes. Des journaux de conteneur sont également disponibles. Pour plus d’informations, consultez [Utiliser le tableau de bord Kubernetes pour surveiller l’intégrité des clusters Kubernetes sur votre appareil Azure Stack Edge](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).

Azure Monitor est également disponible en tant que module complémentaire pour collecter des données d’intégrité des conteneurs, nœuds et contrôleurs. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Monitor](../azure-monitor/overview.md).

<!--## Private container registry

Kubernetes on Azure Stack Edge device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>Gestion des applications

Une fois qu’un cluster Kubernetes a été créé sur votre appareil Azure Stack Edge, vous pouvez gérer les applications déployées sur ce cluster via n’importe laquelle des méthodes suivantes :

- Accès natif via `kubectl`
- IoT Edge 
- Azure Arc

Les méthodes sont expliquées dans les sections suivantes.


### <a name="kubernetes-and-kubectl"></a>Kubernetes et kubectl

Une fois le cluster Kubernetes déployé, vous pouvez gérer les applications déployées dessus localement à partir d’un ordinateur client. Vous utilisez un outil natif tel que *kubectl* via la ligne de commande pour interagir avec les applications. 

Pour plus d’informations sur le déploiement d’un cluster Kubernetes, consultez [Déployer un cluster Kubernetes sur votre appareil Azure Stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md). Pour plus d’informations sur la gestion, accédez à [Utiliser kubectl pour gérer un cluster Kubernetes sur votre appareil Azure Stack Edge](azure-stack-edge-gpu-create-kubernetes-cluster.md).


### <a name="kubernetes-and-iot-edge"></a>Kubernetes et IoT Edge

Kubernetes peut également être intégré avec des charges de travail IoT Edge sur un appareil Azure Stack Edge, où Kubernetes fournit la mise à l’échelle et l’écosystème, et où IoT fournit l’écosystème orienté IoT. La couche Kubernetes est utilisée comme couche d’infrastructure pour déployer les charges de travail d’Azure IoT Edge. La durée de vie du module et l’équilibrage de la charge réseau sont gérés par Kubernetes, tandis que la plateforme d’application de périphérie est gérée par IoT Edge.

Pour plus d’informations sur le déploiement d’applications sur votre cluster Kubernetes via IoT Edge, accédez à : 

- [Exposer des applications sans état sur un appareil Azure Stack Edge via IoT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).


### <a name="kubernetes-and-azure-arc"></a>Kubernetes et Azure Arc

Azure Arc est un outil de gestion hybride qui vous permet de déployer des applications sur vos clusters Kubernetes. Azure Arc vous permet également d’utiliser Azure Monitor pour conteneurs afin d’afficher et de surveiller vos clusters. Pour plus d’informations, consultez [Qu’est-ce que Kubernetes avec Azure Arc ?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview). Pour plus d’informations sur la tarification d’Azure Arc, consultez [Tarification d’Azure Arc](https://azure.microsoft.com/services/azure-arc/#pricing).


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le stockage Kubernetes sur [appareil Azure Stack Edge](azure-stack-edge-gpu-kubernetes-storage.md).
- Comprendre le modèle de mise en réseau Kubernetes sur un [appareil Azure Stack Edge](azure-stack-edge-gpu-kubernetes-networking.md).
- Déployez [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) sur le portail Azure.
