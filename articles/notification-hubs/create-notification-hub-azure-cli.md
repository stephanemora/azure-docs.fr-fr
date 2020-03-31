---
title: Démarrage rapide - Créer un hub de notification Azure avec Azure CLI | Microsoft Docs
description: Dans ce tutoriel, apprenez à créer un hub de notification Azure avec Azure CLI.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80069482"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Démarrage rapide : Créer un hub de notification Azure avec Azure CLI

Azure Notification Hubs fournit un moteur d’envoi de notifications Push facile à utiliser et à grande échelle qui vous permet d’envoyer des notifications à n’importe quelle plateforme (iOS, Android, Windows, Kindle, Baidu, etc.) à partir de n’importe quel serveur principal (cloud ou local). Pour plus d’informations sur le service, consultez [Présentation d’Azure Notification Hubs](notification-hubs-push-notification-overview.md).

Dans ce guide de démarrage rapide, vous créez un hub de notification avec Azure CLI. La première section vous présente les étapes à suivre pour créer un espace de noms de hub de notification, et pour interroger les informations relatives à la stratégie d’accès de cet espace de noms. La deuxième section vous présente les étapes à suivre pour créer un hub de notification dans un espace de noms existant.  Vous allez également apprendre à créer une stratégie d’accès personnalisée.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Notification Hubs nécessite la version 2.0.67 ou ultérieure d’Azure CLI. Exécutez `az --version` pour rechercher la version et les bibliothèques dépendantes installées. Pour installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Préparation de votre environnement

1. Connectez-vous.

   Connectez-vous à l’aide de la commande [az login](/cli/azure/reference-index#az-login) si vous utilisez une installation locale de l’interface CLI.

    ```azurecli-interactive
    az login
    ```

    Suivez les étapes affichées dans votre terminal pour effectuer le processus d’authentification.

2. Installez l’extension Azure CLI.

   Si vous souhaitez exécuter les commandes Azure CLI relatives aux hubs de notification, installez l’[extension Azure CLI pour Notification Hubs](/cli/azure/ext/notification-hub/notification-hub).  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. Créez un groupe de ressources.

   Les hubs de notification Azure, comme toutes les ressources Azure, doivent être déployés dans un groupe de ressources. Les groupes de ressources vous permettent d’organiser et de gérer les ressources Azure connexes.

   Pour ce guide de démarrage rapide, créez un groupe de ressources nommé *spnhubrg* dans la zone *eastus* à l’aide de la commande [az group create](/cli/azure/group#az-group-create) suivante :

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>Créer un espace de noms de hub de notification

1. Créez un espace de noms pour vos hubs de notification

   Un espace de noms contient un ou plusieurs hubs. Le nom doit être unique dans tous les abonnements Azure.  Pour vérifier la disponibilité de l’espace de noms de service donné, utilisez la commande [az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability).  Exécutez la commande [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) pour créer un espace de noms.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. Listez les clés et les chaînes de connexion de votre stratégie d’accès à l’espace de noms.

   Une stratégie d’accès nommée **RootManageSharedAccessKey** est automatiquement créée pour un nouvel espace de noms.  Chaque stratégie d’accès dispose de deux jeux de clés et de chaînes de connexion.  Pour lister les clés et les chaînes de connexion de l’espace de noms, exécutez la commande [az notification-hub namespace authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys).

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>Créer des hubs de notification

1. Créez votre premier hub de notification.

   Vous pouvez désormais créer un hub de notification dans le nouvel espace de noms.  Exécutez la commande [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) pour créer un hub de notification.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Créez un deuxième hub de notification.

   Vous pouvez créer plusieurs hubs de notification dans un seul espace de noms.  Pour créer un deuxième hub de notification dans le même espace de noms, réexécutez la commande `az notification-hub create` avec un autre nom de hub.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>Utiliser des stratégies d’accès

1. Créez une règle d’autorisation pour un hub de notification.

   Une stratégie d’accès est automatiquement créée pour chaque nouveau hub de notification.  Pour créer et personnaliser votre propre stratégie d’accès, utilisez la commande [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create).

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. Lister les stratégies d’accès d’un hub de notification.

   Pour interroger les stratégies d’accès existantes d’un hub de notification, utilisez la commande [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list).

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > N’utilisez pas la stratégie **DefaultFullSharedAccessSignature** dans votre application. Il est prévu pour être utilisé uniquement dans votre serveur principal.  Utilisez uniquement les stratégies d’accès **Listen** dans votre application cliente.

3. Lister les clés et les chaînes de connexion d’une stratégie d’accès du hub de notification

   Il existe deux jeux de clés et de chaînes de connexion pour chaque stratégie d’accès.  Vous en aurez besoin pour gérer les notifications Push.  Pour lister les clés et les chaînes de connexion d’une stratégie d’accès du hub de notification, utilisez la commande [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys).

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Un [espace de noms de hub de notification](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) et un [hub de notification](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) ont des stratégies d’accès distinctes.  Vérifiez que vous utilisez la commande de référence Azure CLI appropriée quand vous recherchez des clés et des chaînes de connexion.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin du groupe de ressources, utilisez la commande [az group delete](/cli/azure/group) pour supprimer celui-ci ainsi que toutes les ressources associées.

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>Voir aussi

Découvrez toutes les fonctionnalités de gestion des hubs de notification à l’aide d’Azure CLI.

* [Liste complète des commandes de références Azure CLI pour les hubs de notification](/cli/azure/ext/notification-hub/notification-hub)
* [Liste des commandes de référence Azure CLI pour les espaces de noms de hubs de notification](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Liste des commandes de référence Azure CLI pour les règles d’autorisation de hubs de notification](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Liste des commandes de référence Azure CLI pour les informations d’identification de hubs de notification](/cli/azure/ext/notification-hub/notification-hub/credential)
