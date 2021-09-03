---
title: Vue d’ensemble du schéma YAML CLI (v2)
titleSuffix: Azure Machine Learning
description: Vue d’ensemble et index des schémas YAML CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 33d8bb83b172e913abc13804d61c9b158e86e78c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768507"
---
# <a name="cli-v2-yaml-schemas"></a>Schémas YAML CLI (v2)

La CLI (v2) Azure Machine Learning, une extension d’Azure CLI, utilise souvent et requiert parfois des fichiers YAML avec des schémas spécifiques. Cet article répertorie les documents de référence et le schéma source pour les fichiers YAML.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="assets"></a>Éléments multimédias

Informations de référence | URI
- | -
[Espace de travail](reference-yaml-workspace.md) | https://azuremlschemas.azureedge.net/latest/workspace.schema.json
[Calcul](reference-yaml-compute.md) | https://azuremlschemas.azureedge.net/latest/compute.schema.json
[Environment](reference-yaml-environment.md) | https://azuremlschemas.azureedge.net/latest/environment.schema.json
[Dataset](reference-yaml-dataset.md) | https://azuremlschemas.azureedge.net/latest/dataset.schema.json
[Modèle](reference-yaml-model.md) | https://azuremlschemas.azureedge.net/latest/model.schema.json

## <a name="datastores"></a>Magasins de données

Informations de référence | URI
- | -
[Blob Azure](reference-yaml-datastore-blob.md) | https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json
[Azure Files](reference-yaml-datastore-files.md) | https://azuremlschemas.azureedge.net/latest/azureFile.schema.json
[Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json
[Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json

## <a name="jobs"></a>travaux

Informations de référence | URI
- | -
[Commande](reference-yaml-job-command.md) | https://azuremlschemas.azureedge.net/latest/commandJob.schema.json
[Sweep](reference-yaml-job-sweep.md) | https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json

## <a name="endpoints"></a>Points de terminaison

Informations de référence | URI
- | -
[Managé en ligne (en temps réel)](reference-yaml-endpoint-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json
[Lot managé](reference-yaml-endpoint-managed-batch.md) | https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json
[Kubernetes (k8s) en ligne (en temps réel)](reference-yaml-endpoint-k8s-online.md) | https://azuremlschemas.azureedge.net/latest/k8sOnlineEndpoint.schema.json

## <a name="deployments"></a>Déploiements

Informations de référence | URI
- | -
[Managé en ligne (en temps réel)](reference-yaml-deployment-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json
[Lot managé](reference-yaml-deployment-managed-batch.md) | https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json
[Kubernetes (k8s) en ligne (en temps réel)](reference-yaml-deployment-k8s-online.md) | https://azuremlschemas.azureedge.net/latest/k8sOnlineDeployment.schema.json

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
