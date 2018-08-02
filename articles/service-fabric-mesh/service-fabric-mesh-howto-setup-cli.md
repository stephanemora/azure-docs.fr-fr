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
ms.openlocfilehash: 567f2afdea44f439779212c61fb3a129f4f979be
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281570"
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
az extension add --source https://meshcli.blob.core.windows.net/cli/mesh-0.9.1-py2.py3-none-any.whl
```

Vous pouvez également configurer votre [environnement de développement Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

[azure-cli-install]: /cli/azure/install-azure-cli