---
title: Vue d’ensemble d’OpenShift dans Azure
description: Vue d’ensemble d’OpenShift dans Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: aba01fc2317372438bc0d93a6618d518ab03ed0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672310"
---
# <a name="openshift-in-azure"></a>OpenShift dans Azure

OpenShift est une plateforme d’applications de conteneur ouverte et extensible qui permet aux entreprises d’avoir accès à Docker et Kubernetes.  

OpenShift inclut Kubernetes pour la gestion et l’orchestration du conteneur. Il offre également des outils orientés développement et orientés production qui permettent :

- Un développement d’applications rapide.
- Un déploiement et une mise à l’échelle plus simples.
- La maintenance du cycle de vie à long terme des applications et des équipes.

Plusieurs versions d’OpenShift sont disponibles.  Parmi ces versions, seules deux sont disponibles aujourd’hui pour être déployées dans Azure par les clients : OpenShift Container Platform et OKD (auparavant OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift est une version complètement managée d’OpenShift qui s’exécute dans Azure. Ce service est managé et pris en charge par Microsoft et Red Hat conjointement. Pour plus d’informations, consultez la documentation [Azure Red Hat OpenShift Service](../../openshift/index.yml).

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform est une [version commerciale](https://www.openshift.com) adaptée aux entreprises dont Red Hat est l’éditeur et assure le support. Avec cette version, le client achète les droits nécessaires pour OpenShift Container Platform et est responsable de l’installation et de la gestion de l’ensemble de l’infrastructure.

Étant donné que le client est « propriétaire » de l’ensemble de la plateforme, il peut l’installer dans son centre de données local ou sur un cloud public (comme Azure).

## <a name="okd"></a>OKD

OKD est un projet initial [open source](https://www.okd.io/) d’OpenShift dont le support est assuré par la communauté. OKD peut être installé sur CentOS ou Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les éléments prérequis communs pour OpenShift dans Azure](./openshift-container-platform-3x-prerequisites.md)
- [Déployer OpenShift Container Platform dans Azure](./openshift-container-platform-3x.md)
- [Déployer un cluster OpenShift Container Platform autogéré à l'aide de l'offre de la Place de marché](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Déployer OpenShift dans Azure Stack](./openshift-azure-stack.md)
- [Tâches de post-déploiement](./openshift-container-platform-3x-post-deployment.md)
- [Résoudre les problèmes liés au déploiement d’OpenShift](./openshift-container-platform-3x-troubleshooting.md)
