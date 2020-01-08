---
title: Qu’est-ce qu’un contrôleur d’entrée Azure Application Gateway ?
description: Cet article présente ce qu’est un contrôleur d’entrée Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 838145f8573e11deff8566c932a9c73c6f59f03b
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561658"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Qu’est-ce qu’un contrôleur d’entrée Application Gateway ?
Le contrôleur d’entrée Application Gateway (AGIC) est une application Kubernetes, qui permet aux clients d’[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) de tirer parti de l’équilibreur de charge L7 [Application Gateway](https://azure.microsoft.com/services/application-gateway/) natif d’Azure pour exposer un logiciel cloud à Internet. AGIC surveille le cluster Kubernetes sur lequel il est hébergé et met à jour en permanence une Application Gateway, afin que les services sélectionnés soient exposés à Internet.

Le contrôleur d’entrée s’exécute dans son propre pod sur l’AKS du client. AGIC surveille les modifications dans un sous-ensemble de ressources Kubernetes. L’état du cluster AKS est converti en configuration spécifique à l’Application Gateway et appliqué à [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Avantages du contrôleur d’entrée Application Gateway
AGIC permet à votre déploiement de contrôler plusieurs clusters AKS avec un seul contrôleur d’entrée Application Gateway. AGIC permet également d’éliminer la nécessité d’avoir un autre IP public/équilibreur de charge devant le cluster AKS et d’éviter plusieurs tronçons dans votre chemin d’accès aux données avant que les demandes atteignent le cluster AKS. Application Gateway communique directement avec les pods à l’aide de leur IP privé et ne nécessite pas de services NodePort ou KubeProxy. Cela offre également un meilleur niveau de performance à vos déploiements.

Le contrôleur d’entrée est pris en charge exclusivement par les références SKU Standard_v2 et WAF_v2, ce qui vous offre également des avantages en matière de mise à l’échelle automatique. Application Gateway peut réagir en réponse à une augmentation ou une diminution de la charge du trafic et mettre à l’échelle en conséquence, sans consommer de ressources de votre cluster AKS.

L’utilisation d’Application Gateway en plus d’AGIC permet également de protéger votre cluster AKS en fournissant une stratégie TLS et la fonctionnalité de pare-feu d’applications web (WAF).

![Azure Application Gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC est configuré par le biais de la [ressource d’entrée](https://kubernetes.io/docs/user-guide/ingress/) Kubernetes, ainsi que du service et des déploiements/pods. Il offre un certain nombre de fonctionnalités, tirant parti de l’équilibreur de charge L7 Application Gateway natif d’Azure. Pour n’en nommer que quelques-unes :
  - Routage d’URL
  - Affinité basée sur les cookies
  - Arrêt SSL
  - SSL de bout en bout
  - Prise en charge des sites web publics, privés et hybrides
  - Pare-feu d’applications web intégré

AGIC est en mesure de gérer plusieurs espaces de noms et a des ProhibitedTargets, ce qui signifie qu’AGIC peut configurer Application Gateway spécifiquement pour les clusters AKS sans perturber les autres serveurs principaux existants. 

## <a name="next-steps"></a>Étapes suivantes

- [**Déploiement « greenfield »** ](ingress-controller-install-new.md) : Instructions pour installer AGIC, AKS et Application Gateway sur une infrastructure vierge.
- [**Déploiement « brownfield »** ](ingress-controller-install-existing.md) : Installez AGIC sur un AKS et une Application Gateway existants.

