---
title: Schéma YAML de déploiement en ligne Kubernetes avec l’interface CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence sur le schéma YAML de déploiement en ligne Kubernetes avec l’interface CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 2be11917873944c91895fc44ebb079f37cc26990
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524409"
---
# <a name="cli-v2-kubernetes-online-deployment-yaml-schema"></a>Schéma YAML de déploiement en ligne Kubernetes avec l’interface CLI (v2)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>schéma

Le schéma JSON source se trouve sur https://azuremlschemas.azureedge.net/latest/k8sOnlineDeployment.schema.json. Le schéma est fourni ci-dessous au format JSON et YAML pour plus de commodité.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/k8sOnlineDeployment.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/k8sOnlineDeployment.schema.yml":::

---

## <a name="remarks"></a>Remarques

La commande `az ml deployment` peut être utilisée pour gérer les déploiements en ligne Kubernetes d’Azure Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
