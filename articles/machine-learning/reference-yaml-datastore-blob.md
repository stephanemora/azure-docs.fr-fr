---
title: Schéma YAML de magasin de données Azure Blob avec l’interface CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentation de référence pour le schéma YAML de magasin de données Azure Blob avec l’interface CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: de1ade806ebd7b26d89fda6d0a7d69d35c2d020d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532733"
---
# <a name="cli-v2-azure-blob-datastore-yaml-schema"></a>Schéma YAML de magasin de données Azure Blob avec l’interface CLI (v2)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>schéma

Le schéma JSON source se trouve sur https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json. Le schéma est fourni ci-dessous dans les formats JSON et YAML pour plus de commodité.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/azureBlob.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/azureBlob.schema.yml":::

---

## <a name="remarks"></a>Remarques

La commande `az ml datastore` peut être utilisée pour gérer les magasins de données d’Azure Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser l’interface CLI (v2)](how-to-configure-cli.md)
