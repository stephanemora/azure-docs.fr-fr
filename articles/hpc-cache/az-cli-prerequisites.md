---
title: Prérequis Azure CLI à l’utilisation d’Azure HPC Cache
description: Étapes de configuration avant de pouvoir utiliser Azure CLI pour créer ou modifier un cache Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 30621eceefd69cd3e08de137bb34f1079a17a406
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780476"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Configurer Azure CLI pour Azure HPC Cache

Suivez ces étapes pour préparer votre environnement avant d’utiliser Azure CLI pour créer ou gérer un cache Azure HPC Cache.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure HPC Cache nécessite la version 2.7 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="set-default-resource-group-optional"></a>Définir le groupe de ressources par défaut (facultatif)

La plupart des commandes hpc-cache nécessitent que vous transmettiez le groupe de ressources du cache. Vous pouvez définir le groupe de ressources par défaut en utilisant [az configure](/cli/azure/reference-index#az_configure).

## <a name="next-steps"></a>Étapes suivantes

Après avoir installé l’extension Azure CLI et vous être connecté, vous pouvez utiliser Azure CLI pour créer et gérer des systèmes Azure HPC Cache.

* [Créer un cache Azure HPC Cache](hpc-cache-create.md)
* [Documentation Azure CLI sur hpc-cache](/cli/azure/ext/hpc-cache/hpc-cache)
