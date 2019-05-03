---
title: Vue d’ensemble du développement Blockchain Service Azure
description: Introduction sur le développement de solutions sur Azure Blockchain Service.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 388a5d8c80c3e2462602959e9d5cbc1452974d1f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027899"
---
# <a name="azure-blockchain-service-development-overview"></a>Vue d’ensemble du développement Blockchain Service Azure

Avec Azure Blockchain Service, vous pouvez créer des réseaux de blockchain permettant des scénarios d’entreprise comme provenance, jeton numérique, fidélité et reward, chaîne logistique financiers et suivi des biens consortium. Cet article est une introduction à la vue d’ensemble du développement Azure Blockchain Service et aux principaux sujets pour implémenter la fonctionnalité « blockchain » pour l’entreprise.

## <a name="client-connection-to-azure-blockchain-service"></a>Connexion client au Service de Azure Blockchain

Il existe différents types de clients pour les réseaux de blockchain, y compris les nœuds complètes, les nœuds de lumière et les clients distants. Azure Blockchain Service génère un réseau de blockchain qui inclut les nœuds. Vous pouvez utiliser différents clients que votre passerelle vers Azure Blockchain Service pour le développement de solutions blockchain. Azure Blockchain Service offre l’authentification de base ou de la clé d’accès comme un point de terminaison de développement. Les éléments suivants sont des clients vous permettent de vous connecter.

### <a name="metamask"></a>MetaMask

MetaMask est basée sur navigateur wallet (le client distant), client RPC et l’Explorateur de contrat de base. Contrairement aux autres portefeuilles de navigateur, MetaMask injecte l’une instance web3 dans le contexte JavaScript du navigateur, agissant comme un client RPC qui se connecte à un large éventail de Ethereum blockchains (*mainnet*, *Ropsten testnet*, *Kovan testnet*, nœud RPC local, etc..). Vous pouvez configurer RPC personnalisée facilement à se connecter au Service de Blockchain Azure et démarrer le développement de blockchain à l’aide de Remix.

### <a name="geth"></a>Geth

Geth est l’interface de ligne de commande pour l’exécution d’un nœud Ethereum complet implémenté dans Go. Vous n’avez pas besoin de l’exécution complète de nœud, mais peut lancer sa console interactive qui fournit un environnement d’exécution JavaScript exposer une API JavaScript pour interagir avec le Service Azure Blockchain.

## <a name="development-framework-configuration"></a>Configuration d’infrastructure de développement

Pour développer des solutions de blockchain enterprise sophistiquées, une infrastructure de développement est nécessaire pour se connecter aux réseaux de blockchain différents, gérer le cycle de vie de contrat intelligente, automatiser les tests, déployer le contrat intelligent avec des scripts et équiper une console interactive.

Truffle est une infrastructure de développement populaire blockchain à écrire, compiler, déployer et tester des applications décentralisées sur les chaînes de blocs Ethereum. Vous pouvez également considérer Truffle comme une infrastructure qui tente d’intégrer en toute transparence le développement de contrat intelligent et le développement web traditionnel.

Même le plus petit projet interagit avec au moins deux nœuds de blockchain : Une sur l’ordinateur du développeur et l’autre représentant le réseau où le développeur déploie à leur application. Par exemple, le principal réseau Ethereum public ou Azure Blockchain Service. Truffle fournit un système pour gérer les artefacts de compilation et de déploiement pour chaque réseau et le fait d’une manière qui simplifie le déploiement de l’application finale. Pour plus d’informations, consultez [Démarrage rapide : Utilisez Truffle pour vous connecter à un un réseau Azure Blockchain Service](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Transaction privée Ethereum Quorum

Quorum est un protocole basé sur les Ethereum de Registre distribué transaction ainsi que de nouveaux mécanismes de consensus et de confidentialité de contrat. Des améliorations clés sur Go-Ethereum sont les suivantes :

* Confidentialité - Quorum prend en charge les transactions privées et contrats privés via la séparation de l’état public et privé et utilise les échanges de messages chiffrés de peer-to-peer pour orienté transfert de données privées aux participants de réseau.
* Autres mécanismes de Consensus - sans avoir besoin d’un consensus preuve de travail ou de preuve de jeu dans un réseau autorisé. Quorum offre plusieurs mécanismes de consensus qui sont conçus pour les chaînes telles que RAFTING et IBFT consortium.  Azure Blockchain Services utilise le mécanisme de consensus IBFT.

* Homologuer octroi d’autorisations - nœud et l’homologue octroi d’autorisations à l’aide de contrats intelligents, ne garantissant que les tiers connus permettre se connecter au réseau
* Performances plus élevées - Quorum une meilleure performance que Geth publique

Consultez le [tutoriel : Envoi d’une transaction à l’aide d’Azure Blockchain Service](send-transaction.md) pour obtenir un exemple de transaction privé.

## <a name="block-explorers"></a>Explorateurs de bloc

Explorateurs de bloc sont les navigateurs de blockchain en ligne qui affichent le contenu des blocs individuels, adresse données de transaction et l’historique. Informations de bloc de base sont disponibles via Azure Monitor dans Azure Blockchain Service, toutefois, si vous avez besoin des informations plus détaillées pendant le développement, les explorateurs de bloc peuvent être utiles.  Il existe des explorateurs de bloc d’open source populaires que vous pouvez utiliser. Voici une liste des explorateurs de bloc qui fonctionnent avec Azure Blockchain Service :

* [Azure Blockchain Service Explorer](https://web3labs.com/azure-offer) des laboratoires de Web3
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>Mesure de programmes transactionnels

Blockchain est utilisée dans plusieurs scénarios d’entreprise, les transactions par seconde (TPS) vitesse est important d’éviter les goulots d’étranglement et système manque d’efficacité. Taux de transaction élevé peuvent être difficiles à gérer au sein d’une blockchain décentralisée. Une mesure de TPS précise peut-être être affectée par différents facteurs tels que le thread du serveur, de taille de file d’attente de transaction, de latence du réseau et de sécurité. Si vous avez besoin mesurer la vitesse de programmes transactionnels au cours du développement, un outil open source populaire est [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Étapes suivantes

[Démarrage rapide : Utilisez Truffle pour vous connecter à un un réseau Azure Blockchain Service](connect-truffle.md)
