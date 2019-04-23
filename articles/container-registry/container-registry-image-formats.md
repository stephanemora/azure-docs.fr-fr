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
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997068"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formats de contenu pris en charge dans Azure Container Registry

Utilisez un référentiel privé dans Azure Container Registry pour gérer l’un des formats de contenu suivants. 

## <a name="docker-compatible-container-images"></a>Images de conteneur compatibles avec Docker

Les formats d’image de conteneur Docker suivantes sont prises en charge :

* [Docker Image manifeste V2, schéma 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image manifeste V2, schéma 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - inclut des listes de manifeste qui permettent aux registres de stocker des images multiplateformes sous une seule référence « image:tag »

## <a name="oci-images"></a>Images OCI

Azure Container Registry prend également en charge les images qui remplissent la [spécification de Format d’Image Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md). Formats d’empaquetage incluent [singularité de l’Image de Format (SIF)](https://www.sylabs.io/2018/03/sif-containing-your-containers/).

## <a name="helm-charts"></a>Graphiques Helm

Registre de conteneurs Azure peut héberger des référentiels pour [Helm graphiques](https://helm.sh/), un format d’empaquetage permet de gérer rapidement et de déployer des applications pour Kubernetes. Le client [Helm](https://docs.helm.sh/using_helm/#installing-helm) version 2.11.0 ou ultérieure est recommandé.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [envoyer et récupérer](container-registry-get-started-docker-cli.md) des images avec Azure Container Registry.

* Utilisez les [tâches ACR](container-registry-tasks-overview.md) pour générer et tester des images de conteneur. 

* Utilisez le [Moby BuildKit](https://github.com/moby/buildkit) pour générer et empaqueter des conteneurs dans le format OCI.

* Configurez un [référentiel Helm](container-registry-helm-repos.md) hébergé dans Azure Container Registry. 


