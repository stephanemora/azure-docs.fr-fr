---
title: Consortium de Fabric Hyperledger
description: Utilisez le modèle de solution Consortium de Fabric Hyperledger pour déployer et configurer un réseau à membre unique
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 28561f5f94044d19cfd07e99d7f7a736ec470cf1
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960353"
---
# <a name="hyperledger-fabric-single-member-network"></a>Réseau à membre unique Hyperledger Fabric

Vous pouvez utiliser le modèle de solution Consortium de Fabric Hyperledger pour déployer et configurer un réseau à membre unique (à plusieurs nœuds) Hyperledger Fabric.

À la fin de cet article, vous serez capable :

- Obtenir une connaissance pratique de la technologie blockchain, d’Hyperledger Fabric et d’architectures de réseau de consortium plus complexes
- Découvrir comment déployer et configurer un réseau de consortium Hyperledger Fabric à membre unique à partir du portail Azure

## <a name="about-blockchain"></a>À propos de la technologie blockchain

Si vous êtes un nouveau membre de la communauté blockchain, voici une opportunité exceptionnelle d’en savoir plus sur cette technologie de manière simple et configurable via Azure. Blockchain est la technologie sous-jacente derrière Bitcoin. Elle est toutefois bien plus qu’un simple activateur d’une monnaie virtuelle. Il s’agit d’un mélange de base de données, de système distribué et de technologies de chiffrement existants qui permet d’effectuer des calculs multipartites sécurisés offrant des garanties d’immuabilité, de vérifiabilité, d’auditabilité et de résilience en cas d’attaque. Différents protocoles utilisent différents mécanismes pour fournir ces attributs. [Hyperledger Fabric](https://github.com/hyperledger/fabric) est l’un de ces protocoles.

## <a name="consortium-architecture-on-azure"></a>Architecture Consortium sur Azure

Ce modèle déploie une topologie qui permet de tester et de simuler la production pour les utilisateurs d’une seule et même organisation (membre unique). Ce déploiement comprend un réseau à plusieurs nœuds dans une seule région, laquelle sera bientôt développée en plusieurs régions.

Le réseau comprend trois types de nœuds :

1. **Nœud de membres** : nœud exécutant le service d’appartenance à Fabric qui inscrit et gère les membres du réseau. Ce nœud peut éventuellement être en cluster dans un souci de scalabilité et de haute disponibilité. Toutefois, dans ce lab, un nœud à un seul membre est utilisé.
2. **Nœuds des auteurs des commandes** : nœud exécutant le service de communication qui implémente une garantie de livraison, comme le nombre total de diffusions de commandes ou de transactions atomiques.
3. **Nœuds homologues** : nœud validant les transactions et conservant l’état et une copie du registre distribué.

## <a name="getting-started"></a>Prise en main

Pour commencer, vous avez besoin d’un abonnement Azure qui peut prendre en charge le déploiement de plusieurs machines virtuelles et de comptes de stockage standard. Si vous n’avez pas d’abonnement Azure, vous pouvez [créer un compte Azure gratuit](https://azure.microsoft.com/free/).

Par défaut, la plupart des types d’abonnements prennent en charge une topologie de petit déploiement sans avoir besoin d’augmenter le quota. Le plus petit déploiement possible pour un seul membre a besoin des éléments suivants :

- 5 machines virtuelles (5 cœurs)
- 1 réseau virtuel
- 1 équilibreur de charge
- 1 adresse IP publique

Une fois que vous avez un abonnement, accédez au [portail Azure](https://portal.azure.com). Sélectionnez **+**, **Blockchain**, puis **Blockchain à membre unique Hyperledger Fabric**.

![Modèle de la Place de marché Blockchain à membre unique Hyperledger Fabric](./media/hyperledger-fabric-single-member-blockchain/marketplace-template.png)

## <a name="deployment"></a>Déploiement

Pour commencer, sélectionnez **Blockchain à membre unique Hyperledger Fabric**, puis cliquez sur **Créer**. Le panneau **Bases** de l’Assistant s’ouvre.

Le déploiement de modèle vous guidera dans la configuration du réseau à plusieurs nœuds. Le flux de déploiement est divisé en trois étapes : Bases, Configuration du réseau et Configuration de Fabric.

### <a name="basics"></a>Concepts de base

Sous le panneau **Bases**, spécifiez les valeurs des paramètres standard pour n’importe quel déploiement, comme l’abonnement, le groupe de ressources et les propriétés de base de la machine virtuelle.

![Concepts de base](./media/hyperledger-fabric-single-member-blockchain/basics.png)

Nom du paramètre| Description| Valeurs autorisées|Valeur par défaut
---|---|---|---
**Préfixe de ressource**| Chaîne utilisée comme base pour nommer les ressources déployées.|6 caractères au maximum|N/D
**Nom d’utilisateur de la machine virtuelle**| Nom d’utilisateur de l’administrateur pour chaque machine virtuelle déployée pour ce membre.|De 1 à 64 caractères|azureuser
**Type d’authentification**| Méthode d’authentification auprès de la machine virtuelle.|Mot de passe ou clé publique SSH|Mot de passe
**Mot de passe (Type d’authentification = Mot de passe)**|Mot de passe du compte administrateur pour chacune des machines virtuelles déployées. Le mot de passe doit contenir 3 des éléments suivants : une minuscule, une majuscule, un chiffre et un caractère spécial.<br /><br />Initialement, toutes les machines virtuelles ont le même mot de passe, que vous pouvez modifier après le provisionnement.|Entre 12 et 72 caractères|N/D
**Clé SSH (Type d’authentification = Clé publique)**|Clé de shell sécurisée utilisée pour la session à distance.||N/D
**Restreindre l’accès par adresse IP**|Paramètre permettant de déterminer si l’accès au point de terminaison client est limité ou non.|Oui/Non| Non 
**Adresse IP ou sous-réseau autorisé (restreindre l’accès par adresse IP = Oui)**|Adresse IP ou l’ensemble d’adresses IP autorisées à accéder au point de terminaison client quand le contrôle d’accès est activé.||N/D
**Abonnement** |Abonnement sur lequel effectuer le déploiement.
**Groupe de ressources** |Groupe de ressources sur lequel déployez le réseau de consortium.||N/D
**Lieu** |Région Azure dans laquelle déployer l’empreinte réseau du premier membre.

### <a name="network-size-and-performance"></a>Taille et performances du réseau

Ensuite, sous **Taille et performances du réseau**, spécifiez des valeurs pour la taille du réseau de consortium, comme le nombre de nœuds d’appartenances, d’auteurs des commandes et homologues. Choisissez les options d’infrastructure et la taille de vos machines virtuelles.

![Taille et performances du réseau](./media/hyperledger-fabric-single-member-blockchain/network-size-performance.png)

Nom du paramètre| Description| Valeurs autorisées|Valeur par défaut
---|---|---|---
**Nombre de nœuds d’appartenance**|Nombre de nœuds exécutant le service d’appartenance. Pour plus d’informations sur le service d’appartenance, consultez la section relative à la sécurité et aux services d’appartenance dans la [documentation](https://media.readthedocs.org/pdf/hyperledger-fabric/latest/hyperledger-fabric.pdf) Hyperledger.<br /><br />Cette valeur est actuellement limitée à 1 nœud, mais nous prévoyons de prendre en charge l’augmentation de la taille des instances par le biais du clustering dans la prochaine révision.|1| 1
**Nombre de nœuds des auteurs des commandes** |Nombre de nœuds qui commandent (organisent) les transactions dans un bloc. --> Cette instruction est très longue et peu claire. Pour plus d’informations sur le service de commande, consultez la [documentation](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) Hyperledger.<br /><br />Cette valeur est actuellement limitée à 1 nœud. |1 |1
**Nombre de nœuds homologues**| Nœuds appartenant à des membres de consortium qui exécutent des transactions et conservent l’état et une copie du registre.<br /><br />Pour plus d’informations sur le service de commande, consultez la [documentation](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html) Hyperledger.|3| 3 - 9
**Performances de stockage**|Type de stockage prenant en charge chacun des nœuds déployés. Pour en savoir plus sur le stockage, consultez [Introduction au stockage Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction) et [Stockage Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).|Standard ou Premium|standard
**Taille de la machine virtuelle** |Taille de machine virtuelle utilisée pour tous les nœuds dans le réseau|Standard A,<br />Standard D,<br />Standard D-v2,<br />Standard F série,<br />Standard DS,<br />et Standard FS|Standard D1_v2

### <a name="fabric-specific-settings"></a>Paramètres spécifiques à Fabric

Enfin, sous **Paramètres Fabric**, spécifiez les paramètres de configuration relatifs à Fabric.

![Paramètres Fabric](./media/hyperledger-fabric-single-member-blockchain/fabric-settings.png)

Nom du paramètre| Description| Valeurs autorisées|Valeur par défaut
---|---|---|---
**Nom d’utilisateur de démarrage**| L’utilisateur autorisé initial qui est inscrit avec les services membres dans le réseau déployé.|9 caractères ou moins|admin
**Mot de passe de l’utilisateur de démarrage pour l’autorité de certification Fabric**|Mot de passe d’administrateur utilisé pour sécuriser le compte d’autorité de certification Fabric importé dans le nœud d’appartenance.<br /><br />Le mot de passe doit contenir une majuscule, une minuscule et un chiffre.|12 caractères ou plus|N/D

### <a name="deploy"></a>Déployer

Dans **Résumé**, passez en revue les entrées spécifiées et exécutez une validation de pré-déploiement de base.

![Résumé](./media/hyperledger-fabric-single-member-blockchain/summary.png)

Passez en revue les conditions juridiques et de confidentialité, puis cliquez sur **Achat** pour lancer le déploiement. En fonction du nombre de machines virtuelles en cours de provisionnement, le temps de déploiement peut varier de quelques minutes à des dizaines de minutes.

### <a name="accessing-nodes"></a>Accès aux nœuds

Une fois le déploiement terminé, une **Vue d’ensemble** s’affiche.

![Déploiements](./media/hyperledger-fabric-single-member-blockchain/deployments.png)

Si l’écran ne s’affiche pas automatiquement (peut-être parce que vous vous êtes déplacé dans le portail de gestion pendant l’exécution du déploiement), vous pouvez toujours le trouver sous l’onglet Groupes de ressources dans la barre de navigation de gauche. Cliquez sur le nom de groupe de ressources entré à l’étape 1 pour accéder à la page **Vue d’ensemble**.

La vue d’ensemble répertorie toutes les ressources qui ont été déployées par le modèle de solution. Vous pouvez les explorer à votre convenance, mais cet écran vous permet également d’accéder aux _paramètres de sortie_ générés par le modèle. Ces paramètres de sortie vous donnent des informations utiles lors de la connexion à votre réseau Fabric Hyperledger.

Pour accéder aux paramètres de sortie, cliquez tout d’abord sur l’onglet **Déploiements** dans le panneau Groupe de ressources. L’historique de déploiement s’affiche.

![Historique de déploiement](./media/hyperledger-fabric-single-member-blockchain/deployment-history.png)

À partir de l’historique de déploiement, cliquez sur le premier déploiement de la liste pour examiner les détails.

![Détails du déploiement](./media/hyperledger-fabric-single-member-blockchain/deployment-details.png)

L’écran de détails affiche un résumé du déploiement, suivi de trois paramètres de sortie utiles :

- Le paramètre _API-ENDPOINT_ peut être utilisé une fois que vous avez déployé une application sur le réseau.
- Le paramètre _PREFIX_, également appelé _préfixe de déploiement_, identifie de façon unique vos ressources et votre déploiement. Il est utilisé quand vous utilisez les outils en ligne de commande.
- Le paramètre _SSH-TO-FIRST-VM_ vous donne une commande SSH pré-assemblée avec tous les paramètres appropriés nécessaires pour se connecter à la première machine virtuelle de votre réseau. dans le cas de Fabric Hyperledger, ce sera le nœud de l’autorité de certification Fabric.

Vous pouvez vous connecter à distance aux machines virtuelles de chaque nœud via le protocole SSH avec le nom d’utilisateur administrateur et le mot de passe/la clé SSH fournis. Étant donné que les machines virtuelles de nœud n’ont pas leurs propres adresses IP publiques, vous devez y accéder par le biais de l’équilibreur de charge et spécifier le numéro de port. La commande SSH permettant d’accéder au premier nœud de la transaction est la troisième sortie de modèle tiers, **SSH-TO-FIRST-VM (pour l’exemple de déploiement : `sh -p 3000 azureuser@hlf2racpt.northeurope.cloudapp.azure.com`). Pour obtenir des nœuds de transaction supplémentaires, incrémentez le numéro de port d’une unité (par exemple, le premier nœud de transaction est sur le port 3000, le deuxième est sur 3001, le troisième est sur 3002, etc.).

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez désormais vous concentrer sur le développement d’applications et de code chaîné sur votre réseau blockchain de consortium Hyperledger.
