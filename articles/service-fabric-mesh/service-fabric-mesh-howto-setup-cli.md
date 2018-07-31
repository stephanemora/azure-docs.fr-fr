---
title: Configurer l’interface de ligne de commande Azure Service Fabric Mesh CLI | Microsoft Docs
description: Découvrez comment configurer l’interface de ligne de commande Azure Service Fabric Mesh CLI.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: cf899e236b7fa21a90a0784e1e2b4fce0feae055
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213433"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Configurer Service Fabric Mesh CLI
L’interface de ligne de commande Service Fabric Mesh est requise pour déployer et gérer les ressources de Service Fabric Mesh. 

Pour la préversion, Azure Service Fabric Mesh CLI est écrit en tant qu’extension à Azure CLI. Vous pouvez l’installer dans Azure Cloud Shell ou dans une instance locale d’Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez installer Azure CLI 2.0.35 ou ultérieur. Exécutez `az --version` pour trouver la version. Pour installer la dernière version de l’interface CLI ou procéder à sa mise à niveau vers la dernière version, consultez [Installer Azure CLI 2.0][azure-cli-install].

Supprimez toute installation précédente du module Azure Service Fabric Mesh CLI.

```azurecli-interactive
az extension remove --name mesh
```

Installez le module d’extension Azure Service Fabric Mesh CLI à l’aide de la commande suivante. 

```azurecli-interactive
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

Vous pouvez également configurer votre [environnement de développement Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[azure-cli-install]: /cli/azure/install-azure-cli