---
title: Tutoriel pour créer, générer et déployer des contrats intelligents - Azure Blockchain Service
description: Tutoriel expliquant comment utiliser le kit de développement Azure Blockchain pour l’extension Ethereum dans Visual Studio Code pour créer, générer et déployer un contrat intelligent dans Azure Blockchain Service.
ms.date: 11/20/2019
ms.topic: tutorial
ms.reviewer: chrisseg
ms.openlocfilehash: 2d2cb174656f5ed8f13d4463d416455ebb3f9ec9
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325166"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>Didacticiel : Créer, générer et déployer des contrats intelligents sur Azure Blockchain Service

Dans ce tutoriel, vous allez utiliser le kit de développement Azure Blockchain pour l’extension Ethereum dans Visual Studio Code pour créer, générer et déployer un contrat intelligent dans Azure Blockchain Service. Vous allez aussi utiliser Truffle pour exécuter une fonction de contrat intelligent via une transaction.

Vous utilisez Azure Blockchain Development Kit pour Ethereum afin de :

> [!div class="checklist"]
> * Créer un contrat intelligent
> * Déployer un contrat intelligent
> * Exécuter une fonction de contrat intelligent par le biais d’une transaction
> * Interroger l’état du contrat

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

* Effectuer l’étape [Démarrage rapide : Utiliser Visual Studio Code pour se connecter à un réseau de consortium Azure Blockchain Service](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure Blockchain Development Kit pour l’extension Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x ou ultérieur](https://nodejs.org/download)
* [Git 2.10.x ou ultérieur](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Ajoutez python.exe à votre chemin. La présence de Python version 2.7.15 dans votre chemin est obligatoire pour le Kit de développement Azure Blockchain.
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

Sur Windows, un compilateur C++ doit être installé pour le module node-gyp. Vous pouvez utiliser les outils MSBuild :

* Si Visual Studio 2017 est installé, configurez npm pour utiliser les outils MSBuild avec la commande `npm config set msvs_version 2017 -g`
* Si Visual Studio 2019 est installé, définissez le chemin des outils de build MS pour npm. Par exemple, `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Dans le cas contraire, installez les outils de build Visual Studio autonomes à l’aide de `npm install --global windows-build-tools` dans une interface de commande *Exécuter en tant qu’administrateur* avec élévation de privilèges.

Pour plus d’informations sur node-gyp, consultez le [dépôt node-gyp sur GitHub](https://github.com/node-gyp).

## <a name="create-a-smart-contract"></a>Créer un contrat intelligent

Azure Blockchain Development Kit pour Ethereum utilise des modèles de projet et des outils Truffle pour aider à structurer, générer et déployer des contrats. Avant de commencer, respectez les prérequis [Démarrage rapide : Utilisez Visual Studio Code pour vous connecter à un réseau de consortium Azure Blockchain Service](connect-vscode.md). Le guide de démarrage rapide vous aide à installer et à configurer pas à pas le kit de développement Azure Blockchain pour Ethereum.

1. À partir de la palette de commandes VS Code, choisissez **Azure Blockchain : Nouveau projet Solidity**.
1. Choisissez **Créer un projet de base**.
1. Créez un dossier nommé `HelloBlockchain` et **Sélectionnez un nouveau chemin de projet**.

Azure Blockchain Development Kit crée et initialise un nouveau projet Solidity pour vous. Le projet de base comprend un exemple de contrat intelligent **HelloBlockchain** et tous les fichiers nécessaires à la création et au déploiement sur votre membre de consortium dans Azure Blockchain Service. La création du projet peut prendre plusieurs minutes. Vous pouvez superviser la progression dans le panneau Terminal de VS Code en sélectionnant la sortie pour Azure Blockchain.

La structure du projet ressemble à l’exemple suivant :

   ![Projet Solidity](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Générer un contrat intelligent

Les contrats intelligents se trouvent dans le répertoire **contracts** du projet. Vous compilez les contrats intelligents avant de les déployer sur un blockchain. Utilisez la commande **Générer les contrats** pour compiler tous les contrats intelligents dans votre projet.

1. Dans la barre latérale de l’Explorateur de VS Code, développez le dossier **contracts** dans votre projet.
1. Cliquez avec le bouton droit sur **HelloBlockchain.sol**, puis choisissez **Générer les contrats** dans le menu.

    ![Générer les contrats](./media/send-transaction/build-contracts.png)

Azure Blockchain Development Kit utilise Truffle pour compiler les contrats intelligents.

![Compiler la sortie](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Déployer un contrat intelligent

Truffle utilise des scripts de migration pour déployer vos contrats sur un réseau Ethereum. Les migrations sont des fichiers JavaScript situés dans le répertoire **migrations** du projet.

1. Pour déployer votre contrat intelligent, cliquez avec le bouton droit sur **HelloBlockchain.sol** et choisissez **Déployer les contrats** dans le menu.
1. Choisissez votre réseau de consortium Azure Blockchain dans la palette de commandes. Le réseau blockchain de consortium a été ajouté au fichier de configuration Truffle du projet lorsque vous avez créé le projet.
1. Choisissez **Générer le mnémonique**. Choisissez un nom de fichier et enregistrez le fichier mnémonique dans le dossier du projet. Par exemple : `myblockchainmember.env`. Le fichier mnémonique est utilisé pour générer une clé privée Ethereum pour votre membre blockchain.

Azure Blockchain Development Kit utilise Truffle pour exécuter le script de migration et déployer les contrats dans le blockchain.

![Contrat correctement déployé](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Appeler une fonction de contrat

La fonction **SendRequest** du contrat **HelloBlockchain** modifie la variable d’état **RequestMessage**. La modification de l’état d’un réseau blockchain s’effectue par le biais d’une transaction. Vous pouvez créer un script pour exécuter la fonction **SendRequest** par le biais d’une transaction.

1. Créez un fichier à la racine de votre projet Truffle et nommez-le `sendrequest.js`. Ajoutez le code JavaScript Web3 suivant au fichier.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Quand Azure Blockchain Development Kit crée un projet, le fichier de configuration Truffle est généré avec les détails du point de terminaison de votre réseau blockchain de consortium. Ouvrez **truffle-config. js** dans votre projet. Le fichier de configuration liste deux réseaux : l’un nommé «development », l’autre portant le même nom que le consortium.
1. Dans le volet Terminal de VS Code, utilisez Truffle pour exécuter le script sur votre réseau blockchain de consortium. Dans la barre de menus du volet Terminal, sélectionnez l’onglet **Terminal**, et **PowerShell** dans la liste déroulante.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Remplacez le \<réseau blockchain\> par le nom du réseau blockchain défini dans le fichier **truffle-config. js**.

Truffle exécute le script sur votre réseau blockchain.

![Sortie du script](./media/send-transaction/execute-transaction.png)

Lorsque vous exécutez une fonction d’un contrat par le biais d’une transaction, cette transaction n’est traitée que lorsqu’un bloc est créé. Les fonctions destinées à être exécutées par l’intermédiaire d’une transaction retournent un ID de transaction au lieu d’une valeur renvoyée.

## <a name="query-contract-state"></a>Interroger l’état du contrat

Les fonctions de contrat intelligent peuvent retourner la valeur actuelle des variables d’état. Nous allons ajouter une fonction pour retourner la valeur d’une variable d’état.

1. Dans **HelloBlockchain.sol**, ajoutez une fonction **getMessage** au contrat intelligent **HelloBlockchain**.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    La fonction retourne le message stocké dans une variable d’état, selon l’état actuel du contrat.

1. Cliquez avec le bouton droit sur **HelloBlockchain.sol** et choisissez **Générer les contrats** dans le menu pour compiler les modifications apportées au contrat intelligent.
1. Pour déployer, cliquez avec le bouton droit sur **HelloBlockchain.sol** et choisissez **Déployer les contrats** dans le menu. Quand vous y êtes invité, choisissez votre réseau de consortium Azure Blockchain dans la palette de commandes.
1. Ensuite, créez un script pour appeler la fonction **getMessage**. Créez un fichier à la racine de votre projet Truffle et nommez-le `getmessage.js`. Ajoutez le code JavaScript Web3 suivant au fichier.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Dans le volet Terminal de VS Code, utilisez Truffle pour exécuter le script sur votre réseau blockchain. Dans la barre de menus du volet Terminal, sélectionnez l’onglet **Terminal**, et **PowerShell** dans la liste déroulante.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Remplacez le \<réseau blockchain\> par le nom du réseau blockchain défini dans le fichier **truffle-config. js**.

Le script interroge le contrat intelligent en appelant la fonction getMessage. La valeur actuelle de la variable d’état **RequestMessage** est retournée.

![Sortie du script](./media/send-transaction/execute-get.png)

Notez que la valeur n’est pas **Hello,blockchain!** . Un espace réservé est la valeur retournée à la place. Quand vous changez et déployez le contrat, celui-ci est déployé à une nouvelle adresse et des valeurs sont attribuées aux variables d’état dans le constructeur de contrat intelligent. Le script de migration **2_deploy_contracts. js** de l’exemple de Truffle déploie le contrat intelligent et passe une valeur d’espace réservé en tant qu’argument. Le constructeur affecte la variable d’état **RequestMessage** à la valeur d’espace réservé : c’est ce qui est retourné.

1. Pour définir la variable d’état **RequestMessage** et interroger la valeur, réexécutez les scripts **sendrequest.js** et **getmessage.js**.

    ![Sortie du script](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** définit la variable d’état **RequestMessage** sur **Hello, blockchain!** tandis que **getmessage.js** interroge le contrat pour obtenir la valeur de la variable d’état **RequestMessage** et retourne **Hello, blockchain!** .

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez supprimer les ressources en supprimant le groupe de ressources `myResourceGroup` que vous avez créé à l’étape du démarrage rapide *Créer un membre blockchain* effectuée en tant que prérequis.

Pour supprimer le groupe de ressources :

1. Dans le portail Azure, accédez à **Groupe de ressources** dans le volet de navigation de gauche et sélectionnez le groupe de ressources que vous souhaitez supprimer.
1. Sélectionnez **Supprimer le groupe de ressources**. Validez la suppression en entrant le nom du groupe de ressources, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un exemple de projet Solidity à l’aide d’Azure Blockchain Development Kit. Vous avez créé et déployé un contrat intelligent, puis appelé une fonction au moyen d’une transaction sur un réseau blockchain de consortium hébergé sur Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Développement d’applications de blockchain à l’aide du service Azure Blockchain](develop.md)
