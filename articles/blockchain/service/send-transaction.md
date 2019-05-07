---
title: Envoyer des transactions à l’aide du service Azure Blockchain
description: Tutoriel sur l’utilisation du service Azure Blockchain pour déployer un contrat intelligent et envoyer une transaction privée.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: d3ad4cdfe33948c04c278ed3dfef7aa6fda637ab
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027495"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>Didacticiel : Envoyer des transactions à l’aide du service Azure Blockchain

Dans ce tutoriel, vous allez créer des nœuds de transaction pour tester la confidentialité d’un contrat et d’une transaction.  Vous allez utiliser Truffle pour créer un environnement de développement local afin de déployer un contrat intelligent et envoyer une transaction privée.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Ajouter des nœuds de transaction
> * Utiliser Truffle pour déployer un contrat intelligent
> * Envoyer une transaction
> * Valider la confidentialité de la transaction

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

* Effectuer l’étape [Créer un membre de blockchain à l’aide du portail Azure](create-member.md)
* Effectuer l’étape [Démarrage rapide : Utiliser Truffle pour se connecter à un réseau de consortium](connect-truffle.md)
* Truffle nécessite d’installer plusieurs outils comme [Node.js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) et [Truffle](https://github.com/trufflesuite/truffle).

    Pour une installation rapide sur Windows 10, installez [Ubuntu sur Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6) pour un terminal d’interpréteur de commandes Bash Unix, puis installez [Truffle](https://github.com/trufflesuite/truffle). La distribution Ubuntu sur Windows inclut Node.js et Git.

* Installer [Visual Studio Code](https://code.visualstudio.com/Download)
* Installer l’[extension Visual Studio Code Solidity](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)

## <a name="create-transaction-nodes"></a>Créer des nœuds de transaction

Par défaut, vous n’avez qu’un seul nœud de transaction. Nous allons en ajouter deux autres. L’un des nœuds participe à la transaction privée. L’autre n’est pas inclus dans la transaction privée.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à votre membre Azure Blockchain et sélectionnez **Nœuds de transaction > Ajouter**.
1. Complétez les paramètres d’un nouveau nœud de transaction nommé `alpha`.

    ![Créer un nœud de transaction](./media/send-transaction/create-node.png)

    | Paramètre | Valeur | Description |
    |---------|-------|-------------|
    | Nom | `alpha` | Nom du nœud de transaction. Le nom est utilisé pour créer l’adresse DNS du point de terminaison du nœud de transaction. Par exemple : `alpha-mymanagedledger.blockchain.azure.com`. |
    | Mot de passe | Mot de passe fort | Le mot de passe est utilisé pour accéder au point de terminaison du nœud de transaction avec une authentification de base.

1. Sélectionnez **Créer**.

    Le provisionnement d’un nouveau nœud de transaction prend environ 10 minutes.

1. Répétez les étapes 2 à 4 pour ajouter un nœud de transaction nommé `beta`.

Vous pouvez poursuivre le tutoriel pendant que les nœuds sont en cours de provisionnement. Une fois le provisionnement terminé, vous avez trois nœuds de transaction.

## <a name="open-truffle-project"></a>Ouvrir un projet Truffle

1. Ouvrez un terminal d’interpréteur de commandes Bash.
1. Modifiez le chemin du répertoire du projet Truffle à partir du prérequis [Démarrage rapide : Utiliser Truffle pour se connecter à un réseau de consortium](connect-truffle.md). Par exemple,

    ```bash
    cd truffledemo
    ```

1. Lancez la console de développement interactive de Truffle.

    ``` bash
    truffle develop
    ```

    Truffle crée une blockchain de développement locale et fournit une console interactive.

## <a name="connect-to-transaction-node"></a>Se connecter au nœud de transaction

Utilisez Web3 pour vous connecter au nœud de transaction par défaut et créer un compte. Vous pouvez obtenir la chaîne de connexion Web3 à partir du portail Azure.

1. Dans le portail Azure, accédez au nœud de transaction par défaut et sélectionnez **Nœuds de transaction > Exemple de code > Web3**.
1. Copiez le code JavaScript de l’![exemple de code Web3](./media/send-transaction/web3-code.png) **HTTPS (clé d’accès 1)**.

1. Collez le code JavaScript Web3 du nœud de transaction par défaut dans la console de développement interactive de Truffle. Le code crée un objet Web3 connecté au nœud de transaction du service Azure Blockchain.

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Vous pouvez appeler des méthodes sur l’objet Web3 pour interagir avec votre nœud de transaction.

1. Créez un compte sur le nœud de transaction par défaut. Remplacez le paramètre de mot de passe par votre propre mot de passe fort.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    Notez l’adresse du compte retournée et le mot de passe utilisé pour la section suivante.

1. Quittez l’environnement de développement Truffle.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Configurer le projet Truffle

Pour configurer le projet Truffle, vous avez besoin d’obtenir certaines informations sur le nœud de transaction à partir du portail Azure.

### <a name="transaction-node-public-key"></a>Clé publique de nœud de transaction

Chaque nœud de transaction a une clé publique. Cette clé publique vous permet d’envoyer une transaction privée au nœud. Pour envoyer une transaction à partir du nœud de transaction par défaut au nœud de transaction *alpha*, vous avez besoin de la clé publique du nœud de transaction *alpha*.

Vous pouvez obtenir cette clé publique à partir de la liste des nœuds de transaction. Copiez la clé publique du nœud alpha et enregistrez la valeur pour l’utiliser plus tard dans ce tutoriel.

![Liste des nœuds de transaction](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>Adresses des points de terminaison de nœud de transaction

1. Dans le portail Azure, accédez à chaque nœud de transaction et sélectionnez **Nœuds de transaction > Chaînes de connexion**.
1. Copiez et enregistrez l’URL du point de terminaison à partir de **HTTPS (clé d’accès 1) pour chaque nœud de transaction. Vous aurez besoin des adresses des points de terminaison pour le fichier de configuration du contrat intelligent, plus tard dans ce tutoriel.

    ![Adresse de point de terminaison de transaction](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Modifier le fichier de configuration

1. Lancez Visual Studio Code et ouvrez le dossier du répertoire du projet Truffle à l’aide du menu **Fichier > Dossier ouvert**.
1. Ouvrez le fichier de configuration Truffle `truffle-config.js`.
1. Remplacez le contenu du fichier par les informations de configuration suivantes. Ajoutez des variables contenant les adresses des points de terminaison et les informations de compte. Remplacez les sections entre crochets par les valeurs que vous avez collectées dans les sections précédentes.

``` javascript
var defaultnode = "<default transaction node connection string>";
var alpha = "<alpha transaction node connection string>";
var beta = "<beta transaction node connection string>";

var myAccount = "<account address>";
var myPassword = "<account password>";

var Web3 = require("web3");
```

Ajoutez le code de configuration à la section **module.exports** de la configuration.

```javascript
module.exports = {
  networks: {
    defaultnode: {
      provider:(() =>  {
      const AzureBlockchainProvider = new Web3.providers.HttpProvider(defaultnode);

      const web3 = new Web3(AzureBlockchainProvider);
      web3.eth.personal.unlockAccount(myAccount, myPassword);

      return AzureBlockchainProvider;
      })(),

      network_id: "*",
      gas: 0,
      gasPrice: 0,
      from: myAccount
    },
    alpha: {
      provider: new Web3.providers.HttpProvider(alpha),
      network_id: "*",
      gas: 0,
      gasPrice: 0
    },
    beta: {
      provider: new Web3.providers.HttpProvider(beta),
      network_id: "*",
      gas: 0,
      gasPrice: 0
    }
  }
}
```

## <a name="create-smart-contract"></a>Créer un contrat intelligent

Dans le dossier **contracts**, créez un fichier nommé `SimpleStorage.sol`. Ajoutez le code suivant.

```solidity
pragma solidity >=0.4.21 <0.6.0;

contract SimpleStorage {
    string public storedData;

    constructor(string memory initVal) public {
        storedData = initVal;
    }

    function set(string memory x) public {
        storedData = x;
    }

    function get() view public returns (string memory retVal) {
        return storedData;
    }
}
```

Dans le dossier **migrations**, créez un fichier nommé `2_deploy_simplestorage.js`. Ajoutez le code suivant.

```solidity
var SimpleStorage = artifacts.require("SimpleStorage.sol");

module.exports = function(deployer) {

  // Pass 42 to the contract as the first constructor parameter
  deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Account address>"})  
};
```

Remplacez les valeurs entre crochets.

| Valeur | Description
|-------|-------------
| \<clé publique du nœud alpha\> | Clé publique du nœud alpha.
| \<Adresse du compte\> | Adresse du compte créé dans le nœud de transaction par défaut.

Dans cet exemple, la valeur initiale de **storeData** est 42.

**privateFor** définit les nœuds pour lesquels le contrat est disponible. Dans cet exemple, le compte du nœud de transaction par défaut peut caster des transactions privées vers le nœud **alpha**. Vous devez ajouter des clés publiques pour tous les participants aux transactions privées. Si vous n’incluez pas **privateFor:** et **from:**, les transactions de contrat intelligent sont publiques et tous les membres du consortium peuvent les voir.

Enregistrez tous les fichiers en sélectionnant **Fichier > Enregistrer tout**.

## <a name="deploy-smart-contract"></a>Déployer un contrat intelligent

Utilisez Truffle pour déployer `SimpleStorage.sol` sur le réseau de nœuds de transaction par défaut.

```bash
truffle migrate --network defaultnode
```

Truffle commence par compiler, puis déployer le contrat intelligent **SimpleStorage**.

Exemple de sortie :

```
pat@DESKTOP:/mnt/c/truffledemo$ truffle migrate --network defaultnode

2_deploy_simplestorage.js
=========================

   Deploying 'SimpleStorage'
   -------------------------
   > transaction hash:    0x3f695ff225e7d11a0239ffcaaab0d5f72adb545912693a77fbfc11c0dbe7ba72
   > Blocks: 2            Seconds: 12
   > contract address:    0x0b15c15C739c1F3C1e041ef70E0011e641C9D763
   > account:             0x1a0B9683B449A8FcAd294A01E881c90c734735C3
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

## <a name="validate-contract-privacy"></a>Valider la confidentialité du contrat

En raison de la confidentialité du contrat, il est uniquement possible d’interroger ses valeurs à partir des nœuds que nous avons déclarés dans **privateFor**. Dans cet exemple, nous pouvons interroger le nœud de transaction par défaut, car le compte existe dans ce nœud. À l’aide de la console Truffle, connectez-vous au nœud de transaction par défaut.

```bash
truffle console --network defaultnode
```

Exécutez une commande qui retourne la valeur de l’instance du contrat.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Si l’interrogation du nœud de transaction par défaut réussit, la valeur 42 est retournée.

Exemple de sortie :

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network defaultnode
truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
'42'
```

Quittez la console.

```bash
.exit
```

Étant donné que nous avons déclaré la clé publique du nœud **alpha** dans **privateFor**, nous pouvons interroger le nœud **alpha**. À l’aide de la console Truffle, connectez-vous au nœud **alpha**.

```bash
truffle console --network alpha
```

Exécutez une commande qui retourne la valeur de l’instance du contrat.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Si l’interrogation du nœud **alpha** réussit, la valeur 42 est retournée.

Exemple de sortie :

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network alpha
truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
'42'
```

Quittez la console.

```bash
.exit
```

Étant donné que nous n’avons pas déclaré la clé publique du nœud **bêta** dans **privateFor**, nous ne pouvons pas interroger le nœud **bêta** en raison de la confidentialité du contrat. À l’aide de la console Truffle, connectez-vous au nœud **bêta**.

```bash
truffle console --network beta
```

Exécutez une commande qui retourne la valeur de l’instance du contrat.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

L’interrogation du nœud **bêta** échoue, car le contrat est privé.

Exemple de sortie :

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network beta
truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
Thrown:
Error: Returned values aren't valid, did it run Out of Gas?
    at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
    at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
    at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
    at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
```

Quittez la console.

```bash
.exit
```

## <a name="send-a-transaction"></a>Envoyer une transaction

Création d’un fichier appelé `sampletx.js`. Enregistrez-le à la racine de votre projet.

Ce script définit la valeur de la variable **storedData** du contrat sur 65. Ajoutez le code au nouveau fichier.

```javascript
var SimpleStorage = artifacts.require("SimpleStorage");

module.exports = function(done) {
  console.log("Getting deployed version of SimpleStorage...")
  SimpleStorage.deployed().then(function(instance) {
    console.log("Setting value to 65...");
    return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Account address>"});
  }).then(function(result) {
    console.log("Transaction:", result.tx);
    console.log("Finished!");
    done();
  }).catch(function(e) {
    console.log(e);
    done();
  });
};
```

Remplacez les valeurs entre crochets, puis enregistrez le fichier.

| Valeur | Description
|-------|-------------
| \<clé publique du nœud alpha\> | Clé publique du nœud alpha.
| \<Adresse du compte\> | Adresse du compte créé dans le nœud de transaction par défaut.

**privateFor** définit les nœuds pour lesquels le contrat est disponible. Dans cet exemple, le compte du nœud de transaction par défaut peut caster des transactions privées vers le nœud **alpha**. Vous devez ajouter des clés publiques pour tous les participants aux transactions privées.

Utilisez Truffle pour exécuter le script pour le nœud de transaction par défaut.

```bash
truffle exec sampletx.js --network defaultnode
```

Exécutez une commande qui retourne la valeur de l’instance du contrat.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Si la transaction est réussie, la valeur 65 est retournée.

Exemple de sortie :

```
Getting deployed version of SimpleStorage...
Setting value to 65...
Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
Finished!
```

Quittez la console.

```bash
.exit
```

## <a name="validate-transaction-privacy"></a>Valider la confidentialité de la transaction

En raison de la confidentialité des transactions, celles-ci ne peuvent être effectuées que sur les nœuds que nous avons déclarés dans **privateFor**. Dans cet exemple, nous pouvons effectuer des transactions puisque nous avons déclaré la clé publique du nœud **alpha** dans **privateFor**. Utilisez Truffle pour exécuter la transaction sur le nœud **alpha**.

```bash
truffle exec sampletx.js --network alpha
```

Exécutez une commande qui retourne la valeur de l’instance du contrat.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Si la transaction est réussie, la valeur 65 est retournée.

Exemple de sortie :

```
Getting deployed version of SimpleStorage...
Setting value to 65...
Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
Finished!
```

Quittez la console.

```bash
.exit
```

Dans ce tutoriel, vous avez ajouté deux nœuds de transaction pour démontrer la confidentialité d’un contrat et d’une transaction. Vous avez utilisé le nœud par défaut pour déployer un contrat intelligent privé. Vous avez testé la confidentialité en interrogeant des valeurs de contrat et en exécutant des transactions sur la blockchain.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez supprimer les ressources en supprimant le groupe de ressources `myResourceGroup` créé par le service Azure Blockchain.

Pour supprimer le groupe de ressources :

1. Dans le portail Azure, accédez à **Groupe de ressources** dans le volet de navigation de gauche et sélectionnez le groupe de ressources que vous souhaitez supprimer.
1. Sélectionnez **Supprimer le groupe de ressources**. Validez la suppression en entrant le nom du groupe de ressources, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Développement d’applications de blockchain à l’aide du service Azure Blockchain](develop.md)
