---
title: 'Démarrage rapide : Définir et récupérer un certificat dans Azure Key Vault avec Azure CLI'
description: Démarrage rapide montrant comment définir et récupérer un certificat dans Azure Key Vault à l’aide d’Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: dc3abe43c2cdf4d0ee58ac9aed570a2c507b0d7f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815267"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-cli"></a>Démarrage rapide : Définir et récupérer un certificat dans Azure Key Vault à l’aide d’Azure CLI

Dans ce guide de démarrage rapide, vous créez un coffre de clés dans Azure Key Vault avec Azure CLI. Azure Key Vault est un service cloud qui fonctionne comme un magasin des secrets sécurisé. Vous pouvez stocker des clés, des mots de passe, des certificats et d’autres secrets en toute sécurité. Pour plus d’informations sur Key Vault, consultez la [présentation](../general/overview.md). Azure CLI vous permet de créer et gérer des ressources Azure à l’aide de commandes ou de scripts. Une fois que vous avez terminé, vous allez stocker un certificat.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ce guide de démarrage rapide nécessite la version 2.0.4 ou ultérieure de l’interface Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-certificate-to-key-vault"></a>Ajouter un certificat à Key Vault

Pour ajouter un certificat au coffre, vous devez effectuer deux autres opérations. Ce certificat peut être utilisé par une application. 

Tapez les commandes ci-dessous pour créer un certificat auto-signé avec une stratégie par défaut appelé **ExampleCertificate** :

```azurecli
az keyvault certificate create --vault-name "<your-unique-keyvault-name>" -n ExampleCertificate -p "$(az keyvault certificate get-default-policy)"
```

Vous pouvez maintenant référencer ce certificat que vous avez ajouté à Azure Key Vault à l’aide de son URI. Utilisez **"https://<nom_unique_de_votre_coffre_de_clés>.vault.azure.net/certificates/ExampleCertificate"** pour obtenir la version actuelle. 

Pour voir le certificat stocké précédemment :

```azurecli

az keyvault certificate show --name "ExampleCertificate" --vault-name "<your-unique-keyvault-name>"
```

Vous venez de créer un coffre de clés, d’y stocker un certificat et de récupérer ce dernier.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés et vous y avez stocké un certificat. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- Consulter la référence des [commandes az keyvault Azure CLI](/cli/azure/keyvault)
- Passer en revue la [Vue d’ensemble de la sécurité de Key Vault](../general/security-features.md)
