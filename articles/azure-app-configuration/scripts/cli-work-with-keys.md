---
title: Exemple de script Azure CLI - Utiliser des clés-valeurs dans un magasin Azure App Configuration | Microsoft Docs
description: Fournit des informations sur l’utilisation de clés-valeurs dans un magasin Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: e69e2ca5ccd8e8edc2f55d74a0cca03eaabc9f49
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884243"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Utiliser des clés-valeurs dans un magasin Azure App Configuration

Cet exemple de script crée une clé-valeur dans un magasin Azure App Configuration liste toutes les clés-valeurs existantes, met à jour la clé-valeur nouvellement créée, et enfin, la supprime.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

Vous devez d’abord installer l’extension CLI d’Azure App Configuration en exécutant la commande suivante :

        az extension add -n appconfig

## <a name="sample-script"></a>Exemple de script

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour effectuer des opérations sur des clés-valeurs dans un magasin de configuration d’application. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az appconfig kv set](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-kv-set) | Crée ou met à jour une clé-valeur. |
| [az appconfig kv list](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-kv-list) | Liste les clés-valeurs d’un magasin de configuration d’application. |
| [az appconfig kv delete](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-kv-delete) | Supprime une clé-valeur. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts d’interface de ligne de commande App Configuration dans la [documentation Azure App Configuration](../cli-samples.md).
