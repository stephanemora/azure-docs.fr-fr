---
title: Formats de contenu Azure Container Registry
description: En savoir plus sur les formats de contenu pris en charge dans Azure Container Registry.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2018
ms.author: danlep
ms.openlocfilehash: e7155604339bc634078fd022e05ede5f902bc0d8
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634823"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formats de contenu pris en charge dans Azure Container Registry

Utilisez un référentiel privé dans Azure Container Registry pour gérer l’un des formats de contenu suivants. 

## <a name="docker-compatible-container-images"></a>Images de conteneur compatibles avec Docker

* [Docker Image manifeste V2, schéma 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image manifeste V2, schéma 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - inclut des listes de manifeste qui permettent aux registres de stocker des images multiplateformes sous une seule référence « image:tag »

* [Spécification du format d’image Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md) 


## <a name="helm-charts"></a>Graphiques Helm

Azure Container Registry peut également héberger des référentiels pour [graphiques Helm](https://helm.sh/), un format d’empaquetage utilisé pour gérer et déployer rapidement des applications pour Kubernetes. Le client [Helm](https://docs.helm.sh/using_helm/#installing-helm) version 2.11.0 ou ultérieure est recommandé.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [envoyer et récupérer](container-registry-get-started-docker-cli.md) des images avec Azure Container Registry.

* Utilisez les [tâches ACR](container-registry-tasks-overview.md) pour générer et tester des images de conteneur. 

* Utilisez le [Moby BuildKit](https://github.com/moby/buildkit) pour générer et empaqueter des conteneurs dans le format OCI.

* Configurez un [référentiel Helm](container-registry-helm-repos.md) hébergé dans Azure Container Registry. 


