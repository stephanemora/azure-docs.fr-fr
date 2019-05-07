---
title: Gérer Azure Blockchain Service à l’aide d’Azure CLI
description: Comment créer et gérer Azure Blockchain Service avec Azure CLI
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: d078ca181b2eed4b80d4f12f1c03b42f4e242194
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154452"
---
# <a name="manage-azure-blockchain-service-with-azure-cli"></a>Gérer Azure Service Blockchain avec Azure CLI

Outre le portail Azure, vous pouvez utiliser Azure CLI pour créer et gérer les membres de la fonctionnalité « blockchain » et les nœuds de transaction pour votre Service de Blockchain Azure rapidement.

Assurez-vous que vous avez installé la dernière version [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) et d’être connecté à un compte Azure avec `az login`.

Dans les exemples suivants, remplacez l’exemple `<parameter names>` avec vos propres valeurs.

## <a name="create-blockchain-member"></a>Créer des membres de la blockchain

Exemple crée un membre de la fonctionnalité « blockchain » dans Azure Blockchain Service qui exécute le protocole de comptabilité de Quorum dans un consortium de nouveau.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources de Service de Blockchain Azure sont créées. |
| **name** | Un nom unique qui identifie le membre de votre blockchain Azure Blockchain Service. Le nom est utilisé pour l’adresse de point de terminaison public. Par exemple : `myblockchainmember.blockchain.azure.com`. |
| **location** | Région Azure où le membre blockchain est créé. Par exemple : `eastus`. Choisissez l’emplacement le plus proche de vos utilisateurs ou de vos autres applications Azure. |
| **mot de passe** | Le mot de passe du compte de membre. Le mot de passe de compte de membre est utilisé pour s’authentifier sur le point de terminaison public du membre blockchain à l’aide de l’authentification de base. Le mot de passe doit répondre aux trois des quatre conditions suivantes : longueur doit être comprise entre 12 et 72 caractères, 1 caractère minuscule, 1 caractère majuscule, 1 chiffre et 1 caractère spécial est sign(#) pas au nombre, pourcentage (%), virgule (,), star(*), guillemet arrière (\`), double-cliquez quote("), quote(') unique, dash(-) et semicolumn(;)|
| **protocol** | Version préliminaire publique prend en charge le Quorum. |
| **consortium** | Nom du consortium à rejoindre ou créer. |
| **consortiumManagementAccountPassword** | Le mot de passe d’administration de consortium. Le mot de passe est utilisé pour joindre un consortium. |
| **ruleName** | Nom de la règle de mise en liste verte une plage d’adresses IP. Paramètre facultatif pour les règles de pare-feu.|
| **startIpAddress** | Début de la plage d’adresses IP pour la mise en liste verte. Paramètre facultatif pour les règles de pare-feu. |
| **endIpAddress** | Fin de la plage d’adresses IP pour la mise en liste verte. Paramètre facultatif pour les règles de pare-feu. |
| **skuName** | Type de la couche. Utilisez S0 Standard et B0 pour Basic. |

## <a name="change-blockchain-member-password"></a>Mot de passe de modification blockchain membre

Exemple modifie le mot de passe d’un membre de blockchain.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources de Service de Blockchain Azure sont créées. |
| **name** | Nom qui identifie votre membre Azure Blockchain Service. |
| **mot de passe** | Le mot de passe du compte de membre. Le mot de passe doit répondre aux trois des quatre conditions suivantes : longueur doit être comprise entre 12 et 72 caractères, 1 caractère minuscule, 1 caractère majuscule, 1 chiffre et 1 caractère spécial est sign(#) pas au nombre, pourcentage (%), virgule (,), star(*), guillemet arrière (\`), double-cliquez quote(") quote(') unique, dash(-) et le point-virgule ( ;). |


## <a name="create-transaction-node"></a>Créer le nœud de la transaction

Créer un nœud de transactions à l’intérieur d’un membre existant de la fonctionnalité « blockchain ». En ajoutant des nœuds de la transaction, vous pouvez augmenter l’isolation de sécurité et répartir la charge. Par exemple, vous pourriez avoir un point de terminaison du nœud de transaction pour différentes applications clientes.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources de Service de Blockchain Azure sont créées. |
| **name** | Nom du membre blockchain Azure Blockchain Service qui inclut également le nouveau nom de nœud de transaction. |
| **location** | Région Azure où le membre blockchain est créé. Par exemple : `eastus`. Choisissez l’emplacement le plus proche de vos utilisateurs ou de vos autres applications Azure. |
| **mot de passe** | Le mot de passe du nœud de transaction. Le mot de passe doit répondre aux trois des quatre conditions suivantes : longueur doit être comprise entre 12 et 72 caractères, 1 caractère minuscule, 1 caractère majuscule, 1 chiffre et 1 caractère spécial est sign(#) pas au nombre, pourcentage (%), virgule (,), star(*), guillemet arrière (\`), double-cliquez quote(") quote(') unique, dash(-) et le point-virgule ( ;). |
| **ruleName** | Nom de la règle de mise en liste verte une plage d’adresses IP. Paramètre facultatif pour les règles de pare-feu. |
| **startIpAddress** | Début de la plage d’adresses IP pour la mise en liste verte. Paramètre facultatif pour les règles de pare-feu. |
| **endIpAddress** | Fin de la plage d’adresses IP pour la mise en liste verte. Paramètre facultatif pour les règles de pare-feu.|

## <a name="change-transaction-node-password"></a>Mot de passe de modification transaction nœud

Exemple modifie un mot de passe de nœud de transaction.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service existent. |
| **name** | Nom du membre blockchain Azure Blockchain Service qui inclut également le nouveau nom de nœud de transaction. |
| **mot de passe** | Le mot de passe du nœud de transaction. Le mot de passe doit répondre aux trois des quatre conditions suivantes : longueur doit être comprise entre 12 et 72 caractères, 1 caractère minuscule, 1 caractère majuscule, 1 chiffre et 1 caractère spécial est sign(#) pas au nombre, pourcentage (%), virgule (,), star(*), guillemet arrière (\`), double-cliquez quote(") quote(') unique, dash(-) et le point-virgule ( ;). |

## <a name="change-consortium-management-account-password"></a>Modification de mot de passe consortium gestion compte

Le compte de gestion consortium est utilisé pour la gestion des appartenances consortium. Chaque membre est identifiée uniquement par un compte de gestion du consortium et vous pouvez modifier le mot de passe de ce compte avec la commande suivante.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources de Service de Blockchain Azure sont créées. |
| **name** | Nom qui identifie votre membre Azure Blockchain Service. |
| **consortiumManagementAccountPassword** | Mot de passe du compte de gestion consortium. Le mot de passe doit répondre aux trois des quatre conditions suivantes : longueur doit être comprise entre 12 et 72 caractères, 1 caractère minuscule, 1 caractère majuscule, 1 chiffre et 1 caractère spécial est sign(#) pas au nombre, pourcentage (%), virgule (,), star(*), guillemet arrière (\`), double-cliquez quote(") quote(') unique, dash(-) et le point-virgule ( ;). |
  
## <a name="update-firewall-rules"></a>Mettre à jour des règles de pare-feu

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service existent. |
| **name** | Nom du membre de blockchain Azure Blockchain Service. |
| **ruleName** | Nom de la règle de mise en liste verte une plage d’adresses IP. Paramètre facultatif pour les règles de pare-feu.|
| **startIpAddress** | Début de la plage d’adresses IP pour la mise en liste verte. Paramètre facultatif pour les règles de pare-feu.|
| **endIpAddress** | Fin de la plage d’adresses IP pour la mise en liste verte. Paramètre facultatif pour les règles de pare-feu.|

## <a name="list-api-keys"></a>Clés de l’API de liste

Clés de l’API peuvent être utilisées pour l’accès de nœud similaire au nom d’utilisateur et mot de passe. Il existe deux clés d’API pour prendre en charge de la rotation des clés. Utilisez la commande suivante pour répertorier vos clés API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service existent. |
| **name** | Nom du membre blockchain Azure Blockchain Service qui inclut également le nouveau nom de nœud de transaction. |

## <a name="regenerate-api-keys"></a>Régénère les clés API

Utilisez la commande suivante pour régénérer vos clés API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service existent. |
| **name** | Nom du membre blockchain Azure Blockchain Service qui inclut également le nouveau nom de nœud de transaction. |
| **keyName** | Remplacez \<keyValue\> avec key1 ou key2. |

## <a name="delete-a-transaction-node"></a>Supprimer un nœud de transactions

Exemple supprime un nœud de transactions du membre blockchain.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service existent. |
| **name** | Nom du membre blockchain Azure Blockchain Service qui inclut également le nouveau nom de nœud de transaction doit être supprimé. |

## <a name="delete-a-blockchain-member"></a>Supprimer un membre de la blockchain

Exemple supprime un membre de la fonctionnalité « blockchain ».

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service existent. |
| **name** | Nom du membre de blockchain Azure Blockchain Service à supprimer. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Octroyer l’accès d’utilisateur Azure AD

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Paramètre | Description |
|---------|-------------|
| **role** | Nom du rôle Azure AD. |
| **assignee** | ID d’utilisateur AD Azure. Par exemple, `user@contoso.com` |
| **scope** | Portée de l’attribution de rôle. Peut être soit un membre de blockchain de nœud de la transaction. |

**Exemple :**

Accorder l’accès au nœud pour l’utilisateur Azure AD à la blockchain **membre**:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exemple :**

Accorder l’accès au nœud pour l’utilisateur Azure AD à la blockchain **nœud de transactions**:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Accorder l’accès au nœud de groupe Azure AD ou application rôle

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| Paramètre | Description |
|---------|-------------|
| **role** | Nom du rôle Azure AD. |
| **assignee-object-id** | ID de groupe Azure AD ou application ID. |
| **scope** | Portée de l’attribution de rôle. Peut être soit un membre de blockchain de nœud de la transaction. |

**Exemple :**

Accorder l’accès au nœud pour **rôle d’application**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Supprimer l’accès de nœud d’Azure AD

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Paramètre | Description |
|---------|-------------|
| **role** | Nom du rôle Azure AD. |
| **assignee** | ID d’utilisateur AD Azure. Par exemple, `user@contoso.com` |
| **scope** | Portée de l’attribution de rôle. Peut être soit un membre de blockchain de nœud de la transaction. |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer les nœuds de transaction Azure Blockchain Service avec le portail Azure](configure-transaction-nodes.md)
