---
title: Schéma YAML de point de terminaison en ligne Kubernetes avec l’interface CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence sur le schéma YAML de point de terminaison en ligne Kubernetes avec l’interface CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: ad1946ca0460257365cdb589af21ee9f37e6f16f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524408"
---
# <a name="cli-v2-kubernetes-online-endpoint-yaml-schema"></a>Schéma YAML de point de terminaison en ligne Kubernetes avec l’interface CLI (v2)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>schéma

Le schéma JSON source se trouve sur https://azuremlschemas.azureedge.net/latest/k8sOnlineEndpoint.schema.json. Le schéma est fourni ci-dessous dans les formats JSON et YAML pour plus de commodité.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/k8sOnlineEndpoint.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/k8sOnlineEndpoint.schema.yml":::

---

## <a name="remarks"></a>Remarques

La commande `az ml endpoint` peut être utilisée pour gérer les points de terminaison d’Azure Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
