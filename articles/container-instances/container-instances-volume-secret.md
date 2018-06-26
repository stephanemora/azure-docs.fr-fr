---
title: Monter un volume secret dans Azure Container Instances
description: Découvrez comment monter un volume secret pour stocker des informations sensibles et y accéder à partir de vos instances de conteneur
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: a030582c885dd0a5680bd23046ea2a9c0329701a
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830069"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Monter un volume secret dans Azure Container Instances

Découvrez comment monter un volume *secret* dans vos instances de conteneur pour le stockage et la récupération des informations sensibles par les conteneurs de vos groupes de conteneurs.

> [!NOTE]
> Le montage d’un volume *secret* est actuellement limité aux conteneurs Linux. Découvrez comment transmettre des variables d’environnement sécurisées pour les conteneurs Windows et Linux dans [Définir des variables d’environnement](container-instances-environment-variables.md). Nous travaillons actuellement à proposer toutes ces fonctionnalités dans des conteneurs Windows. En attendant, nous vous invitons à découvrir les différences actuelles de la plateforme dans [Disponibilité des régions et quotas pour Azure Container Instances](container-instances-quotas.md).

## <a name="secret-volume"></a>volume secret

Vous pouvez utiliser un volume *secret* pour fournir des informations sensibles aux conteneurs d’un groupe de conteneurs. Le volume *secret* stocke vos secrets spécifiés dans les fichiers du volume, auxquels les conteneurs de votre groupe de conteneurs peuvent accéder. Grâce aux secrets d’un volume *secret*, vous pouvez éviter de placer des données sensibles telles que les clés SSH ou les informations d’identification sur une base de données dans votre code d’application.

Tous les volumes *secrets* sont sauvegardés par [tmpfs][tmpfs], un système de fichiers reposant sur la RAM ; leur contenu n’est jamais écrit sur un stockage non volatile.

## <a name="mount-a-secret-volume"></a>Monter un volume secret

Pour monter un volume *secret* dans une instance de conteneur, vous devez opérer le déploiement à l’aide d’un [modèle Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Tout d’abord, remplissez le tableau `volumes` dans la section `properties` du groupe de conteneurs du modèle. Ensuite, pour chaque conteneur du groupe de conteneurs dans lequel vous souhaitez monter le volume *secret*, remplissez le tableau `volumeMounts` dans la section `properties` de la définition de conteneur.

Par exemple, le modèle Resource Manager suivant crée un groupe de conteneurs consistant en un seul conteneur. Le conteneur monte un volume *secret* constitué de deux secrets codés en Base64.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Pour voir un exemple de déploiement d’instance de conteneur avec un modèle Azure Resource Manager, consultez [Déployer des groupes de plusieurs conteneurs dans Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment monter d’autres types de volumes dans Azure Container Instances :

* [Monter un partage de fichiers Azure dans Azure Container Instances](container-instances-volume-azure-files.md)
* [Monter un volume emptyDir dans Azure Container Instances](container-instances-volume-emptydir.md)
* [Monter un volume gitRepo dans Azure Container Instances](container-instances-volume-gitrepo.md)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs