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
ms.openlocfilehash: 8b1a701beac867c5f331ffa1ee1dee615961c6b3
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416290"
---
# <a name="quickstart-use-truffle-to-connect-to-an-azure-blockchain-service-network"></a>Démarrage rapide : Utiliser Truffle pour vous connecter à un réseau Azure Blockchain Service

Truffle est un environnement de développement blockchain que vous pouvez utiliser pour vous connecter à un nœud Azure Blockchain Service.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

* [Créer un membre Azure Blockchain](create-member.md)
* Installez [Truffle](https://github.com/trufflesuite/truffle). Truffle nécessite que plusieurs outils soient installés, notamment [Node.js](https://nodejs.org) et [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Installez [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python est nécessaire pour Web3.

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

1. Lancez la console de développement interactive de Truffle.

    ``` bash
    truffle develop
    ```

    Truffle crée une blockchain de développement locale et fournit une console interactive.

## <a name="connect-to-transaction-node"></a>Se connecter à un nœud de transaction

Utilisez *Web3* pour vous connecter au nœud de transaction. Vous pouvez obtenir la chaîne de connexion *Web3* à partir du portail Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez au membre Azure Blockchain Service. Sélectionnez **Nœuds de transaction**, puis le lien du nœud de transaction par défaut.

    ![Sélectionner le nœud de transaction par défaut](./media/connect-truffle/transaction-nodes.png)

1. Sélectionnez **Exemple de code > Web3**.
1. Copiez le code JavaScript situé sous **HTTPS (clé d’accès 1)** . Vous avez besoin du code pour la console de développement interactif Truffle.

    ![Code web3](./media/connect-truffle/web3-code.png)

1. Collez le code JavaScript de l’étape précédente dans la console de développement interactif Truffle. Le code crée un objet web3 connecté au nœud de transaction Azure Blockchain Service.

    Exemple de sortie :

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Vous pouvez appeler des méthodes sur l’objet **web3** pour interagir avec votre nœud de transaction.

1. Appelez la méthode **getBlockNumber** pour retourner le nombre actuel de blocs.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Exemple de sortie :

    ```bash
    truffle(develop)> web3.eth.getBlockNumber();
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