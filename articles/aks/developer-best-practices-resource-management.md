---
title: Bonnes pratiques de gestion des ressources
titleSuffix: Azure Kubernetes Service
description: Découvrir les bonnes pratiques relatives à la gestion des ressources dans Azure Kubernetes Services (AKS) pour le développeur d’applications
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 2cd2bab05346f66b933512e677f1d38f4514796c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105270"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Bonnes pratiques relatives à la gestion des ressources dans Azure Kubernetes Services (AKS) pour le développeur d’applications

Quand vous développez et exécutez des applications dans Azure Kubernetes Service (AKS), vous devez tenir compte de quelques points clés. La façon dont vous gérez vos déploiements d’application peut impacter négativement l’expérience des utilisateurs finaux. Pour réussir, gardez à l’esprit ces quelques meilleures pratiques relatives au développement et à l’exécution d’applications dans AKS.

Cet article se concentre sur l’exécution de votre cluster et de vos charges de travail du point de vue d’un développeur d’applications. Pour accéder aux bonnes pratiques en matière d’administration, consultez les [bonnes pratiques de l’opérateur de cluster relatives à l’isolation et à la gestion de ressources dans Azure Kubernetes Service (AKS)][operator-best-practices-isolation]. Dans cet article, vous apprenez :

> [!div class="checklist"]
> * Les demandes et les limites des ressources de pod.
> * Les moyens de développer et de déployer des applications avec Bridge to Kubernetes et Visual Studio Code.
> * L’utilisation de l’outil `kube-advisor` pour rechercher les problèmes liés aux déploiements.

## <a name="define-pod-resource-requests-and-limits"></a>À définir les demandes et limites de ressources de pod

> **Conseils sur les bonnes pratiques**
> 
> Définissez des demandes et des limites de pods sur tous les pods dans vos manifestes YAML. Si le cluster AKS utilise des *quotas de ressources* et que vous ne définissez pas ces valeurs, votre déploiement peut être rejeté.

Utilisez les demandes et les limites de pods pour gérer les ressources de calcul au sein d’un cluster AKS. Les demandes et les limites de pods informent le planificateur Kubernetes des ressources de calcul à attribuer à un pod.

### <a name="pod-cpumemory-requests"></a>Demandes d’UC/de mémoire de pods
Les *demandes de pods* définissent une quantité fixe d’UC et de mémoire dont le pod a besoin régulièrement.

Dans les spécifications de votre pod, il est **recommandé et essentiel** de définir ces requêtes et limites en fonction des informations ci-dessus. Si vous n’incluez pas ces valeurs, le planificateur Kubernetes ne peut pas prendre en compte les ressources dont vos applications ont besoin pour faciliter la planification des décisions.

Surveillez les performances de votre application pour ajuster les demandes de pods. 
* Si vous sous-estimez les demandes de pods, votre application peut voir ses performances se dégrader en raison de la surplanification d’un nœud. 
* Si les demandes sont surestimées, votre application peut avoir des difficultés à être planifiées.

### <a name="pod-cpumemory-limits"></a>Limites d’UC/de mémoire de pods** 
Les *limites de pods* définissent la quantité maximale d’UC et de mémoire qu’un pod peut utiliser. 

* Les *limites de mémoire* définissent quels pods doivent être tués lorsque les nœuds sont instables en raison de ressources insuffisantes. Sans les limites appropriées, les pods seront tués jusqu’à ce que la pression sur les ressources diminue. 
* Bien qu’un pod puisse dépasser périodiquement la *limite d’UC*, le pod ne sera pas tué pour l’avoir dépassée. 

Les limites de pods définissent le moment où un pod perd le contrôle de la consommation des ressources. Lorsque la limite est dépassée, le pod est destiné à être tué. Ce comportement maintient l’intégrité du nœud et réduit l’impact sur les pods partageant le nœud. Si vous ne définissez pas de limite de pod, elle est définie par défaut sur la valeur disponible la plus élevée sur un nœud donné.

Évitez de définir une limite de pods supérieure à ce que vos nœuds peuvent prendre en charge. Chaque nœud AKS réserve une quantité fixe d’UC et de mémoire pour les principaux composants de Kubernetes. Votre application peut tenter de consommer trop de ressources sur le nœud pour que les autres pods s’exécutent correctement.

Supervisez les performances de votre application à différents moments de la journée ou de la semaine. Déterminez les pics de demande et alignez les limites de pods sur les ressources requises pour répondre au maximum de besoins.

> [!IMPORTANT]
>
> Dans les spécifications de votre pod, définissez ces demandes et ces limites en fonction des informations ci-dessus. Le fait de ne pas inclure ces valeurs empêche le planificateur Kubernetes de comptabiliser les ressources dont vos applications ont besoin pour faciliter la planification des décisions.

Si le planificateur place un pod sur un nœud dont les ressources sont insuffisantes, cela entraîne la dégradation des performances de l’application. Les administrateurs de clusters **doivent** définir des *quotas de ressources* sur un espace de noms qui vous oblige à définir des demandes et des limites de ressources. Pour plus d’informations, consultez [Quotas de ressources sur des clusters AKS][resource-quotas].

Quand vous définissez une demande ou une limite d’UC, la valeur est mesurée en unités d’UC. 
* Une UC de *1.0* équivaut à un cœur de processeur virtuel sous-jacent sur le nœud. 
    * La même mesure est utilisée pour les GPU.
* Vous pouvez définir des fractions mesurées en millicœurs. Par exemple, *100 m* correspond à *0,1* pour un cœur de processeur virtuel sous-jacent.

Dans l’exemple de base suivant pour un pod NGINX, le pod demande *100m* de temps UC et *128Mi* de mémoire. Les limites de ressources pour le pod sont définies avec *250m* comme UC et *256Mi* comme mémoire :

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Pour plus d’informations sur les mesures et affectations de ressources, consultez [Gestion des ressources de calcul pour les conteneurs][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Développer et déboguer des applications sur un cluster AKS

> **Conseils sur les bonnes pratiques** 
>
> Les équipes de développement doivent déployer et déboguer leurs applications sur un cluster AKS à l’aide de Bridge to Kubernetes.

Avec Bridge to Kubernetes, vous pouvez développer, déboguer et tester les applications directement sur un cluster AKS. Les développeurs d’une même équipe collaborent pour générer et tester l’application tout au long de son cycle de vie. Vous pouvez continuer à utiliser les outils existants tels que Visual Studio ou Visual Studio Code avec l’extension Bridge to Kubernetes. 

L’utilisation du processus de développement et de test intégré à Bridge to Kubernetes évite de recourir à des environnements de test locaux tels que [minikube][minikube]. Au lieu de cela, vous développez et testez sur un cluster AKS, même les clusters sécurisés et isolés. 

> [!NOTE]
> Bridge to Kubernetes est destiné à être utilisé avec les applications qui s’exécutent sur les nœuds et pods Linux.

## <a name="use-the-visual-studio-code-vs-code-extension-for-kubernetes"></a>Utiliser l’extension Visual Studio Code (VS Code) pour Kubernetes

> **Conseils sur les bonnes pratiques** 
>
> Installez et utilisez l’extension VS Code pour Kubernetes quand vous écrivez des manifestes YAML. Vous pouvez également utiliser l’extension en tant que solution de déploiement intégrée, ce qui peut être utile pour les propriétaires d’applications qui interagissent rarement avec le cluster AKS.

L’[extension Visual Studio Code pour Kubernetes][vscode-kubernetes] vous permet de développer et déployer des applications sur AKS. L’extension fournit :
* IntelliSense pour les ressources Kubernetes, les charts Helm et les modèles. 
* Des capacités de navigation, de déploiement et de modification pour les ressources Kubernetes à partir de VS Code. 
* Une vérification IntelliSense pour les demandes ou limites de ressources définies dans les spécifications de pod :

    ![Avertissement de l’extension VS Code pour Kubernetes concernant des limites de mémoire manquantes](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Rechercher régulièrement les problèmes liés à l’application avec kube-advisor

> **Conseils sur les bonnes pratiques** 
> 
> Exécutez régulièrement la dernière version de l’outil open source `kube-advisor` pour détecter les problèmes dans votre cluster. Exécutez `kube-advisor` avant d’appliquer des quotas de ressources sur un cluster AKS existant pour rechercher les pods dont les demandes et limites de ressources ne sont pas définies.

L’outil [kube-advisor][kube-advisor] est un projet open source AKS associé qui analyse un cluster Kubernetes et signale les problèmes identifiés. Une vérification utile consiste à identifier les pods dépourvus de demandes et de limites de ressources.

Bien que l’outil `kube-advisor` puisse signaler les demandes et limites de ressources manquantes dans PodSpecs pour les applications Windows et Linux, `kube-advisor` doit être planifié sur un pod Linux. Utilisez un [sélecteur de nœuds][k8s-node-selector] dans la configuration du pod pour planifier l’exécution d’un pod sur un pool de nœuds avec un système d’exploitation spécifique.

Dans un cluster AKS qui héberge de nombreuses équipes de développement et applications, il est plus facile de suivre les pods à l’aide de demandes et de limites de ressources. Une bonne pratique consiste à exécuter régulièrement `kube-advisor` sur vos clusters AKS.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons traité de la façon d’exécuter votre cluster et vos charges de travail du point de vue d’un opérateur de cluster. Pour accéder aux bonnes pratiques en matière d’administration, consultez les [bonnes pratiques de l’opérateur de cluster relatives à l’isolation et à la gestion de ressources dans Azure Kubernetes Service (AKS)][operator-best-practices-isolation].

Pour implémenter quelques-unes de ces bonnes pratiques, consultez les articles suivants :

* [Développer avec Bridge to Kubernetes][btk]
* [Rechercher des problèmes avec kube-advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
