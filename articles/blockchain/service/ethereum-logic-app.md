---
title: Guide pratique pour utiliser le connecteur Ethereum Blockchain avec Azure Logic Apps
description: Découvrez comment utiliser le connecteur Ethereum Blockchain avec Azure Logic Apps pour déclencher des fonctions de contrat intelligent et répondre à des événements de contrat intelligent.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/26/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 48f862bea6df50480ce4f9d21993693da34cfbc9
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720672"
---
# <a name="how-to-use-ethereum-blockchain-connector-with-azure-logic-apps"></a>Guide pratique pour utiliser le connecteur Ethereum Blockchain avec Azure Logic Apps

Utilisez le [connecteur Ethereum Blockchain](https://docs.microsoft.com/connectors/blockchainethereum/) avec [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) pour déclencher des fonctions de contrat intelligent et répondre à des événements de contrat intelligent. Par exemple, vous souhaitez créer un microservice basé sur REST qui retourne des informations à partir d’un registre blockchain. À l’aide d’une application logique, vous pouvez accepter les requêtes HTTP qui interrogent les informations stockées dans un registre blockchain.

## <a name="prerequisites"></a>Prérequis

* Effectuez le guide de démarrage rapide prérequis facultatif intitulé [Démarrage rapide : Utilisez Visual Studio Code pour vous connecter à un réseau de consortium Azure Blockchain Service](connect-vscode.md). Le guide de démarrage rapide vous guide tout au long de l’installation de l’[Azure Blockchain Development Kit for Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) et de la configuration de votre environnement de développement blockchain.

## <a name="create-a-logic-app"></a>Créer une application logique

Azure Logic Apps vous aide à planifier et à automatiser les processus et workflows d’entreprise quand vous avez besoin d’intégrer des systèmes et des services. Tout d’abord, vous créez une logique qui utilise le connecteur Ethereum Blockchain.

1. Dans le [portail Azure](https://portal.azure.com), choisissez **Créer une ressource > Intégration > Application logique**.
1. Sous **Créer une application logique**, indiquez où vous souhaitez créer votre application logique. Sélectionnez **Créer** quand vous avez terminé.

    Pour plus d’informations sur la création d’applications logiques Azure, consultez [Créer des applications logiques Azure](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Une fois qu’Azure a déployé votre application, sélectionnez votre ressource d’application logique.
1. Dans le Concepteur Logic Apps, sous **Modèles**, choisissez **Application logique vide**.

Chaque application logique doit démarrer avec un déclencheur, qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur s’active, le moteur Logic Apps crée une instance d’application logique qui démarre et exécute votre flux de travail.

Le connecteur Ethereum Blockchain a un déclencheur et plusieurs actions. Le déclencheur ou l’action que vous utilisez dépend de votre scénario.

Choisissez l’une des sections suivantes si votre workflow :

* Se déclenche quand un événement se produit sur la blockchain : [Utiliser le déclencheur d’événements](#use-the-event-trigger).
* Interroge ou déploie un contrat intelligent : [Utiliser des actions](#use-actions).
* Suit un scénario courant : [Générer un workflow à l’aide du kit de développement](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>Utiliser le déclencheur d’événements

Utilisez les déclencheurs d’événements Ethereum Blockchain quand vous souhaitez qu’une application logique s’exécute après un événement de contrat intelligent. Par exemple, vous souhaitez envoyer un e-mail quand une fonction de contrat intelligent est appelée.

1. Dans le Concepteur Logic Apps, choisissez le connecteur Ethereum Blockchain.
1. Sous l’onglet **Déclencheurs**, choisissez **Quand un événement de contrat intelligent se produit**.
1. Modifiez ou [créez une connexion d’API](#create-an-api-connection) à votre Azure Blockchain Service.
1. Entrez les détails sur le contrat intelligent dont vous souhaitez vérifier les événements.

    ![Propriétés de déclencheur d’événements](./media/ethereum-logic-app/event-properties.png)

    | Propriété | Description |
    |----------|-------------|
    | **Contract ABI** | L’interface ABI (Application Binary Interface) du contrat définit les interfaces du contrat intelligent. Découvrez comment [obtenir l’ABI du contrat](#get-contract-abi). |
    | **Smart contract address** | L’adresse du contrat est l’adresse de destination du contrat intelligent sur la blockchain Ethereum. Découvrez comment [obtenir l’adresse du contrat](#get-contract-address). |
    | **Event name** | Choisissez un événement de contrat intelligent à vérifier. L’événement déclenche l’application logique. |
    | **Interval** et **Frequency** | Choisissez la fréquence à laquelle vous souhaitez vérifier l’événement. |

1. Sélectionnez **Enregistrer**.

Pour terminer votre application logique, vous pouvez ajouter une nouvelle étape qui effectue une action basée sur le déclencheur d’événements Ethereum blockchain, par exemple envoyer un e-mail.

## <a name="use-actions"></a>Utiliser des actions

Utilisez les actions Ethereum Blockchain quand vous souhaitez qu’une application logique effectue une action sur le registre Blockchain. Par exemple, vous souhaitez créer un microservice basé sur REST qui appelle une fonction de contrat intelligent quand une requête HTTP est effectuée sur une application logique.

Les actions de connecteur nécessitent un déclencheur. Vous pouvez utiliser une action de connecteur Ethereum Blockchain comme étape suivante après un déclencheur. Par exemple, un déclencheur de requête HTTP pour un microservice.

1. Dans le Concepteur Logic Apps, sélectionnez **Nouvelle étape** à la suite d’un déclencheur.
1. Choisissez le connecteur Ethereum Blockchain.
1. Sous l’onglet **Actions**, choisissez l’une des actions disponibles.

    ![Propriétés des actions](./media/ethereum-logic-app/action-properties.png)

1. Modifiez ou [créez une connexion d’API](#create-an-api-connection) à votre Azure Blockchain Service.
1. En fonction de l’action choisie, fournissez les informations suivantes sur votre fonction de contrat intelligent.

    | Propriété | Description |
    |----------|-------------|
    | **Contract ABI** | L’interface ABI (Application Binary Interface) du contrat définit les interfaces du contrat intelligent. Découvrez comment [obtenir l’ABI du contrat](#get-contract-abi). |
    | **Contract bytecode** | Bytecode de contrat intelligent compilé. Découvrez comment [obtenir le bytecode du contrat](#get-contract-bytecode). |
    | **Smart contract address** | L’adresse du contrat est l’adresse de destination du contrat intelligent sur la blockchain Ethereum. Découvrez comment [obtenir l’adresse du contrat](#get-contract-address). |
    | **Smart contract function name** | Choisissez le nom de la fonction de contrat intelligent pour l’action. La liste est renseignée à partir des détails figurant dans le contrat ABI. |

    Une fois que vous avez choisi un nom de fonction de contrat intelligent, il est possible que des champs obligatoires pour les paramètres de fonction apparaissent. Entrez les valeurs ou le contenu dynamique requis pour votre scénario.

Vous pouvez maintenant utiliser votre application logique. Quand l’événement d’application logique est déclenché, l’action Ethereum Blockchain s’exécute. Par exemple, un déclencheur de requête HTTP exécute une action Ethereum Blockchain pour interroger une valeur d’état de contrat intelligent, ce qui entraîne une réponse HTTP qui retourne la valeur.

## <a name="generate-a-workflow"></a>Générer un workflow

L’extension Visual Studio Code Azure Blockchain Development Kit for Ethereum peut générer des workflows d’application logique pour les scénarios courants. Quatre scénarios sont disponibles :

* Publication de données dans une base de données Azure SQL
* Publication d’événements sur Azure Event Grid ou Azure Service Bus
* Publication de rapports
* Microservice basé sur REST

 Azure Blockchain Development Kit utilise Truffle pour simplifier le développement de blockchain. Pour générer une application logique basée sur un contrat intelligent, vous avez besoin d’une solution Truffle pour le contrat intelligent. Vous avez également besoin d’une connexion à votre réseau de consortium Azure Blockchain Service. Pour plus d’informations, consultez [Démarrage rapide : Utiliser Visual Studio Code pour se connecter à un réseau du consortium Azure Blockchain Service](connect-vscode.md).

Par exemple, les étapes suivantes génèrent une application logique de microservice REST basée sur le contrat intelligent **HelloBlockchain** du guide de démarrage rapide :

1. Dans la barre latérale de l’Explorateur de VS Code, développez le dossier **contracts** dans votre solution.
1. Cliquez avec le bouton droit sur **HelloBlockchain.sol** et choisissez **Generate microservices for smart contracts** (Générer des microservices pour les contrats intelligents) dans le menu.

    ![Générer une application logique](./media/ethereum-logic-app/generate-logic-app.png)

1. Dans la palette de commandes, choisissez **Application logique**.
1. Entrez l’**adresse du contrat**. Pour plus d’informations, consultez [Obtenir l’adresse du contrat](#get-contract-address).
1. Choisissez l’abonnement Azure et le groupe de ressources pour l’application logique.

    La configuration de l’application logique et les fichiers de code sont générés dans le répertoire **generatedLogicApp**.

1. Affichez le répertoire **generatedLogicApp/HelloBlockchain**. Il existe un fichier JSON d’application logique pour chaque fonction, événement et propriété de contrat intelligent.
1. Ouvrez le fichier **generatedLogicApp/HelloBlockchain/Service/property.RequestMessage.logicapp.json** et copiez le contenu.

    ![JSON pour la propriété RequestMessage](./media/ethereum-logic-app/requestmessage.png)

1. Dans votre application logique, sélectionnez **Affichage du code de l’application logique**. Remplacez le JSON existant par le JSON d’application logique généré.

    ![Remplacer la configuration de l’application logique en mode Code](./media/ethereum-logic-app/code-view.png)

1. Sélectionnez **Concepteur** pour basculer vers le mode Concepteur.
1. L’application logique comprend les étapes de base du scénario. Toutefois, vous devez mettre à jour les détails de configuration du connecteur Ethereum Blockchain.
1. Sélectionnez l’étape **Connections**, puis modifiez ou [créez une connexion d’API](#create-an-api-connection) à votre Azure Blockchain Service.

    ![Application logique de microservice](./media/ethereum-logic-app/microservice-logic-app.png)

1. Vous pouvez maintenant utiliser votre application logique. Pour tester le microservice basé sur REST, émettez une requête HTTP POST à l’URL de requête de l’application logique. Copiez l’**URL HTTP POST** à partir de l’étape **When a HTTP request is received** (Quand une requête HTTP est reçue).

    ![URL HTTP POST](./media/ethereum-logic-app/post-url.png)

1. Utilisez cURL pour créer une requête HTTP POST. Remplacez le texte de l’espace réservé **\<HTTP POST URL\>** par l’URL obtenue à l’étape précédente.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    La commande cURL retourne une réponse à partir de l’application logique. En l’occurrence, la sortie de la fonction de contrat intelligent **RequestMessage**.

    ![Sortie de la propriété RequestMessage](./media/ethereum-logic-app/curl.png)

Pour plus d’informations sur l’utilisation du kit de développement, consultez la page du [wiki Azure Blockchain Development Kit for Ethereum](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki).

## <a name="create-an-api-connection"></a>Créer une connexion d’API

Une connexion d’API à une blockchain est nécessaire pour le connecteur Blockchain Ethereum. Vous pouvez utiliser le connecteur d’API pour plusieurs applications logiques. Certaines propriétés sont obligatoires et d’autres dépendent de votre scénario.

> [!IMPORTANT]
> Une clé privée ou une adresse de compte et un mot de passe sont requis pour la création de transactions sur une blockchain. Une seule forme d’authentification est nécessaire. Vous n’avez pas besoin de fournir à la fois la clé privée et les détails du compte. L’interrogation des contrats ne nécessite pas de transaction. Si vous utilisez des actions qui interrogent l’état du contrat, la clé privée ou l’adresse du compte et le mot de passe ne sont pas nécessaires.

Pour configurer une connexion à un membre Azure Blockchain Service, la liste suivante indique les propriétés dont vous pouvez avoir besoin en fonction de votre scénario.

| Propriété | Description |
|----------|-------------|
|**Connection name** | Nom de la connexion d’API. Requis. |
|**Ethereum RPC endpoint** | Adresse HTTP du nœud de transaction Azure Blockchain Service. Requis. Découvrez comment [obtenir le point de terminaison RPC](#get-rpc-endpoint). |
|**Private key** | Clé privée du compte Ethereum. Une clé privée ou une adresse de compte et un mot de passe sont requis pour les transactions. Découvrez comment [obtenir la clé privée](#get-private-key). |
|**Account address** | Adresse du compte membre Azure Blockchain Service. Une clé privée ou une adresse de compte et un mot de passe sont requis pour les transactions. Découvrez comment [obtenir l’adresse du compte](#get-account-address). |
|**Account password** | Le mot de passe du compte est défini lors de la création du membre. Pour plus d’informations sur la réinitialisation du mot de passe, consultez [Compte Ethereum](consortium.md#ethereum-account).|

## <a name="get-rpc-endpoint"></a>Obtenir le point de terminaison RPC

L’adresse du point de terminaison RPC Azure Blockchain Service est nécessaire pour se connecter à un réseau Blockchain. Vous pouvez obtenir l’adresse du point de terminaison à l’aide d’Azure Blockchain Development Kit for Ethereum ou du portail Azure.

**Utilisation du kit de développement :**

1. Sous **Azure Blockchain Service** dans Visual Studio Code, cliquez avec le bouton droit sur le consortium.
1. Sélectionnez **Copy RPC endpoint** (Copier le point de terminaison RPC).

    ![Copier le point de terminaison RPC](./media/ethereum-logic-app/devkit-rpc.png)

    Le point de terminaison RPC est copié dans le Presse-papiers.

**Utilisation du portail Azure :**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez au membre Azure Blockchain Service. Sélectionnez **Nœuds de transaction**, puis le lien du nœud de transaction par défaut.

    ![Sélectionner le nœud de transaction par défaut](./media/ethereum-logic-app/transaction-nodes.png)

1. Sélectionnez **Chaînes de connexion > Clés d’accès**.
1. Copiez l’adresse du point de terminaison située sous **HTTPS (Clé d’accès 1)** ou clé d’accès 2.

    ![Chaîne de connexion](./media/ethereum-logic-app/connection-string.png)

    Le point de terminaison RPC est l’URL HTTPS, y compris l’adresse et la clé d’accès de votre nœud de transaction de membre Azure Blockchain Service.

## <a name="get-private-key"></a>Obtenir la clé privée

La clé privée du compte Ethereum peut être utilisée pour l’authentification lors de l’envoi d’une transaction à la blockchain. Les clés publiques et privées de votre compte Ethereum sont générées à partir d’un mnémonique de 12 mots. Azure Blockchain Development Kit for Ethereum génère un mnémonique quand vous vous connectez à un membre du consortium Azure Blockchain Service. Vous pouvez obtenir l’adresse du point de terminaison à l’aide de l’extension du kit de développement.

1. Dans Visual Studio Code, ouvrez la palette de commandes (F1).
1. Choisissez **Azure Blockchain : Retrieve private key** (Récupérer la clé privée).
1. Sélectionnez le mnémonique que vous avez enregistré lors de la connexion au membre du consortium.

    ![Sélectionner un mnémonique](./media/ethereum-logic-app/private-key.png)

    La clé privée est copiée dans le Presse-papiers.

## <a name="get-account-address"></a>Obtenir l’adresse du compte

Le compte et le mot de passe du membre peuvent être utilisés pour l’authentification lors de l’envoi d’une transaction à la blockchain. Le mot de passe est défini lors de la création du membre.

1. Dans le portail Azure, accédez à la page de vue d’ensemble Azure Blockchain Service.
1. Copiez l’adresse du **compte du membre**.

    ![Copier le compte du membre](./media/ethereum-logic-app/member-account.png)

Pour plus d’informations sur l’adresse et le mot de passe du compte, consultez [Compte Ethereum](consortium.md#ethereum-account).

## <a name="get-contract-abi"></a>Obtenir l’ABI du contrat

L’interface ABI (Application Binary Interface) du contrat définit les interfaces du contrat intelligent. Elle décrit comment interagir avec le contrat intelligent. Vous pouvez obtenir l’ABI du contrat à l’aide d’Azure Blockchain Development Kit for Ethereum ou du fichier de métadonnées du contrat de compilateur Solidity.

**Utilisation du kit de développement :**

Si vous avez utilisé le kit de développement ou Truffle pour créer votre contrat intelligent, vous pouvez utiliser l’extension pour copier l’ABI du contrat dans le Presse-papiers.

1. Dans le volet de l’Explorateur de Visual Studio Code, développez le dossier **build/contracts** de votre projet Solidity.
1. Cliquez avec le bouton droit sur le fichier JSON des métadonnées de contrat. Le nom du fichier est le nom du contrat intelligent suivi de l’extension **.json**.
1. Choisissez **Copy contract ABI** (Copier l’ABI du contrat).

    ![Copier l’ABI du contrat à l’aide du DevKit](./media/ethereum-logic-app/abi-devkit.png)

    L’ABI du contrat est copiée dans le Presse-papiers.

**Utilisation du fichier de métadonnées de contrat :**

1. Ouvrez le fichier de métadonnées de contrat contenu dans le dossier **build/contracts** de votre projet Solidity. Le nom du fichier est le nom du contrat intelligent suivi de l’extension **.json**.
1. Recherchez la section **abi** dans le fichier JSON.
1. Copiez le tableau JSON **abi**.

    ![Section ABI du contrat dans les métadonnées](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-contract-bytecode"></a>Obtenir le bytecode du contrat

Le bytecode du contrat est le contrat intelligent compilé exécuté par la machine virtuelle Ethereum. Vous pouvez obtenir le bytecode du contrat à l’aide d’Azure Blockchain Development Kit for Ethereum ou du compilateur Solidity.

**Utilisation du kit de développement :**

Si vous avez utilisé le kit de développement ou Truffle pour créer votre contrat intelligent, vous pouvez utiliser l’extension pour copier le bytecode du contrat dans le Presse-papiers.

1. Dans le volet de l’Explorateur de Visual Studio Code, développez le dossier **build/contracts** de votre projet Solidity.
1. Cliquez avec le bouton droit sur le fichier JSON des métadonnées de contrat. Le nom du fichier est le nom du contrat intelligent suivi de l’extension **.json**.
1. Choisissez **Copy contract bytecode** (Copier le bytecode du contrat).

    ![Copier le bytecode du contrat à l’aide du DevKit](./media/ethereum-logic-app/bytecode-devkit.png)

    Le bytecode du contrat est copié dans le Presse-papiers.

**Utilisation du fichier de métadonnées de contrat :**

1. Ouvrez le fichier de métadonnées de contrat contenu dans le dossier **build/contracts** de votre projet Solidity. Le nom du fichier est le nom du contrat intelligent suivi de l’extension **.json**.
1. Recherchez l’élément **bytecode** dans le fichier JSON.
1. Copiez la valeur **bytecode**.

    ![Copier le bytecode à l’aide de métadonnées](./media/ethereum-logic-app/bytecode-metadata.png)

**Utilisation du compilateur Solidity :**

Utilisez la commande `solc --bin <smart contract>.sol` pour générer le bytecode du contrat.

## <a name="get-contract-address"></a>Obtenir l’adresse du contrat

L’adresse du contrat est l’adresse de destination du contrat intelligent sur la blockchain Ethereum. Vous utilisez cette adresse pour envoyer une transaction ou une requête d’état d’un contrat actif. Vous pouvez obtenir l’adresse du contrat à partir de la sortie de migration Truffle ou du fichier de métadonnées du contrat.

**Utilisation de la sortie de migration Truffle :**

Truffle affiche l’adresse du contrat après le déploiement du contrat intelligent. Copiez l’**adresse du contrat** à partir de la sortie.

![Adresse du contrat dans la sortie Truffle](./media/ethereum-logic-app/contract-address-truffle.png)

**Utilisation du fichier de métadonnées de contrat :**

1. Ouvrez le fichier de métadonnées de contrat contenu dans le dossier **build/contracts** de votre projet Solidity. Le nom du fichier est le nom du contrat intelligent suivi de l’extension **.json**.
1. Recherchez la section **networks** dans le fichier JSON.
1. Les réseaux privés sont identifiés par un ID de réseau entier. Recherchez la valeur d’adresse dans la section networks.
1. Copiez la valeur **address**.

![Adresse du contrat à partir des métadonnées](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez des [scénarios courants de connexion de blockchain à l’aide d’Azure Logic Apps](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
