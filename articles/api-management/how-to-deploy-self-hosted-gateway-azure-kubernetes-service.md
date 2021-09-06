---
title: Déployer une passerelle auto-hébergée sur Azure Kubernetes Service | Microsoft Docs
description: Découvrez comment déployer la passerelle auto-hébergée de la Gestion des API Azure sur Azure Kubernetes Service.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 06/11/2021
ms.author: apimpm
ms.openlocfilehash: c43f31be807d6a649cdd750ee15841a0ecbd7631
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300756"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Déployer sur Azure Kubernetes Service

Cet article explique comment déployer la passerelle auto-hébergée de la Gestion des API Azure sur [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/). Pour déployer la passerelle auto-hébergée sur un cluster Kubernetes, consultez [cet article de guide pratique](how-to-deploy-self-hosted-gateway-kubernetes.md).

> [!NOTE]
> Vous pouvez également déployer une passerelle auto-hébergée sur un [cluster Kubernetes avec Azure Arc](how-to-deploy-self-hosted-gateway-azure-arc.md) en tant qu’[extension de cluster](../azure-arc/kubernetes/extensions.md).

## <a name="prerequisites"></a>Prérequis

- [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md)
- [Créer un cluster Azure Kubernetes](../aks/kubernetes-walkthrough-portal.md)
- [Provisionnez une ressource de passerelle dans votre instance de gestion des API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>Déployer une passerelle auto-hébergée sur AKS

1. Sélectionnez **Gateways** (Passerelles) sous **Deployment and infrastructure** (Déploiement et infrastructure).
2. Sélectionnez la ressource de passerelle auto-hébergée que vous souhaitez déployer.
3. Sélectionnez **Déploiement**.
4. Un nouveau jeton dans la zone de texte **Jeton** a été généré automatiquement pour vous avec les valeurs par défaut de **Expiration** et **Clé secrète**. Ajustez l’un ou l’autre, ou les deux, si vous le souhaitez, puis sélectionnez **Générer** pour créer un autre jeton.
5. Assurez-vous que **Kubernetes** est sélectionné sous **Scripts de déploiement**.
6. Sélectionnez le lien du fichier **<nom-passerelle>.yml** en regard de **Déploiement** pour télécharger le fichier.
7. Ajustez `config.service.endpoint`, les mappages de port et le nom de conteneur dans le fichier .yml, si nécessaire.
8. Selon votre scénario, vous pouvez avoir besoin de changer le [type de service](../aks/concepts-network.md#services). 
    * La valeur par défaut est `LoadBalancer`, qui correspond à l’équilibreur de charge externe. 
    * Vous pouvez utiliser l’[équilibreur de charge interne](../aks/internal-lb.md) pour restreindre l’accès à la passerelle auto-hébergée aux utilisateurs internes uniquement. 
    * L’exemple ci-dessous utilise `NodePort`.
1. Sélectionnez l’icône **Copier** située à l’extrémité droite de la zone de texte **Déployer** pour enregistrer la commande `kubectl` dans le Presse-papiers.
1. Collez la commande dans la fenêtre (ou la commande) du terminal. La commande s’attend à ce que le fichier d’environnement téléchargé soit présent dans le répertoire actif.
    ```console
        kubectl apply -f <gateway-name>.yaml
    ```
1. Exécutez la commande. La commande indique à votre cluster AKS d’effectuer les opérations suivantes :
    * Exécuter le conteneur à l’aide de l’image de passerelle auto-hébergée téléchargée à partir du Microsoft Container Registry. 
    * Configurer le conteneur pour exposer les ports HTTP (8080) et HTTP (443).
1. Exécutez la commande ci-dessous pour vérifier que le pod de la passerelle est en cours d’exécution. Le nom de votre pod sera différent.
    ```console
    kubectl get pods
    NAME                                   READY     STATUS    RESTARTS   AGE
    contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
    ```
1. Exécutez la commande ci-dessous pour vérifier que le service de passerelle est en cours d’exécution. Le nom de votre service et les adresses IP seront différents.
    ```console
    kubectl get services
    NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
    ```
1. Retournez au portail Azure et vérifiez que le nœud de la passerelle que vous avez déployée signale l’état d’intégrité.

> [!TIP]
> Utilisez la commande <code>kubectl logs <gateway-pod-name></code> pour afficher un instantané du journal de la passerelle auto-hébergée.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la passerelle auto-hébergée, consultez [Vue d’ensemble de la passerelle auto-hébergée Gestion des API](self-hosted-gateway-overview.md).
* Découvrez [comment déployer une passerelle auto-hébergée Gestion des API sur des clusters Kubernetes avec Azure Arc](how-to-deploy-self-hosted-gateway-azure-arc.md).
* En savoir plus sur [Azure Kubernetes Service](../aks/intro-kubernetes.md).
* Découvrez comment [configurer et conserver les journaux dans le cloud](how-to-configure-cloud-metrics-logs.md).
* Découvrez comment [configurer et conserver les journaux localement](how-to-configure-local-metrics-logs.md).
