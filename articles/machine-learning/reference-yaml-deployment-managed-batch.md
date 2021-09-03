---
title: Schéma YAML de déploiement par lots géré avec l’interface CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence sur le schéma YAML de déploiement par lots géré avec l’interface CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 284d9ac70c1ecb8929b31e6994a87686e1e4ec0e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532243"
---
# <a name="cli-v2-managed-batch-deployment-yaml-schema"></a>Schéma YAML de déploiement par lots géré avec l’interface CLI (v2)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>schéma

Le schéma JSON source se trouve sur https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json. Le schéma est fourni ci-dessous aux formats JSON et YAML pour plus de commodité.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/batchDeployment.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/batchDeployment.schema.yml":::

---

## <a name="remarks"></a>Remarques

La commande `az ml deployment` peut être utilisée pour gérer les déploiements d’Azure Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
