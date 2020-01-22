---
title: Déployer le modèle de solution Consortium de Fabric Hyperledger sur Azure
description: Comment déployer et configurer le modèle de solution réseau consortium de Fabric Hyperledger
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: caleteet
ms.openlocfilehash: 3e7dcd3cdcfa636c0b23ac6643bd7732e7f8ada0
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029166"
---
# <a name="hyperledger-fabric-consortium-network"></a>Réseau consortium de Fabric Hyperledger

Vous pouvez utiliser le modèle de solution consortium de Fabric Hyperledger pour déployer et configurer un réseau consortium de Fabric Hyperledger sur Azure.

> [!IMPORTANT]
> Le modèle [Hyperledger Fabric sur Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-hyperledger-fabric) sera déconseillé. Préférez [Hyperledger Fabric sur Azure Kubernetes Service](hyperledger-fabric-consortium-azure-kubernetes-service.md).  

À la fin de cet article, vous serez capable :

- Obtenir une connaissance pratique de la technologie blockchain, d’Hyperledger Fabric et d’architectures de réseau de consortium plus complexes
- Découvrir comment déployer et configurer un réseau de consortium Hyperledger Fabric à partir du portail Azure

## <a name="about-blockchain"></a>À propos de la technologie blockchain

Si vous êtes un nouveau membre de la communauté blockchain, ce modèle de solution est une opportunité exceptionnelle d’en savoir plus sur cette technologie de manière simple et configurable via Azure. Blockchain est la technologie sous-jacente derrière Bitcoin. Elle est toutefois bien plus qu’un simple activateur d’une monnaie virtuelle. Il s’agit d’un mélange de base de données, de système distribué et de technologies de chiffrement existants qui permet d’effectuer des calculs multipartites sécurisés offrant des garanties d’immuabilité, de vérifiabilité, d’auditabilité et de résilience en cas d’attaque. Différents protocoles utilisent différents mécanismes pour fournir ces attributs. [Hyperledger Fabric](https://github.com/hyperledger/fabric) est l’un de ces protocoles.

## <a name="consortium-architecture-on-azure"></a>Architecture Consortium sur Azure

Pour activer Hyperledger Fabric dans Azure, deux principaux types de déploiement sont pris en charge. Ces déploiements sont conçus pour s’adapter à différentes topologies, en fonction de la cible souhaitée.

- **Machine virtuelle unique, serveur de développement** - Ce type de déploiement est conçu comme un environnement de développement utilisé pour construire et tester des solutions basées sur Hyperledger Fabric.
- **Machines virtuelles multiples, déploiement échelonné** - Ce type de déploiement est conçu pour les environnements qui modélisent un consortium de participants différents exploitant un environnement partagé.

Dans l’un ou l’autre déploiement, les blocs qui constituent le cœur de Hyperledger Fabric sont les mêmes.  Les différences dans les déploiements portent sur la façon dont ces composants montent en charge.

- **Nœuds de l’autorité de certification** : Nœud exécutant une autorité de certification qui permet de générer des certificats utilisés pour les identités dans le réseau.
- **Nœuds des auteurs des commandes** : Nœud exécutant le service de communication qui implémente une garantie de livraison, comme le nombre total de diffusions de commandes ou de transactions atomiques.
- **Nœuds homologues** : Nœud validant les transactions et conservant l’état et une copie du registre distribué.
- **Nœuds CouchDB** : Nœud qui peut exécuter le service CouchDB pouvant contenir la base de données d’état et fournir une requête enrichie de données de code chaîné, passant de la clé/valeur simple au stockage de type JSON.

### <a name="single-virtual-machine-architecture"></a>Architecture comportant une seule machine virtuelle

Comme mentionné précédemment, l’architecture comportant une seule machine virtuelle est conçue pour que les développeurs disposent d’un serveur à faible empreinte mémoire qui est utilisé pour développer des applications. Tous les conteneurs affichés fonctionnent dans une même machine virtuelle. Le service d’ordonnancement utilise [SOLO](https://github.com/hyperledger/fabric/tree/master/orderer) pour cette configuration. Cette configuration n’est *pas* un service d’ordonnancement à tolérance de pannes, mais son faible poids est idéal à des fins de développement.

![Architecture comportant une seule machine virtuelle](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Architecture de machines virtuelles

L’architecture évolutive à machines virtuelles multiples est construite pour la haute disponibilité et la mise à l’échelle de chaque composant. Cette architecture est beaucoup plus adaptée pour les déploiements en production.

![Architecture de machines virtuelles](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Prise en main

Pour commencer, vous avez besoin d’un abonnement Azure qui peut prendre en charge le déploiement de plusieurs machines virtuelles et de comptes de stockage standard. Si vous n’avez pas d’abonnement Azure, vous pouvez [créer un compte Azure gratuit](https://azure.microsoft.com/free/).

Une fois que vous avez un abonnement, accédez au [portail Azure](https://portal.azure.com). Sélectionnez **Créer une ressource > Blockchain > Consortium de Fabric Hyperledger**.

![Modèle de la Place de marché Blockchain à membre unique Hyperledger Fabric](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Déploiement

Dans le modèle **Consortium de Fabric Hyperledger**, sélectionnez **Créer**.

Le déploiement de modèle vous guidera dans la configuration du réseau à plusieurs nœuds [Hyperledger 1.3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/). Le flux de déploiement est divisé en quatre étapes : De base, paramètres de réseau de consortium, configuration de Fabric et composants facultatifs.

### <a name="basics"></a>Concepts de base

Dans **Informations de base**, spécifiez les valeurs des paramètres standard pour un déploiement. Par exemple, abonnement, groupe de ressources et propriétés de machine virtuelle de base.

![Concepts de base](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Nom du paramètre | Description | Valeurs autorisées |
|---|---|---|
**Préfixe de ressource** | Préfixe de nom pour les ressources provisionnées dans le cadre du déploiement |6 caractères au maximum |
**Nom d’utilisateur** | Nom d’utilisateur de l’administrateur pour chaque machine virtuelle déployée pour ce membre |De 1 à 64 caractères |
**Type d’authentification** | Méthode d’authentification auprès de la machine virtuelle |Mot de passe ou clé publique SSH|
**Mot de passe (Type d’authentification = Mot de passe)** |Mot de passe du compte administrateur pour chacune des machines virtuelles déployées. Le mot de passe doit contenir trois des types de caractères suivants : une majuscule, une minuscule, un chiffre et un caractère spécial<br /><br />Initialement, toutes les machines virtuelles ont le même mot de passe, que vous pouvez modifier après le provisionnement|Entre 12 et 72 caractères|
**Clé SSH (Type d’authentification = Clé publique SSH)** |Clé de shell sécurisée utilisée pour la session à distance ||
**Abonnement** |Abonnement sur lequel effectuer le déploiement ||
**Groupe de ressources** |Groupe de ressources sur lequel déployez le réseau de consortium ||
**Lieu** |Région Azure dans laquelle déployer l’empreinte réseau du premier membre dans ||

Sélectionnez **OK**.

### <a name="consortium-network-settings"></a>Paramètres de réseau de consortium

Dans **Paramètres réseau**, spécifiez les entrées pour créer ou rejoindre un réseau de consortium existant et configurez les paramètres de votre organisation.

![Paramètres de réseau de consortium](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Nom du paramètre | Description | Valeurs autorisées |
|---|---|---|
**Configuration réseau** |Vous pouvez choisir de créer un nouveau réseau ou de rejoindre un réseau existant. Si vous choisissez *Rejoindre existant*, vous devez fournir des valeurs supplémentaires. |Nouveau réseau <br/> Rejoindre existant |
**Mot de passe d’autorité de certification HLF** |Mot de passe utilisé pour les certificats générés par les autorités de certification qui sont créés dans le cadre du déploiement. Le mot de passe doit contenir trois des types de caractères suivants : une majuscule, une minuscule, un chiffre et un caractère spécial.<br /><br />Initialement, toutes les machines virtuelles ont le même mot de passe, que vous pouvez modifier après le provisionnement.|Entre 1 et 25 caractères |
**Paramètres de l’organisation** |Vous pouvez personnaliser le nom et le certificat de votre organisation ou utiliser des valeurs par défaut.|Default <br/> Avancé |
**Paramètres de réseau VPN** | Configurer une passerelle de tunnel VPN pour accéder aux machines virtuelles | Oui <br/> Non |

Sélectionnez **OK**.

### <a name="fabric-specific-settings"></a>Paramètres spécifiques à Fabric

Dans **Configuration de l’infrastructure**, configurez la taille et les performances du réseau, et spécifiez les entrées de la disponibilité du réseau. Par exemple, le nombre de nœuds d’ordonnanceur et homologues, le moteur de persistance utilisé par chaque nœud, et la taille de la machine virtuelle.

![Paramètres Fabric](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Nom du paramètre | Description | Valeurs autorisées |
|---|---|---|
**Type d’échelle** |Type de déploiement d’une machine virtuelle unique avec plusieurs conteneurs ou de plusieurs machines virtuelles dans un modèle évolutif.|Machine virtuelle unique ou machines virtuelles multiples |
**Type de disque de machine virtuelle** |Type de stockage prenant en charge chacun des nœuds déployés. <br/> Pour en savoir plus sur les types de disques disponibles, consultez [Sélectionner un type de disque](../../virtual-machines/windows/disks-types.md).|SSD Standard <br/> SSD Premium |

### <a name="multiple-vm-deployment-additional-settings"></a>Déploiement de machines virtuelles multiples (paramètres supplémentaires)

![Paramètres de fabric pour les déploiements de machines virtuelles multiples](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Nom du paramètre | Description | Valeurs autorisées |
|---|---|---|
**Nombre de nœuds des auteurs des commandes** |Nombre de nœuds qui commandent (organisent) les transactions dans un bloc. <br />Pour plus d’informations sur le service de commande, consultez la [documentation](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) Hyperledger |1-4 |
**Taille de machine virtuelle du nœud d’ordonnancement** |Taille de machine virtuelle utilisée pour les nœuds d’ordonnancement dans le réseau|Standard BS<br />Standard DS<br />Standard FS |
**Nombre de nœuds homologues** | Nœuds appartenant à des membres de consortium qui exécutent des transactions et conservent l’état et une copie du registre.<br />Pour plus d’informations sur le service de commande, consultez la [documentation](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html) Hyperledger.|1-4 |
**Persistance d’état de nœud** |Moteur de persistance utilisé par les nœuds homologues. Vous pouvez configurer ce moteur par nœud homologue. Consultez les détails ci-dessous pour plusieurs nœuds homologues.|CouchDB <br />LevelDB |
**Taille de machine virtuelle du nœud homologue** |Taille de machine virtuelle utilisée pour tous les nœuds dans le réseau|Standard BS<br />Standard DS<br />Standard FS |

### <a name="multiple-peer-node-configuration"></a>Configuration des nœuds homologues multiples

Ce modèle vous permet de choisir votre moteur de persistance par nœud homologue. Par exemple, si vous avez trois nœuds homologues, vous pouvez utiliser CouchDB sur l’un et LevelDB sur les deux autres.

![Configuration des nœuds homologues multiples](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Sélectionnez **OK**.

### <a name="deploy"></a>Déployer

Dans **Résumé**, passez en revue les entrées spécifiées et exécutez une validation de pré-déploiement de base.

![Résumé](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Passez en revue les conditions juridiques et de confidentialité, puis sélectionnez **Achat** pour lancer le déploiement. En fonction du nombre de machines virtuelles en cours de provisionnement, le temps de déploiement peut varier de quelques minutes à des dizaines de minutes.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez désormais vous concentrer sur le développement d’applications et de code chaîné sur votre réseau blockchain de consortium Hyperledger.
