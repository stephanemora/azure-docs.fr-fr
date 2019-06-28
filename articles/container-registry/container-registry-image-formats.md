---
title: Formats de contenu Azure Container Registry
description: En savoir plus sur les formats de contenu pris en charge dans Azure Container Registry.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: fe129847e685c7151a9b7ad7ea65abbd38530733
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60827457"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formats de contenu pris en charge dans Azure Container Registry

Utilisez un référentiel privé dans Azure Container Registry pour gérer l’un des formats de contenu suivants. 

## <a name="docker-compatible-container-images"></a>Images de conteneur compatibles avec Docker

Les formats d’image conteneur Docker suivants sont pris en charge :

* [Docker Image manifeste V2, schéma 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image manifeste V2, schéma 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - inclut des listes de manifeste qui permettent aux registres de stocker des images multiplateformes sous une seule référence « image:tag »

## <a name="oci-images"></a>Images OCI

Azure Container Registry prend également en charge les images qui répondent à la [spécification de format d’image OCI (Open Container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md). Les formats d’empaquetage incluent [SIF (Singularity Image Format)](https://www.sylabs.io/2018/03/sif-containing-your-containers/).

## <a name="helm-charts"></a>Graphiques Helm

Azure Container Registry peut héberger des référentiels pour [graphiques Helm](https://helm.sh/), un format d’empaquetage utilisé pour gérer et déployer rapidement des applications pour Kubernetes. Le client [Helm](https://docs.helm.sh/using_helm/#installing-helm) version 2.11.0 ou ultérieure est recommandé.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [envoyer et récupérer](container-registry-get-started-docker-cli.md) des images avec Azure Container Registry.

* Utilisez les [tâches ACR](container-registry-tasks-overview.md) pour générer et tester des images de conteneur. 

* Utilisez le [Moby BuildKit](https://github.com/moby/buildkit) pour générer et empaqueter des conteneurs dans le format OCI.

* Configurez un [référentiel Helm](container-registry-helm-repos.md) hébergé dans Azure Container Registry. 


