---
title: Déployer le modèle de solution Ethereum de consortium de preuve d’autorité sur Azure
description: Utiliser la solution Ethereum de consortium de preuve d’autorité pour déployer et configurer un réseau Ethereum de consortium sur Azure
ms.date: 04/08/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: c3e449c1d6ebaf7c6cb2c35dc9f91d55f569447a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326173"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Déployer le modèle de solution Ethereum de consortium de preuve d’autorité sur Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Le modèle de solution Azure Ethereum de consortium de preuve d’autorité](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) a été conçue pour faciliter le déploiement, la configuration et la gouvernance d’un réseau Ethereum de consortium de preuve d’autorité de plusieurs membres avec des connaissances minimales d’Azure et d’Ethereum.

Moyennant quelques entrées de l’utilisateur et un déploiement par simple clic via le portail Azure, chaque membre peut fournir son empreinte réseau en utilisant Microsoft Azure Compute, une mise en réseau et des services de stockage dans le monde entier. L’empreinte réseau de chaque membre se compose d’un ensemble de nœuds de validateur avec équilibrage de charge grâce auquel une application ou un utilisateur peut interagir pour soumettre des transactions Ethereum.

## <a name="concepts"></a>Concepts

### <a name="terminology"></a>Terminologie

-   **Consensus** : fait de synchroniser des données dans le réseau distribué, par la validation et la création de blocs.

-   **Membres du consortium** : entité qui participe à un consensus sur le réseau Blockchain.

-   **Administrateur** : compte Ethereum utilisé pour gérer la participation d’un membre donné du consortium.

-   **Validateur** : ordinateur associé à un compte Ethereum qui participe à un consensus pour le compte d’un administrateur.

### <a name="proof-of-authority"></a>Preuve d’autorité

Pour les petits nouveaux de la communauté blockchain, la version de cette solution représente une opportunité exceptionnelle d’en savoir plus sur cette technologie de manière simple et configurable via Azure. La preuve de travail est un mécanisme de résistance aux attaques Sybil qui s’appuie sur les coûts de calcul pour réguler automatiquement le réseau et favoriser une participation juste. Ce modèle fonctionne très bien dans des réseaux blockchain ouverts et anonymes, où la concurrence autour de la crypto-monnaie encourage la sécurité sur le réseau. Dans les réseaux privés/de consortium, l’Ether sous-jacent n’a cependant aucune valeur. La preuve d’autorité est un autre protocole plus adapté aux réseaux adossés à des privilèges, dans lesquels tous les participants du consensus sont connus et dignes de confiance. Sans nécessité d’exploration, la preuve d’autorité est plus efficace tout en conservant une bonne tolérance aux panne byzantines.

### <a name="consortium-governance"></a>Gouvernance du consortium

Dans la mesure où la preuve d’autorité utilise une liste autorisée d’autorités de réseau pour conserver le réseau, il est important de prévoir un mécanisme juste pour apporter des modifications à cette liste d’autorisations. Chaque déploiement est fourni avec un ensemble de contrats intelligents et avec un portail pour permettre la gouvernance de cette liste autorisée. Dès lors qu’une modification proposée atteint la majorité des voix auprès des membres du consortium, la modification est mise en œuvre. Cela permet d’ajouter de nouveaux participants du consensus ou de supprimer les participants douteux de manière transparente afin d’encourager un réseau honnête.

### <a name="admin-account"></a>Compte d’administrateur

Lors du déploiement des nœuds de preuve d’autorité, il vous sera demandé de fournir une adresse Ethereum administrateur. Vous pouvez utiliser plusieurs mécanismes pour générer et sécuriser ce compte Ethereum. Une fois que cette adresse est ajoutée en tant qu’une autorité sur le réseau, vous pouvez utiliser ce compte pour participer à la gouvernance. Ce compte d’administrateur sera également utilisé pour déléguer la participation du consensus sur les nœuds de validateur créés dans le cadre de ce déploiement. Dans la mesure où seule l’adresse Ethereum publique est utilisée, chaque administrateur a la possibilité de sécuriser ses clés privées d’une manière conforme au modèle de sécurité de son choix.

### <a name="validator-node"></a>Nœud de validateur

Dans le protocole de preuve d’autorité, les nœuds de validateur se substituent aux nœuds d’explorateur traditionnels. Chaque validateur possède une identité Ethereum unique qui est ajoutée à une liste d’autorisations par contrats intelligents. Dès qu’un validateur est inscrit sur cette liste, il peut participer au processus de création de blocs. Pour en savoir plus sur ce processus, consultez la documentation de Parity relative au [consensus Authority Round](https://wiki.parity.io/Aura). Chaque membre du consortium peut approvisionner deux ou plusieurs nœuds de validateur dans cinq régions, à des fins de géo-redondance. Les nœuds de validateur communiquent avec d’autres nœuds de validateur pour parvenir à un consensus sur l’état du registre distribué sous-jacent.
Pour garantir une participation équitable sur le réseau, chaque membre du consortium n’a pas le droit d’utiliser plus de validateurs que le premier membre sur le réseau (si le premier membre déploie trois validateurs, chaque membre ne peut utiliser qu’un maximum de trois validateurs).

### <a name="identity-store"></a>Magasin d’identités

Puisque chaque membre aura simultanément plusieurs nœuds de validateur en cours d’exécution et que chaque nœud doit avoir une identité autorisée, il est important que les validateurs puissent acquérir en toute sécurité une identité active unique sur le réseau. Pour ce faire, nous avons créé un magasin d’identités. Celui-ci est déployé dans l’abonnement de chaque membre, qui préserve la sécurité des identités Ethereum générées. Au moment du déploiement, le conteneur d’orchestration génère une clé privée Ethereum pour chaque validateur et la stocke dans Azure Key Vault. Avant le démarrage du nœud de parité, il commence par obtenir un bail sur une identité inutilisée afin d’éviter que l’identité soit récupérée par un autre nœud. L’identité est fournie au client, qui lui donne l’autorité nécessaire pour démarrer la création de blocs. Si la machine virtuelle hôte connaît une panne, le bail d’identité est libéré, ce qui permet à un nœud de remplacement de reprendre son identité ultérieurement.

### <a name="bootnode-registrar"></a>Bureau d’enregistrement Bootnode

Pour faciliter la connectivité, chaque membre hébergera un ensemble d’informations de connexion sur le [point de terminaison de l’API de données](#data-api). Ces données incluent une liste de bootnodes fournis en tant que nœuds de peering pour le membre de jointure. Dans le cadre de cette API de données, nous conservons cette liste de bootnodes à jour.

### <a name="bring-your-own-operator"></a>Apportez votre propre opérateur

Il arrive souvent qu’un membre du consortium souhaite participer à la gouvernance du réseau, mais qu’il ne veuille ni exploiter ni entretenir son infrastructure. Contrairement aux systèmes traditionnels, le fait d’avoir un seul opérateur sur le réseau fonctionne très bien sur le modèle décentralisé des systèmes de blockchain. Au lieu d’engager un intermédiaire centralisé pour exploiter un réseau, chaque membre du consortium peut déléguer la gestion de l’infrastructure à l’opérateur de son choix. On obtient ainsi un modèle hybride dans lequel chaque membre peut choisir de gérer sa propre infrastructure ou de déléguer l’opération à un autre partenaire. Le flux de travail de l’opération déléguée fonctionne de la manière suivante :

1.  Le **membre du consortium** génère une adresse Ethereum (détenant la clé privée)

2.  Le **membre du consortium** fournit l’adresse Ethereum publique à **l’opérateur**

3.  **L’opérateur** déploie et configure les nœuds de validateur PoA à l’aide de notre solution Azure Resource Manager

4.  **L’opérateur** fournit le RPC et le point de terminaison de gestion au **membre du consortium**

5.  Le **membre du consortium** utilise sa clé privée pour signer une demande en acceptant les nœuds de validateur que **l’opérateur** a déployés pour participer en son nom

### <a name="azure-monitor"></a>Azure Monitor

Cette solution est également fournie avec Azure Monitor pour suivre les statistiques relatives aux nœuds et au réseau. Cela offre aux développeurs d’applications une visibilité sur la blockchain sous-jacente pour suivre les statistiques de génération de blocs. Les opérateurs réseau peuvent utiliser Azure Monitor pour détecter et prévenir rapidement les interruptions réseau grâce à des statistiques sur l’infrastructure et à des journaux d’activité pouvant faire l’objet de requêtes. Pour plus d’informations, consultez l’article [Surveillance de service](#service-monitoring).

### <a name="deployment-architecture"></a>Architecture de déploiement

#### <a name="description"></a>Description

Cette solution peut déployer un réseau de consortium Ethereum de plusieurs membres reposant sur une ou plusieurs régions. Par défaut, le RPC et les points de terminaison du peering sont accessibles via l’adresse IP publique pour simplifier la connectivité entre les différents abonnements et clouds. Nous vous recommandons d’utiliser les [contrats d’octroi d’autorisations de Parity](https://wiki.parity.io/Permissioning) pour les contrôles d’accès de niveau applicatif. Nous prenons également en charge les réseaux déployés derrière des VPN, qui tirent parti des passerelles de réseau virtuel pour garantir une connectivité entre les abonnements. Ces déploiements étant plus complexes, il est recommandé de commencer par le modèle d’adresse IP publique.

#### <a name="consortium-member-overview"></a>Vue d’ensemble des membre de consortium

Chaque déploiement d’un membre de consortium inclut :

-   des machines virtuelles pour l’exécution des validateurs PoA ;

-   Azure Load Balancer, pour la répartition des requêtes de RPC, de peering et de Governance DApp ;

-   Azure Key Vault, pour sécuriser les identités de validateur ;

-   un Stockage Azure, pour héberger les informations réseau persistantes et coordonner les baux ;

-   Azure Monitor, pour l’agrégation des journaux d’activité et des statistiques de performances ;

-   une passerelle de réseau virtuel (facultative) pour autoriser les connexions VPN sur les réseaux virtuels privés.

![architecture de déploiement](./media/ethereum-poa-deployment/deployment-architecture.png)

Nous utilisons des conteneurs Docker pour des questions de fiabilité et de modularité. Nous utilisons Azure Container Registry pour héberger et diffuser des images avec contrôle de version dans le cadre de chaque déploiement. Les images de conteneur sont composées de la manière suivante :

-   Un orchestrateur

    -   Exécuté une seule fois au cours du déploiement

    -   Génère des identités et des contrats de gouvernance

    -   Stocke les identités dans le magasin d’identités

-   Un client Parity

    -   Utilise les identités du magasin d’identités sous la forme d’un bail

    -   Détecte les homologues et s’y connecte

-   Un agent EthStats

    -   Collecte les journaux d’activité locaux et les statistiques via RPC et les envoie (push) à Azure Monitor

-   Une Governance DApp

    -   Interface Web qui interagit avec les contrats de gouvernance

## <a name="how-to-guides"></a>Guides pratiques
### <a name="governance-dapp"></a>Governance DApp

La décentralisation de la gouvernance est au centre du concept de preuve d’autorité. La Governance DApp est un ensemble de [contrats intelligents](https://github.com/Azure-Samples/blockchain/tree/master/ethereum-on-azure/) prédéployés associés à une application web servant à administrer les autorités sur le réseau.
Les autorités sont divisées en identités d’administrateur et en nœuds de validateur.
Les administrateurs ont le pouvoir de déléguer la participation au consensus à un ensemble de nœuds de validateur. Les administrateurs peuvent également voter l’adhésion ou l’expulsion d’autres administrateurs sur le réseau.

![governance dapp](./media/ethereum-poa-deployment/governance-dapp.png)

-   **Gouvernance décentralisée** : les modifications apportées aux autorités du réseau sont administrées par le biais d’un vote à la chaîne auprès de certains administrateurs.

-   **Délégation de validateur** : les autorités peuvent gérer leurs nœuds de validateur qui sont configurés dans chaque déploiement PoA.

-   **Historique des modifications vérifiable** : chaque modification est enregistrée sur la blockchain pour favoriser la transparence et la vérifiabilité.

#### <a name="getting-started-with-governance"></a>Bien démarrer avec la gouvernance
Pour effectuer n’importe quel type de transactions via la Governance DApp, vous devez tirer parti d’un portefeuille Ethereum.  L’approche la plus simple consiste à utiliser un portefeuille dans le navigateur tel que [MetaMask](https://metamask.io) ; cependant, comme il s’agit de contrats intelligents déployés sur le réseau, vous pouvez également automatiser vos interactions sur le contrat de gouvernance.

Après avoir installé MetaMask, accédez à la Governance DApp dans le navigateur.  Vous pouvez localiser l’URL dans l’e-mail de confirmation de déploiement ou via le portail Azure dans la sortie de déploiement.  Si aucun portefeuille dans le navigateur n’est installé, vous ne pourrez pas effectuer des actions ; toutefois, vous êtes toujours en mesure de lire l’état de l’administrateur.  

#### <a name="becoming-an-admin"></a>Devenir un administrateur
Si vous êtes le premier membre déployé sur le réseau, vous devenez alors automatiquement un administrateur et vos nœuds de parité sont répertoriés comme validateurs.  Si vous joignez le réseau, vous devez être élu en tant qu’administrateur par une majorité (plus de 50 %) de l’ensemble d’administrateurs existants.  Si vous choisissez de ne pas devenir un administrateur, vos nœuds continuent de synchroniser et de valider la blockchain ; toutefois, ils ne participent pas au processus de création de blocs. Afin de démarrer le processus de vote pour devenir un administrateur, cliquez sur __Nommer__, puis entrez vos adresse et alias Ethereum.

![Nommer](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

#### <a name="candidates"></a>Candidats
En sélectionnant l’onglet __Candidats__, vous affichez l’ensemble d’administrateurs candidats.  Une fois qu’un candidat a obtenu la majorité des voix auprès des administrateurs actuels, le candidat est promu administrateur.  Pour voter pour un candidat, sélectionnez la ligne, puis cliquez sur « Vote in » (Voter) en haut.  Si vous changez d’avis sur un vote, vous pouvez sélectionner le candidat et cliquer sur « Rescind vote » (Annuler le vote).

![Candidats](./media/ethereum-poa-deployment/governance-dapp-candidates.png)


#### <a name="admins"></a>Administrateurs
L’onglet __Administrateurs__ affiche l’ensemble actuel des administrateurs et vous permet de voter contre.  Une fois qu’un administrateur perd plus de 50 % de ses soutiens, il est supprimé du réseau en tant qu’administrateur.  Tous les nœuds de validateur qui appartiennent à cet administrateur perdent l’état de validateur et deviennent des nœuds de transaction sur le réseau.  Un administrateur peut être supprimé pour différentes raisons ; toutefois, il incombe au consortium de se mettre d’accord sur une stratégie à l’avance.

![Administrateurs](./media/ethereum-poa-deployment/governance-dapp-admins.png)

#### <a name="validators"></a>Validateurs
En sélectionnant l’onglet __Validateurs__ dans le menu de gauche, vous affichez les nœuds de parité déployés pour cette instance et leur état (type de nœud).  Chaque membre du consortium a un ensemble différent de validateurs dans cette liste, étant donné que cette vue représente le membre de consortium déployé actuel.  S’il s’agit d’une instance qui vient d’être déployée et que vous n’avez pas encore ajouté vos validateurs, l’option Add Validators (Ajouter des validateurs) s’affiche.  Cette option permet de choisir automatiquement un ensemble équilibré au niveau régional de nœuds de parité et de les attribuer à votre ensemble de validateurs.  Si vous avez déployé plus de nœuds que la capacité autorisée, les nœuds restants deviennent des nœuds de transaction sur le réseau.

L’adresse de chaque validateur est affectée automatiquement via le [magasin d’identités](#identity-store) dans Azure.  Si un nœud tombe en panne, il renonce à son identité, ce qui permet à un autre nœud dans votre déploiement de prendre sa place.  Cela garantit que votre participation au consensus est hautement disponible.

![Validateurs](./media/ethereum-poa-deployment/governance-dapp-validators.png)

#### <a name="consortium-name"></a>Nom du consortium
Tout administrateur peut mettre à jour le nom du consortium, affiché en haut de la page.  Sélectionnez l’icône d’engrenage dans la partie supérieure gauche pour mettre à jour le nom du consortium.

#### <a name="account-menu"></a>Menu Compte
Dans la partie supérieure droite figurent votre alias de compte Ethereum et l’icône de votre identité.  Si vous êtes un administrateur, vous avez la possibilité de mettre à jour votre alias.

![Compte](./media/ethereum-poa-deployment/governance-dapp-account.png)

### <a name="deploy-ethereum-proof-of-authority"></a>Déploiement d’Ethereum Proof-of-Authority

Voici un exemple de flux de déploiement impliquant plusieurs parties :

1.  Trois membres génèrent chacun un compte Ethereum à l’aide de MetaMask

2.  Le *Membre A* déploie Ethereum PoA, en fournissant son adresse Ethereum publique

3.  Le *Membre A* fournit l’URL du consortium au *Membre B* et au *Membre C*

4.  Le *Membre B* et le *Membre C* déploient Ethereum PoA, en fournissant leur adresse Ethereum publique et l’URL de consortium du *Membre A*

5.  Le *Membre A* donne son vote au *Membre B* pour devenir administrateur

6.  Le *Membre A* et le *Membre B* votent tous les deux pour que le *Membre C* devienne administrateur

Ce processus requiert un abonnement Azure qui peut prendre en charge le déploiement de plusieurs machines virtuelles et disques managés. Si nécessaire, [créez un compte Azure gratuit](https://azure.microsoft.com/free/) pour commencer.

Une fois que vous disposez d’un abonnement, accédez au portail Azure. Sélectionnez « + », la Place de marché (« Afficher tout »), puis recherchez Ethereum PoA Consortium.

Dans la section suivante, vous allez apprendre à configurer l’empreinte du premier membre dans le réseau. Le flux de déploiement est divisé en cinq étapes : actions de base, régions de déploiement, taille et performances du réseau, paramètres Ethereum et Azure Monitor.

#### <a name="basics"></a>Concepts de base

Sous **De base**, spécifiez les valeurs des paramètres standard pour n’importe quel déploiement, telles que l’abonnement, groupe de ressources et les propriétés de base de machine virtuelle.

Voici une description détaillée de chaque paramètre :

Nom du paramètre|Description|Valeurs autorisées|Valeurs par défaut
---|---|---|---
Créer un réseau ou une connexion à un réseau existant ?|Créer un réseau ou une connexion à un réseau de consortium existant|Créer/Rejoindre existant|Création
Adresse de messagerie (facultatif)|Une fois votre déploiement terminé, vous recevrez par courrier électronique une notification contenant des informations sur votre déploiement.|Adresse e-mail valide|N/D
Nom d’utilisateur de la machine virtuelle|Nom d’utilisateur de l’administrateur de chaque machine virtuelle déployée (caractères alphanumériques uniquement).|1 à 64 caractères|N/D
Type d'authentification|Méthode d’authentification auprès de la machine virtuelle.|Clé publique SSH ou mot de passe|Mot de passe
Mot de passe (Type d’authentification = Mot de passe).|Mot de passe du compte administrateur pour chacune des machines virtuelles déployées.  Le mot de passe doit contenir 3 des éléments suivants : une majuscule, une minuscule, un chiffre et un caractère spécial. Initialement, toutes les machines virtuelles ont le même mot de passe, que vous pouvez modifier après le provisionnement.|12 à 72 caractères|N/D
Clé SSH (Type d’authentification = Clé publique)|Clé de shell sécurisée utilisée pour la session à distance.||N/D
Subscription|Abonnement vers lequel vous déployez le réseau de consortium.||N/D
Groupe de ressources|Groupe de ressources vers lequel vous déployez le réseau de consortium.||N/D
Location|Région Azure du groupe de ressources.||N/D

Voici un exemple de déploiement : ![panneau de base](./media/ethereum-poa-deployment/basic-blade.png)

#### <a name="deployment-regions"></a>Régions de déploiement

Ensuite, sous Régions de déploiement, indiquez des valeurs pour le nombre de régions vers lesquelles déployer le réseau de consortium et la sélection des régions Azure en fonction du nombre de régions donné. L’utilisateur peut effectuer le déploiement dans un maximum de 5 régions. Nous vous recommandons de choisir la première région pour correspondre à l’emplacement du groupe de ressources de la section De base. Pour les réseaux de développement ou de test, il est recommandé de choisir une seule région par membre. Pour des réseaux de production, nous vous recommandons d’effectuer le déploiement dans au moins deux régions pour bénéficier d’une haute disponibilité.

Voici une description détaillée de chaque paramètre :

  Nom du paramètre|Description|Valeurs autorisées|Valeurs par défaut
  ---|---|---|---
  Nombre de régions|Nombre de régions dans lesquelles déployer le réseau de consortium|1, 2, 3, 4, 5|1
  Première région|Première région dans laquelle déployer le réseau de consortium|Toutes les régions sont autorisées.|N/D
  Deuxième région|Autre région dans laquelle déployer le réseau de consortium (visible uniquement lorsque le nombre de régions sélectionné est 2).|Toutes les régions sont autorisées.|N/D
  Troisième région|Troisième région dans laquelle déployer le réseau de consortium (visible uniquement lorsque le nombre de régions sélectionné est 3).|Toutes les régions sont autorisées.|N/D
  Quatrième région|Quatrième région dans laquelle déployer le réseau de consortium (visible uniquement lorsque le nombre de régions sélectionné est 4).|Toutes les régions sont autorisées.|N/D
  Cinquième région|Cinquième région dans laquelle déployer le réseau de consortium (visible uniquement lorsque le nombre de régions sélectionné est 5).|Toutes les régions sont autorisées.|N/D

Voici un exemple de déploiement : ![régions de déploiement](./media/ethereum-poa-deployment/deployment-regions.png)

#### <a name="network-size-and-performance"></a>Taille et performances du réseau

Ensuite, sous Taille et performances du réseau, spécifiez des valeurs pour la taille du réseau de consortium, telles que le nombre et la taille des nœuds de validateur.
La taille de stockage du nœud de validateur détermine la taille potentielle de la blockchain. Ce paramètre peut être modifié après le déploiement.

Voici une description détaillée de chaque paramètre :

  Nom du paramètre|Description|Valeurs autorisées|Valeurs par défaut
  ---|---|---|---
  Nombre de nœuds de validateur avec équilibrage de charge|Nombre de nœuds de validateur à configurer dans le réseau.|2-15|2
  Performances de stockage des nœuds de validateur|Type de disque managé prenant en charge chacun des nœuds de validateur déployés.|SSD Standard ou Premium|SSD Standard
  Taille de machine virtuelle du nœud de validateur|Taille de machine virtuelle utilisée pour les nœuds de validateur.|Standard A, Standard D, Standard D-v2, Standard F series, Standard DS et Standard FS|Standard D1 v2

[Tarification Azure Storage](https://azure.microsoft.com/pricing/details/managed-disks/)

[Détails des tarifs des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

La machine virtuelle et le niveau de stockage affectent les performances réseau.  Nous vous recommandons les références SKU suivantes selon la rentabilité voulue :

  Référence SKU de machine virtuelle|Niveau de stockage|Prix|Débit|Latence
  ---|---|---|---|---
  F1|SSD Standard|bas|basse|élevée
  D2_v3|SSD Standard|moyen|moyen|moyenne
  F16s|SSD Premium|élevée|élevée|basse

Voici un exemple de déploiement : ![taille et performances du réseau](./media/ethereum-poa-deployment/network-size-and-performance.png)

#### <a name="ethereum-settings"></a>Paramètres Ethereum

Ensuite, sous Paramètres Ethereum, spécifiez les paramètres de configuration Ethereum, comme l’ID réseau et le mot de passe du compte Ethereum ou le bloc Genesis.

Voici une description détaillée de chaque paramètre :

  Nom du paramètre|Description|Valeurs autorisées|Valeurs par défaut
  ---|---|---|---
ID du membre de consortium|ID associé à chaque membre participant au réseau de consortium, qui sert à configurer les espaces d’adressage IP pour éviter les collisions. Dans le cas d’un réseau privé, l’ID de membre doit être unique dans les différentes organisations du même réseau.  Un ID de membre unique est nécessaire même si une organisation effectue des déploiements dans plusieurs régions. Notez la valeur de ce paramètre, car vous devrez le partager avec les autres membres participants pour éviter tout risque de collision.|0-255|N/D
ID réseau|ID du réseau Ethereum de consortium en cours de déploiement.  Chaque réseau Ethereum possède son propre ID de réseau, 1 étant l’ID du réseau public.|5 - 999 999 999|10101010
Adresse Ethereum de l’administrateur|Adresse du compte Ethereum utilisé pour votre participation à la gouvernance PoA.  Nous vous recommandons d’utiliser MetaMask pour générer une adresse Ethereum.|42 caractères alphanumériques commençant par 0x|N/D
Options avancées|Options avancées pour les paramètres Ethereum|Activer ou désactiver|Désactiver
Adresse IP publique (Options avancées = Activer)|Déploie le réseau derrière une passerelle de réseau virtuel et supprime l’accès de peering. Si cette option est sélectionnée, tous les membres doivent utiliser une passerelle de réseau virtuel pour garantir la compatibilité de la connexion.|Adresse IP publique, réseau virtuel privé|Adresse IP publique
Limite de gaz par bloc (Options avancées = Activer)|Limite de gaz par bloc de départ du réseau|Toute valeur numérique|50000000
Période de rescellage des blocs (s)|Fréquence à laquelle des blocs vides sont créés en l’absence de transaction sur le réseau. Une fréquence supérieure aboutit à une finalité plus rapide, mais avec des coûts de stockage plus élevés.|Toute valeur numérique|15
Contrat d’autorisation de transaction (Options avancées = Activer)|Bytecode du contrat d’autorisation de transaction. Restreint le déploiement et l’exécution de contrats intelligents à une liste autorisée de comptes Ethereum.|Bytecode du contrat|N/D

Voici un exemple de déploiement : ![paramètres ethereum](./media/ethereum-poa-deployment/ethereum-settings.png)

#### <a name="monitoring"></a>Surveillance

Le panneau de surveillance vous permet de configurer une ressource de journaux Azure Monitor pour votre réseau. L’agent de supervision collecte et fournit des métriques et journaux d’activité utiles à partir de votre réseau, en offrant la possibilité de vérifier rapidement l’intégrité du réseau ou les problèmes de débogage.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

  Nom du paramètre|Description|Valeurs autorisées|Valeurs par défaut
  ---|---|---|---
Surveillance|Option permettant d’activer la supervision|Activer ou désactiver|Activer
Se connecter à des journaux Azure Monitor existants|Créer une instance de journaux Azure Monitor ou rejoindre une instance existante|Créer ou rejoindre existant|Création
Emplacement Azure Monitor (Se connecter à des journaux Azure Monitor existants = Créer)|Région dans laquelle l’instance de journaux Azure Monitor sera déployée|Toutes les régions de journaux Azure Monitor|N/D
ID de l’espace de travail Log Analytics existant (Se connecter à des journaux Azure Monitor existants = Rejoindre existant)|ID d’espace de travail de l’instance de journaux Azure Monitor existante||N/D
Clé primaire de l’instance Log Analytics existante (Se connecter à des journaux Azure Monitor existants = Rejoindre existant)|Clé primaire utilisée pour se connecter à l’instance de journaux Azure Monitor existante||N/D


Voici un exemple de déploiement : ![azure monitor](./media/ethereum-poa-deployment/azure-monitor.png)

#### <a name="summary"></a>Résumé

Cliquez sur le panneau de résumé pour passer en revue les entrées spécifiées et pour exécuter une validation de base avant le déploiement. Avant le déploiement, vous pouvez télécharger le modèle et les paramètres.

Passez en revue les conditions juridiques et de confidentialité, puis cliquez sur « Achat » pour lancer le déploiement. Si le déploiement comprend des passerelles de réseau virtuel, il peut prendre 45 à 50 minutes.

#### <a name="post-deployment"></a>Après le déploiement

##### <a name="deployment-output"></a>Sortie du déploiement

Une fois le déploiement terminé, vous serez en mesure d’accéder aux paramètres nécessaires à partir de votre e-mail de confirmation ou via le portail Azure. Ces paramètres contiennent les éléments suivants :

-   Point de terminaison Ethereum RPC

-   URL du tableau de bord de gouvernance

-   URL Azure Monitor

-   URL des données

-   ID de ressource de la passerelle de réseau virtuel (facultatif)

##### <a name="confirmation-email"></a>E-mail de confirmation

Si vous fournissez une adresse de messagerie ([Section De base](#basics)), un e-mail est envoyé à l’adresse de messagerie avec les informations de sortie du déploiement.

![e-mail de déploiement](./media/ethereum-poa-deployment/deployment-email.png)

##### <a name="portal"></a>Portail

Une fois que le déploiement a été correctement effectué et que toutes les ressources ont été configurées, vous serez en mesure de visualiser les paramètres de sortie dans votre groupe de ressources.

1.  Localisez votre groupe de ressources dans le portail

2.  Accédez à *Déploiements*

3.  Sélectionnez le principal déploiement portant le même nom que votre groupe de ressources

4.  Sélectionnez *Sorties*

### <a name="growing-the-consortium"></a>Développer le consortium

Pour développer votre consortium, vous devez d’abord vous connecter au réseau physique.
Cette première étape s’effectue en toute transparence si vous utilisez un déploiement basé sur une adresse IP publique. Si vous effectuez le déploiement derrière un VPN, consultez la section [Connexion des passerelles de réseau virtuel](#connecting-vnet-gateways) pour exécuter la connexion réseau dans le cadre du déploiement du nouveau membre.  Une fois votre déploiement terminé, utilisez la [Governance DApp](#governance-dapp) pour devenir un administrateur réseau.

#### <a name="new-member-deployment"></a>Déploiement du nouveau membre

1.  Partagez les informations suivantes avec le membre participant. Vous trouverez ces informations dans l’e-mail reçu après le déploiement ou dans la sortie du déploiement via le portail.

    -  URL de données du consortium

    -  Nombre de nœuds que vous avez déployés

    -  ID de ressource de la passerelle de réseau virtuel (si vous utilisez un VPN)

2.  Le membre à déployer doit utiliser la [même solution](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) lors du déploiement de sa présence sur le réseau, en gardant à l’esprit ce qui suit :

    -  Sélectionnez *Rejoindre existant*

    -  Choisissez le même nombre de nœuds de validateur que le reste des membres sur le réseau, pour garantir une représentation équitable

    -  Utilisez la même adresse Ethereum que celle qui a été fournie à l’étape précédente

    -  Passez *l’URL de données du consortium* sur l’onglet *Paramètres Ethereum*

    -  Si le reste du réseau est derrière un VPN, sélectionnez *Réseau virtuel privé* sous la section Avancé

#### <a name="connecting-vnet-gateways"></a>Connexion des passerelles de réseau virtuel

Vous pouvez ignorer cette étape si vous avez effectué le déploiement en utilisant les paramètres d’adresse IP publique par défaut. Dans le cas d’un réseau privé, les différents membres sont connectés via des connexions de passerelle de réseau virtuel. Pour qu’un membre puisse rejoindre le réseau et voir le trafic de transaction, un membre existant doit effectuer une configuration finale sur la passerelle VPN pour accepter la connexion. Cela signifie que les nœuds Ethereum du nouveau membre ne fonctionneront qu’une fois la connexion établie. Il est recommandé de créer des connexions réseau redondantes (maillage) dans le consortium afin de réduire les risques d’un point de défaillance unique.

Une fois que le nouveau membre est déployé, le membre existant doit terminer la connexion bidirectionnelle en configurant une connexion de passerelle de réseau virtuel pour le nouveau membre. Pour ce faire, le membre existant a besoin des éléments suivants :

1.  L’ID de ressource de la passerelle de réseau virtuel du membre qui se connecte (voir la sortie du déploiement)

2.  La clé de connexion partagée

Le membre existant doit exécuter le script PowerShell suivant pour terminer la connexion. Nous recommandons d’utiliser Azure Cloud Shell, accessible depuis la barre de navigation se trouvant en haute à droite du portail.

![cloud shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

### <a name="service-monitoring"></a>Surveillance des services

Vous pouvez localiser votre portail Azure Monitor en suivant le lien contenu dans l’e-mail de déploiement ou en recherchant le paramètre dans la sortie du déploiement \[OMS\_PORTAL\_URL\].

Le portail affiche tout d’abord les statistiques réseau de haut niveau et une vue d’ensemble des nœuds.

![catégories monitor](./media/ethereum-poa-deployment/monitor-categories.png)

Cliquez sur **Vue d’ensemble des nœuds** pour accéder à un portail contenant des statistiques sur l’infrastructure par nœud.

![stats des nœuds](./media/ethereum-poa-deployment/node-stats.png)

En sélectionnant **Statistiques réseau**, vous accédez aux statistiques du réseau Ethereum.

![stats réseau](./media/ethereum-poa-deployment/network-stats.png)

#### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

Derrière ces tableaux de bord se trouve un ensemble de journaux d’activité bruts que vous pouvez interroger. Vous pouvez utiliser ces journaux d’activité bruts pour personnaliser les tableaux de bord, examiner les échecs ou configurer des seuils d’alerte. Vous trouverez ci-dessous un ensemble d’exemples de requêtes que vous pouvez exécuter dans l’outil de recherche dans les journaux :

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>Répertorier les blocs qui ont été signalés par plusieurs validateurs. Utile pour trouver des branches de la chaîne.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>Obtenir le nombre moyen d’homologues pour un nœud de validateur spécifié en moyenne sur des compartiments de 5 minutes.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

### <a name="ssh-access"></a>Accès SSH

Pour des raisons de sécurité, l’accès au port SSH est refusé par défaut par une règle de sécurité de groupe de réseau. Pour accéder aux instances des machines virtuelles dans le réseau PoA, vous devez modifier cette règle en sélectionnant le paramètre \"Autoriser\"

1.  Commencez dans la section Vue d’ensemble du groupe de ressources déployé à partir du portail Azure.

    ![vue d’ensemble ssh](./media/ethereum-poa-deployment/ssh-overview.png)

2.  Sélectionnez le groupe de sécurité réseau pour la région de la machine virtuelle à laquelle vous voulez accéder

    ![ssh nsg](./media/ethereum-poa-deployment/ssh-nsg.png)

3.  Sélectionnez la règle \"allow-ssh\"

    ![ssh-allow](./media/ethereum-poa-deployment/ssh-allow.png)

4.  Cliquez sur Autoriser sous \"Action\"

    ![ssh enable allow](./media/ethereum-poa-deployment/ssh-enable-allow.png)

5.  Cliquez sur \"Enregistrer\" (plusieurs minutes peuvent être nécessaire pour prendre en compte les modifications)

Vous pouvez maintenant vous connecter à distance aux machines virtuelles des nœuds de validateur via SSH avec le nom d’utilisateur administrateur et le mot de passe/la clé SSH fournis.
La commande SSH à exécuter pour accéder au premier nœud de validateur est répertoriée dans le paramètre de sortie du déploiement de modèle, « SSH\_TO\_FIRST\_VL\_NODE\_REGION1 » (pour l’exemple de déploiement : ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com). Pour obtenir des nœuds de transaction supplémentaires, incrémentez le numéro de port d’une unité (par exemple, le premier nœud de transaction est sur le port 4000).

Si vous avez déployé sur plusieurs régions, modifiez la commande ci-dessus en utilisant le nom DNS ou l’adresse IP de l’équilibreur de charge dans cette région. Pour trouver le nom DNS ou l’adresse IP des autres régions, recherchez la ressource avec la convention d’affectation de noms \*\*\*\*\*-lbpip-reg\# et affichez les propriétés de son nom DNS et de son adresse IP.

### <a name="azure-traffic-manager-load-balancing"></a>Équilibrage de charge dans Azure Traffic Manager

Azure Traffic Manager vous permet de réduire les temps d’arrêt et d’améliorer la réactivité du réseau PoA en acheminant le trafic entrant entre plusieurs déploiements dans différentes régions. Les contrôles d’intégrité intégrés et le réacheminement automatique garantissent une haute disponibilité des points de terminaison RPC et de la Governance DApp. Cette fonctionnalité est utile si vous avez déployé dans plusieurs régions et que vous êtes prêt pour la production.

Utilisez Traffic Manager pour :

-   améliorer la disponibilité du réseau PoA avec le basculement automatique ;

-   augmenter la réactivité de vos réseaux en acheminant les utilisateurs finaux vers l’emplacement Azure avec un minimum de latence réseau.

Si vous décidez de créer un profil Traffic Manager, vous pouvez utiliser le nom DNS du profil pour accéder à votre réseau. Une fois les autres membres du consortium ajoutés au réseau, vous pouvez également utiliser Traffic Manager pour équilibrer la charge entre les validateurs qui ont été déployés.

#### <a name="creating-a-traffic-manager-profile"></a>Création d’un profil Traffic Manager

Recherchez et sélectionnez \"Profil Traffic Manager\" après avoir cliqué sur le bouton \"Créer une ressource\" dans le portail Azure.

![recherche d’azure traffic manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

Donnez un nom unique au profil et sélectionnez le groupe de ressources qui a été créé pendant le déploiement de PoA. Cliquez sur le bouton Créer pour déployer.

![créer un profil traffic manager](./media/ethereum-poa-deployment/traffic-manager-create.png)

Une fois déployé, sélectionnez l’instance dans le groupe de ressources. Le nom DNS permettant d’accéder à Traffic Manager se trouve sous l’onglet Vue d’ensemble

![Rechercher le DNS Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-dns.png)

Sélectionnez l’onglet Points de terminaison et cliquez sur le bouton Ajouter. Donnez un nom unique au point de terminaison. Modifiez le type de ressource Cible en Adresse IP publique. Sélectionnez l’adresse IP publique de l\'équilibreur de charge de la première région.

![acheminement de traffic manager](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Répétez l’opération pour chaque région dans le réseau déployé. Une fois que les points de terminaison se trouvent à l’état \"activé\", leur charge et leur région seront automatiquement équilibrées sur le nom DNS de Traffic Manager. Vous pouvez maintenant utiliser ce nom DNS à la place du paramètre \[CONSORTIUM\_DATA\_URL\] dans les autres étapes de ce document.

### <a name="data-api"></a>API de données

Chaque membre du consortium héberge les informations nécessaires pour que d’autres utilisateurs puissent se connecter au réseau. Le membre existant fournira le paramètre [CONSORTIUM_DATA_URL] avant le déploiement du membre. Lors du déploiement, un membre rejoignant le consortium récupérera les informations à partir de l’interface JSON au point de terminaison suivant :

`<CONSORTIUM_DATA_URL>/networkinfo`

La réponse contiendra des informations utiles sur la joindre de membres (bloc Genesis, ABI de contrat de l’ensemble de validateurs, bootnodes), ainsi que des informations utiles pour le membre existant (adresses des validateurs). Nous vous encourageons à utiliser cette standardisation pour étendre le consortium à travers les fournisseurs de clouds. Cette API renvoie une réponse au format JSON avec la structure suivante :
```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```
## <a name="tutorials"></a>Tutoriels

### <a name="programmatically-interacting-with-a-smart-contract"></a>Interaction par programmation avec un contrat intelligent

> [!WARNING]
> N’envoyez jamais votre clé Ethereum privée sur le réseau ! Assurez-vous que chaque transaction est d’abord signée en local avant d’être envoyée sur le réseau.

Dans l’exemple suivant, nous utilisons *ethereumjs-wallet* pour générer une adresse Ethereum, *ethereumjs-tx*, à signer en local, et *web3*, pour envoyer la transaction brute au point de terminaison Ethereum RPC.

Dans cet exemple, nous allons utiliser ce simple contrat intelligent Hello World :

```javascript
pragma solidity ^0.4.11;
contract postBox {
    string message;
    function postMsg(string text) public {
        message = text;
    }
    function getMsg() public view returns (string) {
        return message;
    }
}
```

Cet exemple suppose que le contrat est déjà déployé. Vous pouvez utiliser *solc* et *web3* pour le déploiement d’un contrat par programmation. Commencez par installer les modules suivants :
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
Ce script nodeJS exécutera les opérations suivantes :

-   Construire une transaction brute : postMsg

-   Signer la transaction à l’aide de la clé privée générée

-   Envoyer la transaction signée au réseau Ethereum

```javascript
var ethereumjs = require('ethereumjs-tx')
var wallet = require('ethereumjs-wallet')
var Web3 = require('web3')

// TODO Replace with your contract address
var address = "0xfe53559f5f7a77125039a993e8d5d9c2901edc58";
var abi = [{"constant": false,"inputs": [{"name": "text","type": "string"}],"name": "postMsg","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "getMsg","outputs": [{"name": "","type": "string"}],"payable": false,"stateMutability": "view","type": "function"}];

// Generate a new Ethereum account
var account = wallet.generate();
var accountAddress = account.getAddressString()
var privateKey = account.getPrivateKey();

// TODO Replace with your RPC endpoint
var web3 = new Web3(new Web3.providers.HttpProvider(
    "http://testzvdky-dns-reg1.eastus.cloudapp.azure.com:8545"));

// Get the current nonce of the account
web3.eth.getTransactionCount(accountAddress, function (err, nonce) {
   var data = web3.eth.contract(abi).at(address).postMsg.getData("Hello World");
   var rawTx = {
     nonce: nonce,
     gasPrice: '0x00',
     gasLimit: '0x2FAF080',
     to: address,
     value: '0x00',
     data: data
   }
   var tx = new ethereumjs(rawTx);

   tx.sign(privateKey);

   var raw = '0x' + tx.serialize().toString('hex');
   web3.eth.sendRawTransaction(raw, function (txErr, transactionHash) {
     console.log("TX Hash: " + transactionHash);
     console.log("Error: " + txErr);
   });
 });
```

### <a name="deploy-smart-contract-with-truffle"></a>Déployer un contrat intelligent avec Truffle

-   Installer les bibliothèques nécessaires

```javascript
npm init

npm install truffle-hdwallet-provider --save
```
-   Dans truffle.js, ajoutez le code suivant pour déverrouiller votre compte MetaMask et configurer le nœud PoA comme point d’entrée en fournissant l’expression mnémonique (MetaMask / Paramètres / Reveal Seed Words [Révéler les mots initiaux])

```javascript
var HDWalletProvider = require("truffle-hdwallet-provider");

var rpc_endpoint = "XXXXXX";
var mnemonic = "twelve words you can find in metamask/settings/reveal seed words";

module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    poa: {
      provider: new HDWalletProvider(mnemonic, rpc_endpoint),
      network_id: 3,
      gasPrice : 0
    }
  }
};

```

-   Déployer sur le réseau PoA

```javascript
$ truffle migrate --network poa
```

### <a name="debug-smart-contract-with-truffle"></a>Déboguer un contrat intelligent avec Truffle

Truffle dispose d’un réseau de développement local qui est disponible pour le débogage du contrat intelligent. Le didacticiel complet est accessible [ici](https://truffleframework.com/tutorials/debugging-a-smart-contract).

### <a name="webassembly-wasm-support"></a>Prise en charge de WebAssembly (WASM)

La prise en charge de WebAssembly est déjà activée pour vous sur les réseaux PoA récemment déployés. Elle permet de développer des contrats intelligents dans n’importe quel langage transpilable en Web-Assembly (Rust, C, C++). Pour plus d'informations, consultez les liens ci-dessous;

-   Vue d’ensemble de WebAssembly avec Parity : <https://wiki.parity.io/WebAssembly-Home>

-   Didacticiel de Parity Tech : <https://github.com/paritytech/pwasm-tutorial>

## <a name="reference"></a>Informations de référence

### <a name="faq"></a>Forum Aux Questions

#### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Je remarque un grand nombre de transactions sur le réseau que je n\'ai pas envoyées. D’où viennent-elles ?

Il est dangereux de déverrouiller [l’API personnelle](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html). Les bots écoutent les comptes Ethereum déverrouillés et tentent d’en extraire des fonds. Le bot suppose que ces comptes contiennent real-ether et essaie d’être le premier à permettre l’équilibre. N’activez pas l’API personnelle sur le réseau. Il est préférable de signer au préalable les transactions manuellement à l’aide d’un portefeuille comme MetaMask ou par programmation comme indiqué dans la section [Interaction par programmation avec un contrat intelligent](#programmatically-interacting-with-a-smart-contract).

#### <a name="how-to-ssh-onto-a-vm"></a>Comment exécuter SSH sur une machine virtuelle ?

Le port SSH n’est pas exposé pour des raisons de sécurité. Suivez [ce guide pour activer le port SSH](#ssh-access).

#### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Comment configurer un membre d’audit ou des nœuds de transaction ?

Les nœuds de transaction sont un ensemble de clients Parity homologués avec le réseau, mais qui ne participent pas à un consensus. Ces nœuds peuvent toujours être utilisés pour soumettre des transactions Ethereum et lire l’état du contrat intelligent.
Ils fonctionnent bien comme mécanisme de vérifiabilité pour les membres du consortium ne faisant pas autorité sur le réseau. Pour cela, suivez simplement l’étape 2 de la section Développer le consortium.

#### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Pourquoi les transactions MetaMask prennent-elles du temps ?

Pour s’assurer que les transactions sont reçues dans le bon ordre, chaque transaction Ethereum est fournie avec un nonce incrémentiel. Si vous avez utilisé un compte dans MetaMask sur un réseau différent, vous devez réinitialiser la valeur du nonce. Cliquez sur l’icône Paramètres (3 barres), Paramètres, Réinitialiser le compte. L’historique des transactions sera effacé et vous pourrez désormais soumettre à nouveau la transaction.

#### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Dois-je indiquer les frais de gaz dans MetaMask ?

L’Ether n’a aucune utilité dans Proof-of-Authority Consortium. Par conséquent, il est inutile de spécifier les frais de gaz lors de l’envoi des transactions dans MetaMask.

#### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Que dois-je faire si mon déploiement échoue en raison de l’échec de provisionnement d’Azure OMS ?

La supervision est une fonctionnalité facultative. Dans certains cas rares où votre déploiement échoue en raison de l’incapacité à provisionner correctement une ressource Azure Monitor, vous pouvez redéployer sans Azure Monitor.

#### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Les déploiements d’adresses IP publiques sont-ils compatibles avec les déploiements de réseau privé ?

Non, le peering exige une communication bidirectionnelle, ce qui signifie que l’ensemble du réseau doit être public ou privé.

#### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Quel est le débit des transactions attendu de Proof-of-Authority ?

Le débit des transactions dépend fortement des types de transactions et de la topologie du réseau.  À l’aide de transactions simples, nous avons testé une moyenne de 400 transactions par seconde avec un réseau déployé dans plusieurs régions.

#### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Comment s’abonner aux événements de contrats intelligents ?

Ethereum Proof-of-Authority prend désormais en charge les WebSockets.  Vérifiez votre e-mail de déploiement ou sortie du déploiement pour localiser l’URL et le port du WebSocket.

## <a name="next-steps"></a>Étapes suivantes

Apprenez à utiliser la solution [Ethereum Proof-of-Authority Consortium](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium).
