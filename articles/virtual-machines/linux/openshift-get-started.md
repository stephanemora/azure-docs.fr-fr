---
title: Vue d’ensemble d’OpenShift dans Azure | Microsoft Docs
description: Vue d’ensemble d’OpenShift dans Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: 53bed2131e81ee5ed0f46bde389262ee8349339a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542422"
---
# <a name="openshift-in-azure"></a>OpenShift dans Azure

OpenShift est une plateforme d’applications de conteneur ouverte et extensible qui permet aux entreprises d’avoir accès à Docker et Kubernetes.  

OpenShift inclut Kubernetes pour la gestion et l’orchestration du conteneur. Il offre également des outils orientés développement et orientés production qui permettent :

- Un développement d’applications rapide.
- Un déploiement et une mise à l’échelle plus simples.
- La maintenance du cycle de vie à long terme des applications et des équipes.

Il existe plusieurs versions d’OpenShift disponibles.  De ces versions, seuls deux est aujourd'hui disponibles pour les clients à déployer dans Azure : OpenShift Container Platform et OKD (anciennement OpenShift Origin).

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform est une [version commerciale](https://www.openshift.com) adaptée aux entreprises dont Red Hat est l’éditeur et assure le support. Avec cette version, le client achète les droits nécessaires pour OpenShift Container Platform et est responsable de l’installation et de la gestion de l’ensemble de l’infrastructure.

Étant donné que les clients « s’accapare » l’ensemble de la plate-forme, ils peuvent l’installer dans leur centre de données en local ou dans un cloud public (par exemple, Azure).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Azure Red Hat OpenShift est une offre entièrement gérée de OpenShift s’exécutant dans Azure. Ce service est managé et pris en charge par Microsoft et Red Hat conjointement. Le cluster est déployé dans l’abonnement Azure du client. Le service est planifié pour être GA autour de mai 2019. Documentation distincte pour le service géré sera disponible une fois que le service est la disponibilité générale.

## <a name="okd"></a>OKD

OKD est un projet initial [open source](https://www.okd.io/) d’OpenShift dont le support est assuré par la communauté. OKD peut être installé sur CentOS ou Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les éléments prérequis communs pour OpenShift dans Azure](./openshift-prerequisites.md)
- [Déployer OpenShift Container Platform dans Azure](./openshift-container-platform.md)
- [Déployer OpenShift Container Platform autogérées place de marché offre](./openshift-marketplace-self-managed.md)
- [Déployer OpenShift dans Azure Stack](./openshift-azure-stack.md)
- [Tâches de post-déploiement](./openshift-post-deployment.md)
- [Résoudre les problèmes liés au déploiement d’OpenShift](./openshift-troubleshooting.md)
