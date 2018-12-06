---
title: Bonnes pratiques de développement - Gestion des ressources dans Azure Kubernetes Services (AKS)
description: Découvrir les bonnes pratiques relatives à la gestion des ressources dans Azure Kubernetes Services (AKS) pour le développeur d’applications
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 50f55ca3b05a6902841f1b1268cfbde742a9d589
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52429584"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Bonnes pratiques relatives à la gestion des ressources dans Azure Kubernetes Services (AKS) pour le développeur d’applications

Quand vous développez et exécutez des applications dans Azure Kubernetes Service (AKS), vous devez tenir compte de quelques points clés. La façon dont vous gérez vos déploiements d’application peut impacter négativement l’expérience des utilisateurs finaux. Pour vous aider à réussir, gardez à l’esprit ces quelques bonnes pratiques relatives au développement et à l’exécution d’applications dans AKS.

Cet article traite des bonnes pratiques relatives à l’exécution d’un cluster et de charges de travail du point de vue d’un développeur d’applications. Pour accéder aux bonnes pratiques en matière d’administration, consultez les [bonnes pratiques de l’opérateur de cluster relatives à l’isolation et à la gestion de ressources dans Azure Kubernetes Service (AKS)][operator-best-practices-isolation]. Dans cet article, vous apprenez :

> [!div class="checklist"]
> * Ce que sont les demandes et limites de ressources de pod
> * Comment développer et déployer des applications avec Dev Spaces et Visual Studio Code
> * Comment utiliser l’outil `kube-advisor` pour rechercher les problèmes liés aux déploiements

## <a name="define-pod-resource-requests-and-limits"></a>À définir les demandes et limites de ressources de pod

**Guide sur les bonnes pratiques** : définissez des demandes et limites de pod sur tous les pods dans vos manifestes YAML. Si le cluster AKS utilise des *quotas de ressources*, votre déploiement peut être rejeté si vous ne définissez pas ces valeurs.

L’un des principaux moyens de gérer les ressources de calcul au sein d’un cluster AKS consiste à utiliser des demandes et des limites de pod. Ces demandes et limites indiquent au planificateur Kubernetes les ressources de calcul à affecter à un pod.

* Les **demandes de pod** définissent une quantité fixe d’UC et de mémoire dont le pod a besoin. Ces demandes doivent correspondre à la quantité de ressources de calcul nécessaire au pod pour fournir un niveau de performances acceptable.
    * Quand le planificateur Kubernetes tente de placer un pod sur un nœud, les demandes de pod permettent de déterminer le nœud ayant suffisamment de ressources disponibles.
    * Supervisez les performances de votre application pour ajuster ces demandes et veiller à ne pas définir une quantité de ressources inférieure à celle nécessaire pour maintenir un niveau de performances acceptable.
* Les **limites de pod** représentent la quantité maximale d’UC et de mémoire qu’un pod peut utiliser. Ces limites empêchent qu’un ou deux pods dont le contrôle a été perdu ne consomment pas trop d’UC et de mémoire du nœud. Ce scénario réduirait les performances du nœud et des autres pods qui s’exécutent sur ce nœud.
    * Ne définissez pas une limite de pod trop élevée ne pouvant pas être prise en charge par vos nœuds. Chaque nœud AKS réserve une quantité fixe d’UC et de mémoire pour les principaux composants de Kubernetes. Votre application peut tenter de consommer trop de ressources sur le nœud pour que les autres pods s’exécutent correctement.
    * Là encore, supervisez les performances de votre application à différents moments de la journée ou de la semaine. Déterminer les pics de demande et aligner les limites de pod avec les ressources requises pour répondre aux besoins de l’application.

Il est recommandé de définir ces demandes et limites dans les spécifications de pod. Si vous n’incluez pas ces valeurs, le planificateur Kubernetes ne comprend pas les ressources qui sont nécessaires. Le planificateur peut planifier le pod sur un nœud qui ne dispose pas de ressources suffisantes pour fournir un niveau de performance acceptable à votre application. L’administrateur de cluster peut définir des *quotas des ressources* sur un espace de noms qui vous oblige à définir des demandes et des limites de ressources. Pour plus d’informations, consultez [Quotas de ressources sur des clusters AKS][resource-quotas].

Quand vous définissez une demande ou une limite d’UC, la valeur est mesurée en unités d’UC. Une UC de *1.0* équivaut à un cœur de processeur virtuel sous-jacent sur le nœud. La même mesure est utilisée pour les GPU. Vous pouvez également définir une demande ou une limite fractionnaire, en général en millicpu. Par exemple, *100m* correspond à *0,1* d’un cœur de processeur virtuel sous-jacent.

Dans l’exemple de base suivant pour un pod NGINX, le pod demande *100m* de temps UC et *128Mi* de mémoire. Les limites de ressources pour le pod sont définies avec *250m* comme UC et *256Mi* comme mémoire :

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
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

**Guide de bonne pratique** : les équipes de développement doivent déployer et déboguer leurs applications sur un cluster AKS à l’aide de Dev Spaces. Ce modèle de développement garantit que les besoins en termes de réseau, de stockage ou de contrôles d’accès en fonction du rôle sont implémentés avant le déploiement de l’application en production.

Avec Azure Dev Spaces, vous développez, déboguez et testez les applications directement sur un cluster AKS. Les développeurs d’une équipe travaillent ensemble pour générer et tester l’application tout au long de son cycle de vie. Vous pouvez continuer à utiliser des outils existants tels que Visual Studio ou Visual Studio Code. Une extension installée pour Dev Spaces permet d’exécuter et de déboguer l’application dans un cluster AKS :

![Déboguer des applications dans un cluster AKS avec Dev Spaces](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Ce processus de développement et de test intégré à Dev Spaces évite de recourir à des environnements de test locaux tels que [minikube][minikube]. Au lieu de cela, vous développez et testez votre application sur un cluster AKS. Ce cluster peut être sécurisé et isolé, comme indiqué dans la section précédente sur l’utilisation d’espaces de noms pour isoler logiquement un cluster. Votre développement ayant été entièrement réalisé sur un cluster AKS réel, vous pouvez déployer en toute confiance vos applications quand celles-ci sont prêtes à passer en production.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Utiliser l’extension Visual Studio Code pour Kubernetes

**Guide sur les bonnes pratiques** : installez et utilisez l’extension VS Code pour Kubernetes quand vous écrivez des manifestes YAML. Vous pouvez également utiliser l’extension en tant que solution de déploiement intégrée, ce qui peut être utile pour les propriétaires d’applications qui interagissent rarement avec le cluster AKS.

L’[extension Visual Studio Code pour Kubernetes] [ vscode-kubernetes] vous permet de développer et déployer des applications sur AKS. L’extension fournit les informations IntelliSense pour les ressources Kubernetes ainsi que les graphiques et modèles Helm. Vous pouvez également parcourir, déployer et modifier des ressources Kubernetes à partir de VS Code. L’extension fournit une vérification IntelliSense pour les demandes ou limites de ressources définies dans les spécifications de pod :

![Avertissement de l’extension VS Code pour Kubernetes concernant des limites de mémoire manquantes](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Rechercher régulièrement les problèmes liés à l’application avec kube-advisor

**Guide de bonne pratique** : exécutez régulièrement la dernière version de `kube-advisor` pour détecter les problèmes dans votre cluster. Si vous appliquez des quotas de ressources sur un cluster AKS existant, exécutez d’abord `kube-advisor` pour rechercher les pods dont les demandes et limites de ressources ne sont pas définies.

L’outil [kube-advisor][kube-advisor] analyse un cluster Kubernetes et signale les problèmes trouvés. Une vérification utile consiste à identifier les pods dépourvus de demandes et de limites de ressources.

Dans un cluster AKS qui héberge plusieurs applications et équipes de développement, il peut être difficile de suivre les pods sans ces demandes et limites de ressources. Une bonne pratique consiste à exécuter régulièrement `kube-advisor` sur vos clusters AKS.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons traité des bonnes pratiques relatives à l’exécution d’un cluster et de charges de travail du point de vue d’un opérateur de cluster. Pour accéder aux bonnes pratiques en matière d’administration, consultez les [bonnes pratiques de l’opérateur de cluster relatives à l’isolation et à la gestion de ressources dans Azure Kubernetes Service (AKS)][operator-best-practices-isolation].

Pour implémenter quelques-unes de ces bonnes pratiques, consultez les articles suivants :

* [Développer avec Dev Spaces][dev-spaces]
* [Rechercher des problèmes avec kube-advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/get-started-netcore.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
