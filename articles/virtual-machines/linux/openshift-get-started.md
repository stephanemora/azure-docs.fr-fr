---
title: Vue d’ensemble d’OpenShift dans Azure | Microsoft Docs
description: Vue d’ensemble d’OpenShift dans Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: d68215359d50ac153d6df3bbcce5a9b6171698bb
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085437"
---
# <a name="openshift-in-azure"></a>OpenShift dans Azure

OpenShift est une plateforme d’applications de conteneur ouverte et extensible qui permet aux entreprises d’avoir accès à Docker et Kubernetes.  

OpenShift inclut Kubernetes pour la gestion et l’orchestration du conteneur. Il offre également des outils orientés développement et orientés production qui permettent :

- Un développement d’applications rapide.
- Un déploiement et une mise à l’échelle plus simples.
- La maintenance du cycle de vie à long terme des applications et des équipes.

Plusieurs versions de OpenShift sont disponibles :

- OpenShift Container Platform
- OpenShift dans Azure (version d’OpenShift complètement managée prévue début 2019)
- OKD (anciennement OpenShift Origin)
- OpenShift Dedicated
- OpenShift Online

Parmi les cinq versions décrites dans cet article, seules deux sont compatibles avec un déploiement dans Azure par les clients : OpenShift Container Platform et OKD.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform est une [version commerciale](https://www.openshift.com) adaptée aux entreprises dont Red Hat est l’éditeur et assure le support. Avec cette version, le client achète les droits nécessaires pour OpenShift Container Platform et est responsable de l’installation et de la gestion de l’ensemble de l’infrastructure.

Étant donné que le client est « propriétaire » de l’ensemble de la plateforme, il peut l’installer dans son centre de données local ou sur un cloud public (comme Azure, AWS ou Google).

## <a name="openshift-on-azure"></a>OpenShift dans Azure

OpenShift dans Azure est une version complètement managée d’OpenShift qui s’exécute dans Azure. Ce service est managé et pris en charge par Microsoft et Red Hat conjointement. Le cluster est déployé dans l’abonnement Azure du client. Le service est actuellement disponible en préversion privée. La version en disponibilité générale est prévue début 2019. Plus d’informations seront fournies à l’approche de la disponibilité générale.

## <a name="okd-formerly-openshift-origin"></a>OKD (anciennement OpenShift Origin)

OKD est un projet initial [open source](https://www.okd.io/) d’OpenShift dont le support est assuré par la communauté. OKD peut être installé sur CentOS ou Red Hat Enterprise Linux (RHEL).

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Dedicated est une version d’OpenShift *à locataire unique*, managée par Red Hat, qui utilise OpenShift Container Platform. Red Hat gère toute l’infrastructure sous-jacente (machines virtuelles, cluster OpenShift, mise en réseau, stockage, etc.). Le cluster est dédié à un seul client et est exécuté dans un cloud public (comme AWS ou Google). Un cluster de base inclut quatre nœuds d’application, que vous devez payer d’avance chaque année.

## <a name="openshift-online"></a>OpenShift Online

Online est une version d’OpenShift *multilocataire* gérée par Red Hat qui utilise Container Platform. Red Hat gère toute l’infrastructure sous-jacente (comme les machines virtuelles, le cluster OpenShift, la mise en réseau et le stockage). 

Avec cette version, le client déploie des conteneurs, mais n’a aucun contrôle sur les hôtes qu’ils exécutent. Étant donné qu’Online est multilocataire, les conteneurs peuvent coexister sur les mêmes hôtes de machine virtuelle que les conteneurs d’autres clients. Le coût est fixé par conteneur.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les éléments prérequis communs pour OpenShift dans Azure](./openshift-prerequisites.md)
- [Déployer OpenShift Container Platform dans Azure](./openshift-container-platform.md)
- [Déployer OKD dans Azure](./openshift-okd.md)
- [Déployer OpenShift dans Azure Stack](./openshift-azure-stack.md)
- [Tâches de post-déploiement](./openshift-post-deployment.md)
- [Résoudre les problèmes liés au déploiement d’OpenShift](./openshift-troubleshooting.md)
