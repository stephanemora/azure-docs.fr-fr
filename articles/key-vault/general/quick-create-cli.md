---
title: 'Démarrage rapide : Créer un coffre de clés Azure Key Vault à l’aide de l’interface de ligne de commande Azure'
description: Guide de démarrage rapide montrant comment créer un coffre de clés Azure Key Vault à l’aide de l’interface de ligne de commande Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 588bee2922ee44f3f89b5d252b5b4a6991d26b82
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815123"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Démarrage rapide : Créer un coffre de clés à l’aide de l’interface de ligne de commande Azure

Azure Key Vault est un service cloud qui fournit un magasin sécurisé pour les [clés](../keys/index.yml), les [secrets](../secrets/index.yml) et les [certificats](../certificates/index.yml). Pour plus d’informations sur Key Vault, consultez [À propos d’Azure Key Vault](overview.md) ; pour plus d’informations sur ce qui peut être stocké dans un coffre de clés, consultez [À propos des clés, des secrets et des certificats](about-keys-secrets-certificates.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ce guide de démarrage rapide nécessite la version 2.0.4 ou ultérieure de l’interface Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés et l’avez supprimé. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](overview.md)
- Passer en revue la [Vue d’ensemble de la sécurité d’Azure Key Vault](security-features.md)
- Consulter la référence des [commandes az keyvault Azure CLI](/cli/azure/keyvault)

