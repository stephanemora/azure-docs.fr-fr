---
title: Programme de validation de Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Décrit le programme de validation d’Arc pour les distributions Kubernetes
keywords: Kubernetes, Arc, Azure, K8s, validation
ms.openlocfilehash: 0d7756f407a3a176c8913d99bdb45a5026300569
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101689213"
---
# <a name="azure-arc-validation-program"></a>Programme de validation d’Azure Arc

Kubernetes avec Azure Arc fonctionne pour tous les clusters Kubernetes certifiés CNCF (Cloud Native Computing Foundation). L’équipe d’Azure Arc a également collaboré avec des fournisseurs clés du secteur pour valider Kubernetes avec Azure Arc par rapport aux distributions Kubernetes de ces fournisseurs. Les prochaines versions majeures et mineures des distributions Kubernetes publiées par ces fournisseurs seront validées au niveau de leur compatibilité par rapport à Kubernetes avec Azure Arc.

## <a name="validated-distributions"></a>Distributions validées

Les fournisseurs suivants de distributions et d’infrastructures Kubernetes (via Microsoft) ont réussi les tests de conformité à Kubernetes avec Azure Arc :

| Fournisseur de distributions et d’infrastructures | Version |
| ---------------------------------------- | ------- |
| Fournisseur d’API de cluster sur Azure            | Version release : [0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12). Version de Kubernetes : [1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| AKS sur Azure Stack HCI                   | Version release : [Mise à jour de décembre 2020](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012). Version de Kubernetes : [1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

Les fournisseurs suivants et leurs distributions Kubernetes correspondantes ont réussi les tests de conformité à Kubernetes avec Azure Arc :

| Nom du fournisseur | Nom de la distribution | Version |
| ------------ | ----------------- | ------- |
| Red Hat       | [OpenShift Container Platform](https://www.openshift.com/products/container-platform) | [4.5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html), [4.6](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html), [4.7](https://docs.openshift.com/container-platform/4.7/release_notes/ocp-4-7-release-notes.html) |
| VMware       | [Tanzu Kubernetes Grid](https://tanzu.vmware.com/kubernetes-grid) | Version de Kubernetes : v1.17.5 |
| Canonical    | [Charmed Kubernetes](https://ubuntu.com/kubernetes) | [1.19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| SUSE Rancher      | [Rancher Kubernetes Engine](https://rancher.com/products/rke/) | Version de l’interface CLI RKE : [v1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4). Versions de Kubernetes : [1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6)), [1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14)), [1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16))  |
| Nutanix      | [Karbon](https://www.nutanix.com/products/karbon)    | Version 2.2.1 |

L’équipe d’Azure Arc a également exécuté les tests de conformité et validé les scénarios de Kubernetes avec Azure Arc pour les fournisseurs de cloud public suivants :

| Nom du fournisseur de cloud public | Nom de la distribution | Version |
| -------------------------- | ----------------- | ------- |
| Amazon Web Services        | Elastic Kubernetes Service (EKS) | v1.18.9  |
| Google Cloud Platform      | Google Kubernetes Engine (GKE) | v1.17.15 |

## <a name="scenarios-validated"></a>Scénarios validés

Les tests de conformité exécutés dans le cadre de la validation de Kubernetes avec Azure Arc couvrent les scénarios suivants :

1. Connecter des clusters Kubernetes à Azure Arc : 
    * Déployez un chart Helm d’agent Kubernetes avec Azure Arc sur le cluster.
    * Configurez le certificat relatif aux identités managées pour les ressources Azure sur le cluster.
    * Les agents envoient les métadonnées de cluster à Azure.

2. Configuration : 
    * Créez une configuration au-dessus d’une ressource Kubernetes avec Azure Arc.
    * [Flux](https://docs.fluxcd.io/), qui est nécessaire à la configuration du workflow GitOps, est déployé sur le cluster.
    * Flux tire (pull) les manifestes et les charts Helm du dépôt Git de démonstration, et les déploie sur le cluster.

## <a name="next-steps"></a>Étapes suivantes

* [Connecter un cluster à Azure Arc](./connect-cluster.md)
* [Créer des configurations sur votre cluster Kubernetes compatible avec Azure Arc](./use-gitops-connected-cluster.md)
* [Utiliser Azure Policy pour appliquer des configurations à grande échelle](./use-azure-policy.md)
