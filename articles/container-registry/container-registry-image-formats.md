---
title: Formats de contenu pris en charge
description: Découvrez les formats de contenu pris en charge par Azure Container Registry, y compris les images conteneur compatibles avec Docker, les charts Helm, les images OCI et les artefacts OCI.
ms.topic: article
ms.date: 03/02/2021
ms.openlocfilehash: 1203bc8cbebfd59ef28f03d19e939b73d707a3f3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438879"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formats de contenu pris en charge dans Azure Container Registry

Utilisez un référentiel privé dans Azure Container Registry pour gérer l’un des formats de contenu suivants. 

## <a name="docker-compatible-container-images"></a>Images de conteneur compatibles avec Docker

Les formats d’image conteneur Docker suivants sont pris en charge :

* [Docker Image manifeste V2, schéma 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image Manifest v2, schéma 2](https://docs.docker.com/registry/spec/manifest-v2-2/) : inclut des listes de manifeste qui permettent aux registres de stocker des [images multiarchitectures](push-multi-architecture-images.md) sous une seule référence `image:tag`

## <a name="oci-images"></a>Images OCI

Azure Container Registry prend en charge les images qui répondent à la [spécification de format d’image OCI (Open Container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md), notamment la spécification [index d’image](https://github.com/opencontainers/image-spec/blob/master/image-index.md) facultative. Les formats d’empaquetage incluent [SIF (Singularity Image Format)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>Artefacts OCI

Azure Container Registry prend en charge la [spécification de distribution OCI](https://github.com/opencontainers/distribution-spec), une spécification indépendante du Cloud et du fournisseur pour stocker, partager, sécuriser et déployer des images conteneur et d’autres types de contenu (artefacts). La spécification permet à un registre de stocker un large éventail d’artefacts, en plus des images conteneur. Vous utilisez les outils appropriés à l’artefact pour envoyer (push) et tirer (pull) des artefacts. Pour obtenir des exemples, consultez :

* [Envoyer (push) et tirer (pull) un artefact OCI à l’aide d’un registre de conteneurs Azure](container-registry-oci-artifacts.md)
* [Envoyer (push) et tirer (pull) des graphiques Helm vers un registre de conteneurs Azure](container-registry-helm-repos.md)

Pour en savoir plus sur les artefacts OCI, consultez le dépôt [OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) et le dépôt [OCI Artifacts](https://github.com/opencontainers/artifacts) sur GitHub.

## <a name="helm-charts"></a>Graphiques Helm

Azure Container Registry peut héberger des référentiels pour [graphiques Helm](https://helm.sh/), un format d’empaquetage utilisé pour gérer et déployer rapidement des applications pour Kubernetes. Le [client Helm](https://docs.helm.sh/using_helm/#installing-helm) version 3 est recommandé. Consultez [Envoyer (push) et tirer (pull) des graphiques Helm vers un registre de conteneurs Azure](container-registry-helm-repos.md).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [envoyer et récupérer](container-registry-get-started-docker-cli.md) des images avec Azure Container Registry.

* Utilisez les [tâches ACR](container-registry-tasks-overview.md) pour générer et tester des images de conteneur. 

* Utilisez le [Moby BuildKit](https://github.com/moby/buildkit) pour générer et empaqueter des conteneurs dans le format OCI.

* Configurez un [référentiel Helm](container-registry-helm-repos.md) hébergé dans Azure Container Registry. 


