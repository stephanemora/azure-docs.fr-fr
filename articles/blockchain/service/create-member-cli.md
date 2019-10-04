---
title: Créer une instance Azure Blockchain Service à l’aide d’Azure CLI
description: Utilisez Azure Blockchain Service pour créer un membre blockchain avec Azure CLI.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: be5a8151f0de0a33db09194a7159aded6848c78a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416170"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Démarrage rapide : Créer un membre blockchain Azure Blockchain Service à l’aide d’Azure CLI

Azure Blockchain Service est une plateforme blockchain que vous pouvez utiliser pour exécuter votre logique métier au sein d’un contrat intelligent. Ce guide de démarrage rapide vous montre comment créer un membre blockchain à l’aide d’Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte.

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/bash](https://shell.azure.com/bash). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.

Si vous préférez installer et utiliser l’interface de ligne de commande en local, ce guide de démarrage rapide nécessite au minimum la version 2.0.51 d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez l’installer ou le mettre à niveau, consultez [Installation d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](https://docs.microsoft.com/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-blockchain-member"></a>Créer un membre blockchain

Dans Azure Blockchain Service, créez un membre blockchain qui exécute le protocole de registre Quorum dans un nouveau consortium. Vous devez passer plusieurs paramètres et propriétés. Remplacez les exemples de paramètres par vos propres valeurs.

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"eastus\", \"properties\": {\"password\": \"strongMemberAccountPassword@1\", \"protocol\": \"Quorum\", \"consortium\": \"myConsortiumName\", \"consortiumManagementAccountPassword\": \"strongConsortiumManagementPassword@1\" }, \"sku\": { \"name\": \"S0\" } }"
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service sont créées. Utilisez le groupe de ressources que vous avez créé dans la section précédente.
| **name** | Nom unique qui identifie le membre blockchain Azure Blockchain Service. Le nom est utilisé pour l’adresse de point de terminaison public. Par exemple : `myblockchainmember.blockchain.azure.com`.
| **location** | Région Azure dans laquelle le membre blockchain est créé. Par exemple : `eastus`. Choisissez l’emplacement le plus proche de vos utilisateurs ou de vos autres applications Azure.
| **mot de passe** | Le mot de passe du nœud de transaction par défaut du membre. Utilisez le mot de passe pour l’authentification de base lorsque vous vous connectez au point de terminaison public du nœud de transaction par défaut du membre blockchain.
| **consortium** | Nom du consortium à rejoindre ou créer.
| **consortiumAccountPassword** | Le mot de passe du compte consortium est également appelé mot de passe du compte du membre. Le mot de passe du compte du membre sert à chiffrer la clé privée du compte Ethereum créé pour votre membre. Vous utilisez le compte du membre et le mot de passe du compte du membre pour la gestion du consortium.
| **skuName** | Niveau de service. Utilisez S0 pour Standard et B0 pour De base.

La création du membre blockchain et des ressources de prise en charge prend environ 10 minutes.

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez utiliser le membre blockchain que vous avez créé pour le guide de démarrage rapide ou tutoriel suivant. Lorsque vous n’en avez plus besoin, vous pouvez supprimer les ressources en supprimant le groupe de ressources `myResourceGroup` créé par Azure Blockchain Service.

Exécutez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un membre blockchain, essayez les guides de démarrage rapide de connexion pour [Geth](connect-geth.md), [MetaMask](connect-metamask.md) ou [Truffle](connect-truffle.md).

> [!div class="nextstepaction"]
> [Utiliser Truffle pour vous connecter à un réseau Azure Blockchain Service](connect-truffle.md)
