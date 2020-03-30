---
title: Déployer une passerelle de gestion des API Azure auto-hébergée sur Kubernetes | Microsoft Docs
description: Découvrez comment déployer une passerelle de gestion des API Azure auto-hébergée sur Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73510590"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Déployer une passerelle de gestion des API Azure auto-hébergée sur Kubernetes

Cet article présente les étapes du déploiement de la passerelle de gestion des API Azure auto-hébergée dans un cluster Kubernetes.

> [!NOTE]
> La fonctionnalité de passerelle auto-hébergée est en version préliminaire. Pendant la version préliminaire, la passerelle auto-hébergée est disponible uniquement pour les niveaux Développeur et Premium, sans frais supplémentaires. Le niveau Développeur est limité à un seul déploiement de passerelle auto-hébergée.


## <a name="prerequisites"></a>Conditions préalables requises

- Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
- Créez un cluster Kubernetes. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) est une bonne option pour ce qui concerne le développement et l’évaluation. Pour les charges de travail de production, vous pouvez utiliser [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) ou un cluster Kubernetes dans un cloud étranger.
- [Provisionnez une ressource de passerelle dans votre instance de gestion des API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-gateway-to-kubernetes"></a>Déployer la passerelle sur Kubernetes

1. Sélectionnez **Passerelles** sous **Paramètres**.
2. Sélectionnez la ressource de passerelle que vous souhaitez déployer.
3. Sélectionnez **Déploiement**.
4. Notez qu’un nouveau jeton dans la zone de texte **Jeton** a été généré automatiquement pour vous avec les valeurs par défaut de **Expiration** et **Clé secrète**. Ajustez l’un ou l’autre, ou les deux, si vous le souhaitez, puis sélectionnez **Générer** pour créer un autre jeton.
5. Assurez-vous que **Kubernetes** est sélectionné sous **Scripts de déploiement**.
6. Sélectionnez le lien du fichier **<nom-passerelle>.yml** en regard de **Déploiement** pour télécharger le fichier.
7. Ajustez les mappages de port et le nom de conteneur dans le fichier yml, si nécessaire.
8. Sélectionnez l’icône **Copier** située à l’extrémité droite de la zone de texte **Déployer** pour enregistrer la commande `kubectl` dans le Presse-papiers. 
9. Collez la commande dans la fenêtre (ou la commande) du terminal. Notez que la commande s’attend à ce que le fichier d’environnement téléchargé soit présent dans le répertoire actif.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. Exécutez la commande. La commande indique à votre cluster Kubernetes d’exécuter le conteneur à l’aide de l’image de la passerelle auto-hébergée qui a été téléchargée à partir de Microsoft Container Registry, et de configurer le conteneur pour qu’il expose les ports HTTP (8080) et HTTPS (443).
11. Exécutez la commande ci-dessous pour vérifier que le pod de la passerelle est en cours d’exécution. Notez que le nom de votre pod va être différent. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. Exécutez la commande ci-dessous pour vérifier que le service de passerelle est en cours d’exécution. Notez que le nom de votre service va être différent. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Revenez au portail Azure et vérifiez que le nœud de la passerelle que vous venez de déployer signale l’état d’intégrité.

![état de la passerelle](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> Utilisez la commande <code>kubectl logs <gateway-pod-name></code> pour afficher un instantané du journal de la passerelle auto-hébergée.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la passerelle auto-hébergée, consultez [Vue d’ensemble de la passerelle auto-hébergée Gestion des API](self-hosted-gateway-overview.md)
* En savoir plus sur [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)


