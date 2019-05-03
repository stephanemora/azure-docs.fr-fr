---
title: Configurer les nœuds de transaction de Service de Azure Blockchain
description: Comment configurer des nœuds de transaction de Service de Azure Blockchain
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dffeb81ae1eb244c38639a1241c0581e6fcdf94a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027959"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Configurer les nœuds de transaction de Service de Azure Blockchain

Pour interagir avec Azure Blockchain Service, vous le faire à travers la connexion à un ou plusieurs nœuds de transaction dans le membre de votre blockchain.  Pour interagir avec des nœuds de la transaction, vous devez configurer vos nœuds pour l’accès.

## <a name="prerequisites"></a>Conditions préalables

* [Créer un membre Azure Blockchain](create-member.md)

## <a name="transaction-node-overview"></a>Vue d’ensemble du nœud de transaction

Nœuds de transaction sont utilisés pour envoyer des transactions de blockchain sur Azure Blockchain Service via un point de terminaison public. Le nœud de transaction par défaut contient la clé privée du compte Ethereum inscrit sur la blockchain et par conséquent ne peut pas être supprimé.

Pour afficher les détails sur les nœud de transaction par défaut :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à votre membre Azure Blockchain Service. Sélectionnez **des nœuds de Transaction**.

    ![Sélectionnez le nœud de transaction par défaut](./media/configure-transaction-nodes/nodes.png)

    Détails de la vue d’ensemble incluent les adresses de point de terminaison public et la clé publique.

## <a name="create-transaction-node"></a>Créer le nœud de la transaction

Vous pouvez ajouter jusqu'à neuf nœuds de transactions supplémentaires à votre membre blockchain, pour un total de dix nœuds de transaction. En ajoutant des nœuds de la transaction, vous pouvez augmenter l’évolutivité ou répartir la charge. Par exemple, vous pourriez avoir un point de terminaison du nœud de transaction pour différentes applications clientes.

Pour ajouter un nœud de transactions :

1. Dans le portail Azure, accédez à votre membre Azure Blockchain Service et sélectionnez **nœuds de la Transaction > ajouter**.
1. Renseignez les paramètres pour le nouveau nœud de transaction.

    ![Ajouter un nœud de transaction](./media/configure-transaction-nodes/add-node.png)

    | Paramètre | Description |
    |---------|-------------|
    | Nom | Nom du nœud de transaction. Le nom est utilisé pour créer l’adresse DNS pour le point de terminaison de nœud de transaction. Par exemple : `newnode-myblockchainmember.blockchain.azure.com`. Impossible de modifier le nom du nœud qui a été créé. |
    | Mot de passe | Définir un mot de passe fort. Utilisez le mot de passe pour le point de terminaison du nœud de transaction avec l’authentification de base d’accès.

1. Sélectionnez **Créer**.

    Un nouveau nœud de transaction de l’approvisionnement prend environ 10 minutes. Frais de nœuds de transactions supplémentaires. Pour plus d’informations sur les coûts, consultez [tarification Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Points de terminaison

Nœuds de la transaction ont un nom DNS unique et les points de terminaison publics.

Pour afficher les détails du point de terminaison d’un nœud de transaction :

1. Dans le portail Azure, accédez à un de vos nœuds de transaction de membre Azure Blockchain Service et sélectionnez **vue d’ensemble**.

    ![Points de terminaison](./media/configure-transaction-nodes/endpoints.png)

Points de terminaison de nœud transaction sont sécurisés et exiger une authentification. Vous pouvez vous connecter à un point de terminaison de transaction à l’aide de l’authentification Azure AD, l’authentification de base HTTPS et à l’aide d’une clé d’accès via HTTPS ou Websocket via le protocole SSL.

### <a name="azure-active-directory-access-control"></a>Contrôle d'accès Azure Active Directory

Points de terminaison nœud Azure Blockchain Service transaction prend en charge l’authentification Azure Active Directory (Azure AD). Vous pouvez accorder utilisateur Azure AD, du groupe et du service principal l’accès à votre point de terminaison.

Pour accorder Azure de contrôle d’accès d’AD pour votre point de terminaison :

1. Dans le portail Azure, accédez à votre membre Azure Blockchain Service et sélectionnez **nœuds de la Transaction > contrôle d’accès (IAM) > Ajouter > Ajouter une attribution de rôle**.
1. Créer une nouvelle attribution de rôle pour un utilisateur, un groupe ou un principal de service (rôles d’application).

    ![Ajouter le rôle IAM](./media/configure-transaction-nodes/add-role.png)

    | Paramètre | Action |
    |---------|-------------|
    | Rôle | Sélectionnez **propriétaire**, **contributeur**, ou **lecteur**.
    | Attribuer l’accès à | Sélectionnez **utilisateur, groupe ou principal du service Azure AD**.
    | Sélectionnez | Recherchez l’utilisateur, un groupe ou un principal de service que vous souhaitez ajouter.

1. Sélectionnez **enregistrer** pour ajouter l’attribution de rôle.

Pour plus d’informations sur le contrôle d’accès Azure AD, consultez [gérer l’accès aux ressources Azure à l’aide de RBAC et le portail Azure](../../role-based-access-control/role-assignments-portal.md)

Pour plus d’informations sur la façon de se connecter à l’aide de l’authentification Azure AD, consultez [se connecter à votre nœud à l’aide de l’authentification AAD](configure-aad.md).

### <a name="basic-authentication"></a>Authentification de base

Pour l’authentification de base HTTPS, les informations d’identification du nom et mot de passe utilisateur sont passées dans l’en-tête HTTPS de la demande au point de terminaison.

Vous pouvez afficher les détails de point de terminaison de l’authentification de base d’un nœud de transaction dans le portail Azure. Accédez à un de vos nœuds de transaction de membre Azure Blockchain Service et sélectionnez **l’authentification de base** dans paramètres.

![Authentification de base](./media/configure-transaction-nodes/basic.png)

Le nom d’utilisateur est le nom de votre nœud et ne peut pas être modifié.

Pour utiliser l’URL, remplacez \<mot de passe\> avec le mot de passe défini lorsque le nœud a été configuré. Vous pouvez mettre à jour le mot de passe en sélectionnant **réinitialisation de mot de passe**.

### <a name="access-keys"></a>Clés d’accès

Clé d’authentification d’accès, la clé d’accès est incluse dans l’URL de point de terminaison. Lorsque le nœud de la transaction est approvisionné, deux clés d’accès sont générés. Une clé d’accès peut être utilisé pour l’authentification. Activer Modifier et de la rotation des clés de deux clés.

Vous pouvez afficher les détails de la clé d’un nœud de transactions access et copier les adresses de point de terminaison qui incluent les clés d’accès. Accédez à un de vos nœuds de transaction de membre Azure Blockchain Service et sélectionnez **clés d’accès** dans paramètres.

### <a name="firewall-rules"></a>Règles de pare-feu

Règles de pare-feu permettent de limiter les adresses IP qui peuvent tenter de s’authentifier sur le nœud de votre transaction.  Si aucune règle de pare-feu n’est configurés pour le nœud de votre transaction, il ne sont pas accessibles par un tiers.  

Pour afficher les règles de pare-feu d’un nœud de transaction, accédez à un de vos nœuds de transaction de membre Azure Blockchain Service, puis sélectionnez **règles de pare-feu** dans paramètres.

Vous pouvez ajouter des règles de pare-feu en entrant un nom de règle, adresse IP de départ adresse et une fin des adresses IP dans le **règles de pare-feu** grille.

![Règles de pare-feu](./media/configure-transaction-nodes/firewall-rules.png)

Pour activer :

* **Adresse IP unique :** Configurer la même adresse IP de début et de fin des adresses IP.
* **Plage d’adresses IP :** Configurer le début et fin de plage d’adresses IP. Par exemple, une plage commençant à 10.221.34.0 et en terminant à 10.221.34.255 permettrait le sous-réseau 10.221.34.xxx entière.
* **Autoriser toutes les adresses IP :** Configurer l’adresse IP de début sur 0.0.0.0 et 255.255.255.255 l’adresse de fin.

## <a name="connection-strings"></a>Chaînes de connexion

Syntaxe de chaîne de connexion pour le nœud de votre transaction est fournie pour basic authentication ou à l’aide de clés d’accès. Chaînes de connexion, y compris les clés d’accès via HTTPS et WebSocket sont fournies.

Vous pouvez afficher les chaînes de connexion d’un nœud de transaction et copier les adresses de point de terminaison. Accédez à un de vos nœuds de transaction de membre Azure Blockchain Service et sélectionnez **chaînes de connexion** dans paramètres.

![Chaînes de connexion](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Exemple de code

Exemple de code est fourni afin d’activer rapidement la connexion à votre nœud de transactions via Web3, Nethereum, Web3js et Truffle.

Vous pouvez afficher le code de connexion d’exemple d’un nœud de transaction et copiez-le à utiliser avec les outils de développement les plus courants. Accédez à un de vos nœuds de transaction de membre Azure Blockchain Service et sélectionnez **exemple de Code** dans paramètres.

Choisissez l’onglet Web3 ou Nethereum pour afficher l’exemple de code que vous souhaitez utiliser.

![Exemple de code](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer des nœuds de transaction à l’aide d’Azure CLI](manage-cli.md)
