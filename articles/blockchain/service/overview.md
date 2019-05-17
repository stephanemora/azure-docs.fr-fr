---
title: Présentation d’Azure Blockchain Service
description: Vue d’ensemble du Service Azure Blockchain
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: e370916d420a7bc4cd16d021c69a2f8609093d30
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544700"
---
# <a name="what-is-azure-blockchain-service"></a>Qu’est Azure Blockchain Service ?

Le Service Azure Blockchain est un service de comptabilité entièrement géré qui permet aux utilisateurs la possibilité de croître et d’exploiter les réseaux de blockchain à grande échelle dans Azure. En fournissant un contrôle unifié pour gestion de l’infrastructure ainsi que gouvernance de réseau blockchain, Azure Blockchain Service fournit :

* Opérations et le déploiement de réseau simple
* Gestion de consortium intégrée
* Développer des contrats intelligents avec les outils de développement familier

Le Service Azure Blockchain est conçu pour prendre en charge plusieurs protocoles de comptabilité. Actuellement, il prend en charge la Ethereum [Quorum](https://www.jpmorgan.com/Quorum) livre à l’aide de la [IBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) mécanisme de consensus.

Ces fonctionnalités ne demandent pratiquement aucune administration, et toutes sont fournies sans coût supplémentaire. Vous pouvez vous concentrer sur le développement d’applications et de logique métier plutôt que de temps et des ressources à la gestion des machines virtuelles et l’infrastructure. En outre, vous pouvez continuer à développer votre application avec les outils open source et la plateforme de votre choix pour fournir vos solutions sans avoir à acquérir de nouvelles compétences.

## <a name="network-deployment-and-operations"></a>Déploiement de réseau et d’exploitation

Déployer Azure Blockchain Service peut être effectuée via le portail Azure, Azure CLI, ainsi que via Visual Studio code à l’aide de l’extension Azure Blockchain.  Le déploiement est simplifié, y compris les nœuds de transaction et du programme de validation, réseaux virtuels Azure pour l’isolation de sécurité ainsi que le stockage géré par le service d’approvisionnement.  En outre, lorsque vous déployez un nouveau membre de blockchain, les utilisateurs également créer ou joindre, un consortium.  Consortiums activer plusieurs parties dans différents abonnements Azure être en mesure de communiquer en toute sécurité entre eux sur une blockchain partagée.  Cet simplification du déploiement réduit blockchain réseau déploiement jours à quelques minutes.

### <a name="performance-and-service-tiers"></a>Niveaux de performances et de service

Le Service de Blockchain Azure offre deux niveaux de service : *Base* et *Standard*. Chaque niveau offre différentes performances et fonctionnalités pour prendre en charge de développement légère et testez des charges de travail jusqu'à massivement mis à l’échelle les déploiements de production blockchain. Les deux niveaux inclure au moins une transaction et un seul nœud du programme de validation (Basic) ou les deux du programme de validation nœuds (Standard).

![Niveaux de tarification](./media/overview/pricing-tiers.png)

En plus d’offrir deux nœuds du programme de validation, le *Standard* niveau fournit 2 *vCores* pour chaque nœud de la transaction et du programme de validation tandis que le niveau de base offre une configuration de vCore 1.  En proposant 2 vCores pour les nœuds de transaction et du programme de validation, 1 vCore peut être dédié à la comptabilité de Quorum le 1 vCore restant peut être utilisées pour d’autres services liés à l’infrastructure, en garantissant des performances optimales pour la production de charges de travail blockchain. Pour plus d’informations sur la tarification, consultez [tarification Azure Blockchain Service](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Sécurité et maintenance

Après avoir approvisionné votre premier membre de blockchain, vous avez la possibilité d’ajouter des nœuds de transactions supplémentaires à votre membre.  Par défaut, les nœuds de la transaction sont sécurisés par le biais des règles de pare-feu et devront être configuré pour l’accès.  En outre, tous les nœuds de transaction chiffrent les données en mouvement via TLS.  Il existe plusieurs options pour la sécurisation des accès de nœud de transactions, y compris les règles de pare-feu, l’authentification de base, les clés d’accès ainsi qu’intégration d’Azure Active Directory. Pour plus d’informations, consultez [configurer les nœuds de la transaction](configure-transaction-nodes.md) et [configurer l’accès d’Azure Active Directory](configure-aad.md).

En tant que service géré, Azure Blockchain Service permet de s’assurer que les nœuds du membre de votre blockchain sont corrigées avec l’hôte plus récente d’exploitation du système et du grand livre des pile mises à jour, configuré pour la haute disponibilité (niveau Standard uniquement), éliminant la plupart des opérations de développement obligatoire pour les nœuds de blockchain IaaS traditionnels.  Pour plus d’informations sur l’application de correctifs et mises à jour, consultez [Azure Blockchain Service du grand livre des versions prises en charge](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Surveillance et journalisation

En outre, Azure Blockchain Service fournit des métriques riche via Azure Monitor Service fournissant des informations sur les nœuds du processeur, mémoire et l’utilisation du stockage, ainsi que des informations utiles sur les activités de réseau de blockchain tels que les transactions et les blocs extraits, profondeur de file d’attente de transaction, ainsi que des connexions actives.  Mesures peuvent être personnalisées pour fournir des vues dans les informations qui sont importantes pour votre application blockchain.  En outre, vous pouvez définir des seuils via des alertes permettant aux utilisateurs de déclencher des actions comme envoyer un e-mail ou message texte, une application logique, la fonction Azure en cours d’exécution ou l’envoi à un webhook personnalisé.

![Mesures](./media/overview/metrics.png)

Via l’Analytique de journal Azure, les utilisateurs peuvent afficher les journaux relatifs à la comptabilité de Quorum, ou d’autres connexions d’a tenté de telles que des informations importantes sur les nœuds de la transaction.

## <a name="built-in-consortium-management"></a>Gestion de consortium intégrée

Lorsque vous déployez votre premier membre de blockchain, vous rejoignez ou créez un consortium de nouveau.  Un consortium est un groupe logique utilisé pour gérer la gouvernance et la connectivité entre les membres de blockchain qui transact dans un processus de tierces party.  Azure Blockchain Service fournit des contrôles de gouvernance intégrées par le biais des contrats intelligents prédéfinis, qui déterminent les membres des actions dans le consortium possibles.  Ces contrôles de gouvernance peuvent être personnalisés en fonction des besoins par l’administrateur du consortium. Lorsque vous créez un nouveau consortium, membre de votre blockchain est l’administrateur par défaut du consortium, l’activation de la possibilité d’inviter d’autres parties à rejoindre votre consortium.  Vous pouvez joindre un consortium uniquement si vous avez été invité précédemment.  Lorsque vous joignez un consortium, votre membre blockchain est soumis à des contrôles de gouvernance mis en place par l’administrateur du consortium.

![Gestion du consortium](./media/overview/consortium.png)

Actions de gestion Consortium Ajout et suppression de membres dans un consortium sont accessible via PowerShell et une API REST. Vous pouvez gérer par programme un consortium à l’aide des interfaces communes plutôt que la modification et la soumission basée sur l’uniformité des contrats intelligents. Pour plus d’informations, consultez [gestion du consortium](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Développer à l’aide des outils de développement familiers

En fonction de la comptabilité de Quorum Ethereum open source, vous pouvez développer des applications pour Azure Blockchain Service la même façon comme vous le feriez pour les applications existantes Ethereum. Utilisation des principaux partenaires du secteur, l’extension Azure Blockchain Development Kit Visual Studio Code permet aux développeurs de tirer parti des outils familiers tels que Suite Truffle pour créer des contrats intelligents. À l’aide de l’extension du Kit de développement Azure Blockchain, les développeurs peuvent créer ou se connecter à et consortium existant afin que vous pouvez générer et déployer votre smart tous les contrats à partir d’un seul IDE. À l’aide de l’extension Azure Blockchain Visual Studio Code, vous pouvez créer ou se connecter à un consortium existant afin que vous pouvez générer et déployer vos contrats intelligents à partir d’un seul IDE. Pour plus d’informations, consultez [Kit de développement Azure Blockchain dans la place de marché Visual Studio Code](https://aka.ms/vscodebcextension) et [guide d’utilisateur de Kit de développement Azure Blockchain](https://aka.ms/vscodebcextensionwiki ).

## <a name="support-and-feedback"></a>Support et commentaires

Besoin d’aide ou avez des commentaires ?

* Visitez le [sur la technologie Blockchain Azure](https://azure.microsoft.com/blog/topics/blockchain/), [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain), et [forum d’Azure Blockchain](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain).
* Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/forums/921130-azure-blockchain-service).

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, essayez un démarrage rapide ou Découvrez plus d’informations à partir de ces ressources.
* [Créer un membre de blockchain à l’aide du portail Azure](create-member.md) ou [créer un membre de blockchain avec Azure CLI](create-member-cli.md)
* Pour les comparaisons de coûts et les calculatrices, consultez le [page de tarification](https://azure.microsoft.com/pricing/details/blockchain-service).
* Créer votre première application en utilisant le [Kit de développement Azure Blockchain](https://github.com/Azure-Samples/blockchain-devkit)
* Extension de VSCode Azure Blockchain [guide de l’utilisateur](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)
