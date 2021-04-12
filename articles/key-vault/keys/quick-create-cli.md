---
title: Créer et récupérer les attributs d’une clé dans Azure Key Vault - Azure CLI
description: Démarrage rapide montrant comment définir et récupérer une clé dans Azure Key Vault à l’aide d’Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4141e60370b397e799664b7d42384bbeb096bd05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99071169"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Démarrage rapide : Définir et récupérer une clé dans Azure Key Vault avec Azure CLI

Dans ce guide de démarrage rapide, vous créez un coffre de clés dans Azure Key Vault avec Azure CLI. Azure Key Vault est un service cloud qui fonctionne comme un magasin des secrets sécurisé. Vous pouvez stocker des clés, des mots de passe, des certificats et d’autres secrets en toute sécurité. Pour plus d’informations sur Key Vault, consultez la [présentation](../general/overview.md). Azure CLI vous permet de créer et gérer des ressources Azure à l’aide de commandes ou de scripts. Une fois que vous avez terminé, vous allez stocker une clé.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Pour ce guide de démarrage rapide, vous devez avoir la version 2.0.4 ou ultérieure de l’interface Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Ajouter une clé à Key Vault

Pour ajouter une clé au coffre, vous devez effectuer deux autres opérations. Cette clé peut être utilisée par une application. 

Tapez les commandes ci-dessous pour créer une clé appelée **ExampleKey** :

```azurecli
az keyvault key create --vault-name "<your-unique-keyvault-name>" -n ExampleKey --protection software
```

Vous pouvez maintenant référencer cette clé que vous avez ajoutée à Azure Key Vault à l’aide de son URI. Utilisez **"https://<nom_unique_de_votre_coffre_de_clés>.vault.azure.net/keys/ExampleKey"** pour obtenir la version actuelle. 

Pour afficher la clé stockée précédemment :

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "<your-unique-keyvault-name>"
```

Vous venez de créer un coffre de clés, d’y stocker une clé et de récupérer cette dernière.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés et vous y avez stocké une clé. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- Consulter la référence des [commandes az keyvault Azure CLI](/cli/azure/keyvault)
- Passer en revue la [Vue d’ensemble de la sécurité de Key Vault](../general/security-overview.md)
