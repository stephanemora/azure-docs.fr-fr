---
title: Schéma YAML de l’environnement CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma YAML de l’environnement CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 66c14b25fb60f76b015d461b7375deb595c954fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563312"
---
# <a name="cli-v2-environment-yaml-schema"></a>Schéma YAML de l’environnement CLI (v2)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>schéma

Le schéma JSON source se trouve sur https://azuremlschemas.azureedge.net/latest/environment.schema.json. Le schéma est fourni ci-dessous au format JSON et YAML pour plus de commodité.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/environment.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/environment.schema.yml":::

---

## <a name="remarks"></a>Remarques

La commande `az ml environment` peut être utilisée pour gérer les environnements d’Azure Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
