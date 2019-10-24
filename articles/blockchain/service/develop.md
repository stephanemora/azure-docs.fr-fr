---
title: Vue d’ensemble du développement d’Azure Blockchain Service
description: Introduction au développement de solutions sur Azure Blockchain Service.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/11/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 2990917243507d93cb6a86da11cf771f3a42eebd
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298432"
---
# <a name="azure-blockchain-service-development-overview"></a>Vue d’ensemble du développement d’Azure Blockchain Service

Avec Azure Blockchain Service, vous pouvez créer des réseaux blockchain de consortium permettant l’exécution de scénarios d’entreprise comme le suivi des biens, les jetons numériques, la fidélité et les récompenses, finances de la chaîne d’approvisionnement et la provenance. Cet article est une introduction au développement d’Azure Blockchain Service et aux principaux sujets pour implémenter une blockchain pour une entreprise.

## <a name="client-connection-to-azure-blockchain-service"></a>Connexion client à Azure Blockchain Service

Il existe différents types de clients pour les réseaux blockchain, y compris les nœuds complets, les nœuds lumineux et les clients distants. Azure Blockchain Service génère un réseau blockchain qui inclut des nœuds. Vous pouvez utiliser différents clients comme passerelle vers Azure Blockchain Service pour le développement de solutions blockchain. Azure Blockchain Service offre l’authentification de base ou une clé d’accès comme point de terminaison de développement. Les éléments suivants sont des clients vous permettant de vous connecter.

### <a name="metamask"></a>MetaMask

MetaMask est un portefeuille basé sur un navigateur (client distant), un client RPC et un explorateur de contrat de base. Contrairement aux autres portefeuilles de navigateur, MetaMask injecte une instance web3 dans le contexte JavaScript du navigateur, agissant comme un client RPC qui se connecte à un large éventail de blockchains Ethereum (*mainnet*, *Ropsten testnet*, *Kovan testnet*, nœud RPC local, etc..). Vous pouvez configurer un RPC personnalisé facilement pour qu’il se connecter à Azure Blockchain Service et démarrer le développement de blockchain à l’aide de Remix.

### <a name="geth"></a>Geth

Geth est l’interface de ligne de commande pour l’exécution d’un nœud Ethereum complet implémenté dans Go. Vous n’avez pas besoin de l’exécution complète de nœud, mais vous pouvez lancer sa console interactive qui fournit un environnement d’exécution JavaScript exposant une API JavaScript pour interagir avec Azure Blockchain Service.

## <a name="development-framework-configuration"></a>Configuration de l’infrastructure de développement

Pour développer des solutions de blockchain d’entreprise sophistiquées, une infrastructure de développement est nécessaire pour se connecter aux différents réseaux blockchain, gérer le cycle de vie d’un contrat intelligent, automatiser les tests, déployer un contrat intelligent avec des scripts et équiper une console interactive.

Truffle est une infrastructure de développement blockchain populaire pour écrire, compiler, déployer et tester des applications décentralisées sur des blockchains Ethereum. Vous pouvez également considérer Truffle comme une infrastructure qui tente d’intégrer en toute transparence le développement de contrats intelligents et le développement web traditionnel.

Même le plus petit projet interagit avec au moins deux nœuds de blockchain : Un sur l’ordinateur du développeur et l’autre représentant le réseau où le développeur déploie son application. Par exemple, le principal réseau Ethereum public ou Azure Blockchain Service. Truffle fournit un système pour gérer les artefacts de compilation et de déploiement pour chaque réseau et le fait d’une manière qui simplifie le déploiement de l’application finale. Pour plus d’informations, consultez [Démarrage rapide : Utiliser Truffle pour vous connecter à un réseau Azure Blockchain Service](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Transaction privée Ethereum Quorum

Quorum est un protocole de registre distribué basé sur Ethereum avec une confidentialité des transactions et des contrats ainsi que de nouveaux mécanismes de consensus. Les améliorations clés sur Go-Ethereum sont les suivantes :

* Confidentialité - Quorum prend en charge les transactions et les contrats privés via la séparation de l’état public et privé et utilise des échanges de messages chiffrés en peer-to-peer pour un transfert direct de données privées aux participants du réseau.
* Autres mécanismes de Consensus - sans avoir besoin d’un consensus de preuve de travail ou de preuve de participation dans un réseau autorisé. Quorum offre plusieurs mécanismes de consensus qui sont conçus pour les chaînes de consortium telles que RAFTING et IBFT.  Azure Blockchain Services utilise le mécanisme de consensus IBFT.

* Permissions de paires - autorisations de nœud et de paire à l’aide de contrats intelligents, garantissant que seuls les tiers connus peuvent se connecter au réseau
* Performances plus élevées - Quorum offre de meilleures performances que Geth publique

Consultez le [tutoriel : Envoi d’une transaction à l’aide d’Azure Blockchain Service](send-transaction.md) pour obtenir un exemple de transaction privé.

## <a name="block-explorers"></a>Explorateurs de bloc

Les explorateurs de bloc sont des navigateurs de blockchain en ligne qui affichent le contenu des blocs individuels, les données des adresses des transactions et l’historique. Les informations de base sur les blocs sont disponibles via Azure Monitor dans Azure Blockchain Service. Toutefois, si vous avez besoin d’informations plus détaillées pendant le développement, les explorateurs de bloc peuvent être utiles.  Voici les explorateurs de bloc open source populaires que vous pouvez utiliser. Voici une liste des explorateurs de bloc qui fonctionnent avec Azure Blockchain Service :

* [Epirus Azure Blockchain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) de Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>Mesure TPS

Comme une blockchain est utilisée dans plus de scénarios d’entreprise, la vitesse en transactions par seconde (TPS) est importante pour éviter les goulots d’étranglement et le manque d’efficacité des systèmes. Des taux de transaction élevés peuvent être difficiles à gérer au sein d’une blockchain décentralisée. Une mesure précise des TPS peut-être affectée par différents facteurs tels que le thread du serveur, la taille de la file d’attente des transactions, la latence du réseau et la sécurité. Si vous avez besoin de mesurer la vitesse en TPS au cours du développement, [ChainHammer](https://github.com/drandreaskrueger/chainhammer) est un outil open source populaire.

## <a name="next-steps"></a>Étapes suivantes

[Démarrage rapide : Utiliser Truffle pour vous connecter à un réseau Azure Blockchain Service](connect-truffle.md)
