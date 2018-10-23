---
title: Configurer l’interface de ligne de commande Azure Service Fabric Mesh CLI | Microsoft Docs
description: Découvrez comment configurer l’interface de ligne de commande Azure Service Fabric Mesh CLI.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/26/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 7e8a12a215c94102f6b08262f129faebf9cfcde9
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115622"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Configurer Service Fabric Mesh CLI
L’interface de ligne de commande Service Fabric Mesh est requise pour déployer et gérer des ressources de Service Fabric Mesh. 

Pour la préversion, Azure Service Fabric Mesh CLI est écrit en tant qu’extension à Azure CLI. Vous pouvez l’installer dans Azure Cloud Shell ou dans une instance locale d’Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-service-fabric-mesh-cli-locally"></a>Installer localement l’interface CLI Service Fabric Mesh
Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez installer la version Azure CLI 2.0.43 ou ultérieure. Exécutez `az --version` pour trouver la version. Pour installer ou mettre à niveau la dernière version de l’interface CLI, consultez [Installer Azure CLI][azure-cli-install].

Installez le module d’extension Azure Service Fabric Mesh CLI à l’aide de la commande suivante. 

```azurecli-interactive
az extension add --name mesh
```

Pour mettre à jour un module CLI Azure Service Fabric Mesh existant, exécutez la commande suivante.

```azurecli-interactive
az extension update --name mesh
```

Vous pouvez également configurer votre [environnement de développement Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[azure-cli-install]: /cli/azure/install-azure-cli
