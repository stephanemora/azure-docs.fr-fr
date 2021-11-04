---
title: Démarrage rapide - Créer une connexion de service dans Spring Cloud avec le Azure CLI
description: Démarrage rapide montrant comment créer une connexion de service dans Spring Cloud avec l'Azure CLI
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4ee0fffb402ba3553055c732df9e0ebad94ea511
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096683"
---
# <a name="quickstart-create-a-service-connection-in-spring-cloud-with-the-azure-cli"></a>Démarrage rapide : Créer une connexion de service dans Spring Cloud avec l'Azure CLI

L’[interface de ligne de commande Azure (Azure CLI)](/cli/azure) est un ensemble de commandes utilisées pour créer et gérer des ressources Azure. L’interface Azure CLI est disponible dans les services Azure et est conçu pour vous permettre d’utiliser rapidement Azure en mettant l’accent sur l’automatisation. Ce démarrage rapide vous présente les options permettant de créer une instance Azure Web PubSub avec Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- La version 2.22.0 ou une version ultérieure de l’interface Azure CLI est requise pour ce démarrage rapide. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

- Ce guide de démarrage rapide part du principe que vous disposez déjà d’au moins une application Cloud Spring s’exécutant sur Azure. Si vous n’avez pas d’application Spring Cloud, [créez-en une](../spring-cloud/quickstart.md).


## <a name="view-supported-target-service-types"></a>Afficher les types de services cibles pris en charge

Utilisez la commande Azure CLI [az spring-cloud connection]() pour créer et gérer les connexions de service à votre application Spring Cloud. 

```azurecli-interactive
az spring-cloud connection list-support-types
```

## <a name="create-a-service-connection"></a>Créer une connexion de service

Utilisez la commande Azure CLI [az spring-cloud connection]() pour créer une connexion de service à un stockage blob, en fournissant les informations suivantes :

- **Nom du groupe de ressources de Spring Cloud :**  le nom du groupe de ressources de Spring Cloud.
- **Nom du Spring Cloud :**  le nom de votre Spring Cloud.
- **Nom de l'application Spring Cloud :**  le nom de votre application Spring Cloud qui se connecte au service cible.
- **Nom du groupe de ressources du service cible :**  Nom du groupe de ressources du stockage d’objets blob.
- **Nom du compte de stockage :**  Le nom du compte de votre stockage de blob.

```azurecli-interactive
az spring-cloud connection create storage-blob -g <spring_cloud_resource_group> --service <spring_cloud_name> --app <app_name> --deployment default --tg <storage_resource_group> --account <storage_account_name> --system-identity
```

> [!NOTE]
> Si vous n'avez pas de stockage blob, vous pouvez en `az spring-cloud connection create storage-blob -g <app_service_resource_group> -n <app_service_name> --tg <storage_resource_group> --account <storage_account_name> --system-identity --new` provisionner un nouveau et vous connecter directement à votre service d'applications.

## <a name="view-connections"></a>Afficher les connexions

Utilisez la commande Azure CLI [az spring-cloud connection ]()pour répertorier la connexion à votre application Spring Cloud, en fournissant les informations suivantes :

```azurecli-interactive
az spring-cloud connection list -g <your-spring-cloud-resource-group> --spring-cloud <your-spring-cloud-name>
```

## <a name="next-steps"></a>Étapes suivantes

Suivez les tutoriels énumérés ci-dessous pour commencer à créer votre propre application avec Service Connector.

> [!div class="nextstepaction"]
> [Didacticiel : Spring Cloud + MySQL](./tutorial-java-spring-mysql.md)

> [!div class="nextstepaction"]
> [Didacticiel : Spring Cloud + Apache Kafka sur Confluent Cloud](./tutorial-java-spring-confluent-kafka.md)
