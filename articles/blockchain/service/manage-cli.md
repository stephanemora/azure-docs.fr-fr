---
title: Gérer une instance Azure Blockchain Service à l’aide d’Azure CLI
description: Comment gérer une instance Azure Blockchain Service avec Azure CLI
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455579"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Gérer une instance Azure Blockchain Service à l’aide d’Azure CLI

En plus du portail Azure, vous pouvez utiliser Azure CLI pour gérer les membres blockchain et les nœuds de transaction pour votre service Azure Blockchain.

Vérifiez que vous avez installé la dernière version [d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) et que vous êtes connecté à un compte Azure avec `az login`.

Dans les exemples suivants, remplacez l’exemple `<parameter names>` par vos propres valeurs.

## <a name="create-blockchain-member"></a>Créer un membre blockchain

Dans Azure Blockchain Service, l’exemple crée un membre blockchain qui exécute le protocole de registre Quorum dans un nouveau consortium.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service sont créées. |
| **name** | Nom unique qui identifie le membre blockchain Azure Blockchain Service. Le nom est utilisé pour l’adresse de point de terminaison public. Par exemple : `myblockchainmember.blockchain.azure.com`. |
| **location** | Région Azure dans laquelle le membre blockchain est créé. Par exemple : `eastus`. Choisissez l’emplacement le plus proche de vos utilisateurs ou de vos autres applications Azure. |
| **mot de passe** | Mot de passe du compte du membre. Le mot de passe du compte du membre est utilisé pour s’authentifier auprès du point de terminaison public du membre blockchain à l’aide de l’authentification de base. Le mot de passe doit remplir trois des quatre conditions suivantes : il doit comprendre entre 12 et 72 caractères, 1 caractère minuscule, 1 caractère majuscule, 1 chiffre et 1 caractère spécial qui ne soit pas le signe dièze (#), le symbole de pourcentage (%), une virgule (,), une étoile (*), un guillemet inverse (\`), un guillemet double ("), un guillemet unique ('), un tiret (-) ou un point-virgule (;)|
| **protocol** | La préversion publique prend en charge Quorum. |
| **consortium** | Nom du consortium à rejoindre ou créer. |
| **consortiumManagementAccountPassword** | Mot de passe d’administration du consortium. Le mot de passe est utilisé pour rejoindre un consortium. |
| **ruleName** | Nom de la règle de mise en liste verte d'une plage d'adresses IP. Paramètre facultatif pour les règles de pare-feu.|
| **startIpAddress** | Début de la plage d’adresses IP pour la mise en liste verte. Paramètre facultatif pour les règles de pare-feu. |
| **endIpAddress** | Fin de la plage d’adresses IP pour la mise en liste verte. Paramètre facultatif pour les règles de pare-feu. |
| **skuName** | Niveau de service. Utilisez S0 pour Standard et B0 pour De base. |

## <a name="change-blockchain-member-password"></a>Modifier le mot de passe du membre blockchain

L’exemple modifie le mot de passe d’un membre blockchain.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service sont créées. |
| **name** | Nom qui identifie votre membre Azure Blockchain Service. |
| **mot de passe** | Mot de passe du compte du membre. Le mot de passe doit remplir trois des quatre conditions suivantes : il doit comprendre entre 12 et 72 caractères, 1 caractère minuscule, 1 caractère majuscule, 1 chiffre et 1 caractère spécial qui ne soit pas le signe dièze (#), le symbole de pourcentage (%), une virgule (,), une étoile (*), un guillemet inverse (\`), un guillemet double ("), un guillemet unique ('), un tiret (-) ou un point-virgule (;). |

## <a name="create-transaction-node"></a>Créer un nœud de transaction

Créez un nœud de transaction à l'intérieur d'un membre blockchain existant. L’ajout de nœuds de transaction vous permet d’augmenter l’isolation à des fins de sécurité et de répartir la charge. Par exemple, vous pouvez disposer d’un point de terminaison de nœud de transaction pour différentes applications clientes.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service sont créées. |
| **name** | Nom du membre Azure Blockchain Service qui inclut également le nom du nouveau nœud de transaction. |
| **location** | Région Azure dans laquelle le membre blockchain est créé. Par exemple : `eastus`. Choisissez l’emplacement le plus proche de vos utilisateurs ou de vos autres applications Azure. |
| **mot de passe** | Le mot de passe du nœud de transaction. Le mot de passe doit remplir trois des quatre conditions suivantes : il doit comprendre entre 12 et 72 caractères, 1 caractère minuscule, 1 caractère majuscule, 1 chiffre et 1 caractère spécial qui ne soit pas le signe dièze (#), le symbole de pourcentage (%), une virgule (,), une étoile (*), un guillemet inverse (\`), un guillemet double ("), un guillemet unique ('), un tiret (-) ou un point-virgule (;). |
| **ruleName** | Nom de la règle de mise en liste verte d'une plage d'adresses IP. Paramètre facultatif pour les règles de pare-feu. |
| **startIpAddress** | Début de la plage d’adresses IP pour la mise en liste verte. Paramètre facultatif pour les règles de pare-feu. |
| **endIpAddress** | Fin de la plage d’adresses IP pour la mise en liste verte. Paramètre facultatif pour les règles de pare-feu.|

## <a name="change-transaction-node-password"></a>Modifier le mot de passe du nœud de transaction

L’exemple modifie le mot de passe du nœud de transaction.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service existent. |
| **name** | Nom du membre Azure Blockchain Service qui inclut également le nom du nouveau nœud de transaction. |
| **mot de passe** | Le mot de passe du nœud de transaction. Le mot de passe doit remplir trois des quatre conditions suivantes : il doit comprendre entre 12 et 72 caractères, 1 caractère minuscule, 1 caractère majuscule, 1 chiffre et 1 caractère spécial qui ne soit pas le signe dièze (#), le symbole de pourcentage (%), une virgule (,), une étoile (*), un guillemet inverse (\`), un guillemet double ("), un guillemet unique ('), un tiret (-) ou un point-virgule (;). |

## <a name="change-consortium-management-account-password"></a>Modifier le mot de passe du compte de gestion de consortium

Le compte de gestion de consortium est utilisé pour la gestion des appartenances au consortium. Chaque membre est identifié de manière unique par un compte de gestion de consortium, et vous pouvez modifier le mot de passe de ce compte avec la commande suivante.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service sont créées. |
| **name** | Nom qui identifie votre membre Azure Blockchain Service. |
| **consortiumManagementAccountPassword** | Le mot de passe du compte de gestion de consortium. Le mot de passe doit remplir trois des quatre conditions suivantes : il doit comprendre entre 12 et 72 caractères, 1 caractère minuscule, 1 caractère majuscule, 1 chiffre et 1 caractère spécial qui ne soit pas le signe dièze (#), le symbole de pourcentage (%), une virgule (,), une étoile (*), un guillemet inverse (\`), un guillemet double ("), un guillemet unique ('), un tiret (-) ou un point-virgule (;). |
  
## <a name="update-firewall-rules"></a>Mettre à jour les règles de pare-feu

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service existent. |
| **name** | Nom du membre Azure Blockchain Service. |
| **ruleName** | Nom de la règle de mise en liste verte d'une plage d'adresses IP. Paramètre facultatif pour les règles de pare-feu.|
| **startIpAddress** | Début de la plage d’adresses IP pour la mise en liste verte. Paramètre facultatif pour les règles de pare-feu.|
| **endIpAddress** | Fin de la plage d’adresses IP pour la mise en liste verte. Paramètre facultatif pour les règles de pare-feu.|

## <a name="list-api-keys"></a>Répertorier les clés API

Les clés API peuvent être utilisées pour l'accès au nœud comme avec le nom d'utilisateur et le mot de passe. Il existe deux clés API pour prendre en charge la rotation des clés. Utilisez la commande suivante pour répertorier vos clés API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service existent. |
| **name** | Nom du membre Azure Blockchain Service qui inclut également le nom du nouveau nœud de transaction. |

## <a name="regenerate-api-keys"></a>Régénérer les clés API

Utilisez la commande suivante pour régénérer vos clés API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service existent. |
| **name** | Nom du membre Azure Blockchain Service qui inclut également le nom du nouveau nœud de transaction. |
| **keyName** | Remplacez \<keyValue\> par key1 ou key2. |

## <a name="delete-a-transaction-node"></a>Supprimer un nœud de transaction

L’exemple supprime le nœud de transaction d’un membre blockchain.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service existent. |
| **name** | Nom du membre Azure Blockchain Service qui inclut également le nom du nœud de transaction à supprimer. |

## <a name="delete-a-blockchain-member"></a>Supprimer un membre blockchain

L’exemple supprime un membre blockchain.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paramètre | Description |
|---------|-------------|
| **resource-group** | Nom du groupe de ressources dans lequel les ressources Azure Blockchain Service existent. |
| **name** | Nom du membre Azure Blockchain Service à supprimer. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Accorder l’accès à un utilisateur Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Paramètre | Description |
|---------|-------------|
| **role** | Nom du rôle Azure AD. |
| **assignee** | ID d'utilisateur Azure AD. Par exemple, `user@contoso.com` |
| **scope** | Étendue de l’attribution de rôle. Peut être un membre blockchain du nœud de la transaction. |

**Exemple :**

Accorder l’accès au nœud pour l’utilisateur Azure AD au **membre** blockchain :

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exemple :**

Accorder l’accès au nœud pour l’utilisateur Azure AD au **nœud de transaction** blockchain :

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Accorder l’accès au nœud au groupe Azure AD ou au rôle d'application

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Paramètre | Description |
|---------|-------------|
| **role** | Nom du rôle Azure AD. |
| **assignee-object-id** | ID de groupe ou ID d’application Azure AD. |
| **scope** | Étendue de l’attribution de rôle. Peut être un membre blockchain du nœud de la transaction. |

**Exemple :**

Accorder l’accès au nœud au **rôle d’application**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Supprimer l’accès au nœud Azure AD

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Paramètre | Description |
|---------|-------------|
| **role** | Nom du rôle Azure AD. |
| **assignee** | ID d'utilisateur Azure AD. Par exemple, `user@contoso.com` |
| **scope** | Étendue de l’attribution de rôle. Peut être un membre blockchain du nœud de la transaction. |

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [configurer des nœuds de transaction Azure Blockchain Service avec le portail Azure](configure-transaction-nodes.md).
