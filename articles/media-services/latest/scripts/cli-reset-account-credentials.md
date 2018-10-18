---
title: Exemple de script Azure CLI - Réinitialiser vos informations d’identification de compte | Microsoft Docs
description: Utilisez le script Azure CLI pour réinitialiser vos informations d’identification de compte et récupérer les paramètres du fichier app.config.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: f97cf87288f5efbeef11a27e98ab3fc2caf27b40
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376411"
---
# <a name="cli-example-reset-the-account-credentials"></a>Exemple CLI : Réinitialiser les informations d’identification de compte

Le script Azure CLI de cet article vous montre comment réinitialiser vos informations d’identification de compte et récupérer les paramètres du fichier app.config.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.20 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/reset-account-credentials/Reset-Account-Credentials.sh "Reset credentials")]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’exemples, consultez les [exemples Azure CLI](../cli-samples.md).
