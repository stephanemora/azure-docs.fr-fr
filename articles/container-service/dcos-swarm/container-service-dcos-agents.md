---
title: (DÉPRÉCIÉ) Pools d’agents DC/OS pour Azure Container Service
description: Fonctionnement des pools d’agents publics et privés avec un cluster Azure Container Service DC/OS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 03cacda1aa405cb2d0ded579c8ddb5f6011ce3bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478444"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>(DÉPRÉCIÉ) Pools d’agents DC/OS pour Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Des clusters DC/OS d’Azure Container Service contiennent des nœuds d’agent dans deux pools, un pool public et un pool privé. Une application peut être déployée dans un pool, ce qui affecte l’accessibilité entre les machines de votre service de conteneur. Les machines peuvent être exposées à internet (publiques) ou conservées en interne (privées). Cet article explique brièvement pourquoi il existe des pools publics et privés.


* **Agents privés** : Les nœuds d’un agent privé sont exécutés via un réseau non routable. Ce réseau n’est accessible qu’à partir de la zone administrateur ou par le biais du routeur Edge de la zone publique. Par défaut, DC/OS lance les applications sur les nœuds de l’agent privé. 

* **Agents publics** : Les nœuds d’un agent public exécutent les services et les applications DC/OS sur un réseau accessible publiquement. 

Pour plus d’informations sur la sécurité réseau DC/OS, consultez la [documentation DC/OS](https://docs.mesosphere.com/) .

## <a name="deploy-agent-pools"></a>Déploiement de pools d’agents

Les pools d’agents DC/OS d’Azure Container Service sont créés comme suit :

* Le **pool privé** contient le nombre de nœuds d’agent que vous spécifiez lorsque vous [déployez le cluster DC/OS](container-service-deployment.md). 

* Le **pool public** contient initialement un nombre prédéterminé de nœuds d’agent. Ce pool est automatiquement ajouté lorsque le cluster DC/OS est approvisionné.

Le pool privé et le pool public sont des groupes de machines virtuelles Azure identiques. Vous pouvez redimensionner ces pools après le déploiement.

## <a name="use-agent-pools"></a>Utilisation de pools d’agents
Par défaut, **Marathon** déploie toute nouvelle application sur les nœuds de l’agent *privé* . Vous devez déployer explicitement l’application sur les nœuds *publics* pendant la création de l’application. Sélectionnez l’onglet **Facultatif** et saisissez **slave_public** pour la valeur **Rôles de ressources acceptés**. Ce processus est décrit [ici](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) et dans la documentation [DC/OS](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/).

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [gestion de vos conteneurs DC/OS](container-service-mesos-marathon-ui.md).

* Découvrez comment [ouvrir le pare-feu](container-service-enable-public-access.md) fourni par Azure pour autoriser l’accès public à vos conteneurs DC/OS.

