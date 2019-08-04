---
title: Se connecter à l’aide de Truffle
description: Se connecter à un réseau Azure Blockchain Service à l’aide de Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 9154bc749f7db337de67f501d5e5049dfd466156
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698467"
---
# <a name="quickstart-use-truffle-to-connect-to-an-azure-blockchain-service-network"></a>Démarrage rapide : Utiliser Truffle pour vous connecter à un réseau Azure Blockchain Service

Truffle est un environnement de développement blockchain que vous pouvez utiliser pour vous connecter à un nœud Azure Blockchain Service.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

* [Créer un membre Azure Blockchain](create-member.md)
* Installez [Truffle](https://github.com/trufflesuite/truffle). Truffle nécessite que plusieurs outils soient installés, notamment [Node.js](https://nodejs.org) et [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Installez [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python est nécessaire pour Web3.
* Installez [Visual Studio Code](https://code.visualstudio.com/download).
* Installez l’[extension Solidity de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity).

## <a name="create-truffle-project"></a>Créer un projet Truffle

1. Ouvrez un interpréteur de commandes ou une invite de commandes Node.js.
1. Sélectionnez le répertoire dans lequel vous souhaitez créer le projet Truffle.
1. Créez un répertoire pour le projet, puis remplacez le chemin par celui du nouveau répertoire. Par exemple,

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Initialisez le projet Truffle.

    ``` bash
    truffle init
    ```

1. Installez l’API Ethereum JavaScript web3 dans le dossier du projet. La version 1.0.0-beta.37 de web3 est nécessaire.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    Vous pouvez recevoir des avertissements de npm lors de l’installation.
    
## <a name="configure-truffle-project"></a>Configurer le projet Truffle

Pour configurer le projet Truffle, vous avez besoin d’obtenir certaines informations sur le nœud de transaction à partir du portail Azure.

### <a name="transaction-node-endpoint-addresses"></a>Adresses des points de terminaison de nœud de transaction

1. Dans le portail Azure, accédez à chaque nœud de transaction et sélectionnez **Nœuds de transaction > Chaînes de connexion**.
1. Copiez et enregistrez l’URL du point de terminaison à partir de **HTTPS (clé d’accès 1)** pour chaque nœud de transaction. Vous aurez besoin des adresses des points de terminaison pour le fichier de configuration du contrat intelligent, plus tard dans ce tutoriel.

    ![Adresse de point de terminaison de transaction](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Modifier le fichier de configuration

1. Lancez Visual Studio Code et ouvrez le dossier du répertoire du projet Truffle à l’aide du menu **Fichier > Dossier ouvert**.
1. Ouvrez le fichier de configuration Truffle `truffle-config.js`.
1. Remplacez le contenu du fichier par les informations de configuration suivantes. Ajoutez une variable contenant l’adresse du point de terminaison. Remplacez les le contenu entre crochets par des valeurs que vous avez collectées dans les sections précédentes.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";   
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider: new Web3.providers.HttpProvider(defaultnode),
          network_id: "*"
        }
      }
    }
    ```

1. Enregistrez les changements apportés à `truffle-config.js`.

## <a name="connect-to-transaction-node"></a>Se connecter à un nœud de transaction

Utilisez *Web3* pour vous connecter au nœud de transaction.

1. Utilisez la console Truffle pour vous connecter au nœud de transaction par défaut.

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle se connecte au nœud de transaction par défaut et fournit une console interactive.

    Vous pouvez appeler des méthodes sur l’objet **web3** pour interagir avec votre nœud de transaction.

1. Appelez la méthode **getBlockNumber** pour retourner le nombre actuel de blocs.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Exemple de sortie :

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Quittez la console de développement Truffle.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un projet Truffle pour vous connecter à votre nœud de transaction Azure Blockchain Service par défaut.

Essayez le tutoriel suivant pour envoyer une transaction au réseau blockchain de votre consortium à l’aide de Truffle.

> [!div class="nextstepaction"]
> [Envoyer une transaction](send-transaction.md)
