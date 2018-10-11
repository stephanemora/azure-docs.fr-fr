---
title: Modèle de solution de consortium de preuve de travail Ethereum
description: Utilisez le modèle de solution de consortium de preuve de travail Ethereum pour déployer et configurer un réseau Ethereum de consortium de plusieurs membres.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 823bea9bac8ff270d5b5c02e3b76a2f7236c9c99
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48241012"
---
# <a name="ethereum-proof-of-work-consortium-solution-template"></a>Modèle de solution de consortium de preuve de travail Ethereum

Le modèle de solution de consortium de preuve de travail Ethereum est conçu pour faciliter et accélérer le déploiement et la configuration d’un réseau Ethereum de consortium de plusieurs membres avec des connaissances minimales au sujet d’Azure et d’Ethereum.

En quelques entrées d’utilisateur et avec un déploiement par simple clic via le portail Azure, chaque membre peut fournir son empreinte réseau, à l’aide de Microsoft Azure Compute, de la mise en réseau et de services de stockage dans le monde entier. L’empreinte réseau de chaque membre se compose d’un ensemble de nœuds de transaction avec équilibrage de charge grâce auquel une application ou un utilisateur peut interagir pour soumettre des transactions, un ensemble de nœuds d’exploration de données pour enregistrer les transactions et une passerelle VPN. Une étape suivante connecte les passerelles pour créer un réseau blockchain de plusieurs membres entièrement configuré.

## <a name="about-blockchain"></a>À propos de blockchain

Pour les petits nouveaux de la communauté blockchain, la version de cette solution représente une opportunité exceptionnelle d’en savoir plus sur cette technologie de manière simple et configurable via Azure. Toutefois, pour commencer, nous vous recommandons de déployer la topologie de réseau Ethereum autonome simple avec cette procédure pas à pas guidée, puis de créer les réseaux de consortium de plusieurs membres.

### <a name="mining-node-details"></a>Détails des nœuds d’exploration de données

Les nœuds qui explorent les transactions sont séparés des nœuds qui acceptent des transactions pour garantir que ces deux actions ne sont pas en concurrence pour les mêmes ressources.

Un membre du consortium peut configurer jusqu’à cinq régions contenant un ou plusieurs nœuds d’exploration de données, soutenus par un disque managé. Un ou plusieurs nœuds dans la région sont configurés en tant que nœud de démarrage pour prendre en charge la fonctionnalité de détection dynamique des nœuds dans le réseau. Les nœuds d’exploration de données communiquent avec d’autres nœuds d’exploration de données pour parvenir à un consensus sur l’état du registre distribué sous-jacent. Il n’est pas nécessaire pour votre application ait connaissance de ces nœuds ou communique avec eux. En se concentrant sur les réseaux privés, les nœuds d’exploration de données sont isolés du trafic Internet public entrant pour ajouter une couche de protection secondaire. Le trafic sortant est autorisé, mais pas pour le port de détection Ethereum.

Tous les nœuds ont une version stable du client Go Ethereum (Geth) et sont configurés comme nœuds d’exploration de données. Si vous n’avez pas fourni un bloc de genèse personnalisé, tous les nœuds utilisent la même adresse Ethereum et une paire de clés qui est protégée par le mot de passe du compte Ethereum. La phrase secrète Ethereum que vous avez indiquée est utilisée pour générer le compte par défaut (coinbase) de chaque nœud d’exploration de données. Ces nœuds d’exploration de données analysent et collectent des frais ajoutés à ce compte.

Le nombre de nœuds d’exploration de données par membre du consortium dépend de la taille globale du réseau et de la puissance de hachage dédiée à chaque membre. Plus le réseau est vaste, plus de nœuds doivent être compromis pour bénéficier d’un avantage déloyal. Le modèle prend en charge jusqu’à 15 nœuds d’exploration de données par région, configurés à l’aide de groupes de machines virtuelles identiques.

### <a name="transaction-node-details"></a>Détails des nœuds de transaction

Un membre de consortium possède également un ensemble de nœuds de transaction avec équilibrage de charge. Ces nœuds sont accessibles depuis l’extérieur du réseau virtuel, afin que les applications puissent les utiliser pour soumettre des transactions ou exécuter des contrats dynamiques au sein du réseau blockchain. Tous les nœuds ont une version stable du client Go Ethereum (Geth) et sont configurés pour tenir à jour une copie complète du registre distribué. Si aucun bloc de genèse personnalisé n’est fourni, ces nœuds utilisent le même compte Ethereum, protégé par le mot de passe du compte Ethereum.

Les nœuds de transaction sont équilibrés en charge au sein d’un groupe à haute disponibilité pour maintenir la haute disponibilité. Le modèle prend en charge jusqu’à 5 nœuds de transaction configurés à l’aide de groupes de machines virtuelles identiques.

### <a name="log-analytics-details"></a>Détails de Log Analytics

Chaque déploiement également crée instance Log Analytics ou peut rejoindre une instance existante. Cela permet de surveiller diverses mesures de performances de chaque machine virtuelle composant le réseau déployé.

## <a name="deployment-architecture"></a>Architecture de déploiement

### <a name="description"></a>Description

Ce modèle de solution peut déployer un ou plusieurs réseaux de consortium Ethereum de plusieurs membres reposant sur une région. Le réseau virtuel de chaque région est connecté à une autre région dans une topologie de chaîne via les passerelles de réseau virtuel et les ressources de connexion. Il configure également un bureau d’enregistrement qui contient les informations requises de tous les nœuds d’exploration de données et de transaction déployés dans chaque région.

### <a name="standalone-and-consortium-leader-overview"></a>Vue d’ensemble des leaders autonomes et de consortium

![Vue d’ensemble des leaders autonomes et de consortium](./media/ethereum-deployment/leader-overview.png)

### <a name="joining-consortium-member-overview"></a>Vue d’ensemble de la jointure d’un membre de consortium

![Vue d’ensemble de la jointure d’un membre de consortium](./media/ethereum-deployment/member-overview.png)

## <a name="getting-started"></a>Prise en main

Ce processus requiert un abonnement Azure qui peut prendre en charge le déploiement groupe de machines virtuelles identiques et disques managés. Si nécessaire, créez un compte Azure gratuit pour commencer.

Une fois que vous disposez d’un abonnement, accédez au portail Azure. Sélectionnez **+Créer une ressource**, Place de marché (Afficher tout) et recherchez **Consortium de preuve de travail Ethereum**.

Le déploiement de modèle vous guidera dans la configuration de l’empreinte du premier membre dans le réseau. Le flux de déploiement est divisé en cinq étapes : actions de base, suite de gestion des opérations, régions de déploiement, taille et performances du réseau, et paramètres Ethereum.

### <a name="basics"></a>Concepts de base

Sous **De base**, spécifiez les valeurs des paramètres standard pour n’importe quel déploiement, telles que l’abonnement, groupe de ressources et les propriétés de base de machine virtuelle.

![Concepts de base](./media/ethereum-deployment/sample-deployment.png)

Nom du paramètre|Description| Valeurs autorisées|Valeurs par défaut
---|---|---|---
Créer un réseau ou une connexion à un réseau existant ?|Créer un réseau ou une connexion à un réseau de consortium existant|Créer/Rejoindre existant|Création
Déployer un réseau qui fera partie d’un consortium ?|Un réseau de consortium permet aux déploiements futurs de rejoindre ce réseau (visible lorsque l’option *Créer* est sélectionnée).|Consortium autonome|Standalone
Préfixe de ressource |Chaîne utilisée comme base pour les noms de ressources (2 à 4 caractères alphanumériques). Un hachage unique est ajouté au début de la chaîne pour certaines ressources, tandis que des informations propres à la ressource sont ajoutées à la fin.|Caractères alphanumériques (entre 2 et 4)|N/D
Nom d’utilisateur de la machine virtuelle| Nom d’utilisateur de l’administrateur de chaque machine virtuelle déployée (caractères alphanumériques uniquement).|Entre 1 et 64 caractères |gethadmin
Type d'authentification|Méthode d’authentification auprès de la machine virtuelle. |Mot de passe ou clé publique SSH|Mot de passe
Mot de passe (Type d’authentification = Mot de passe).|Mot de passe du compte administrateur pour chaque machine virtuelle déployée. Le mot de passe doit contenir 3 des éléments suivants : une minuscule, une majuscule, un chiffre et un caractère spécial. <br />Initialement, toutes les machines virtuelles ont le même mot de passe, que vous pouvez modifier après l’approvisionnement.|Entre 12 et 72 caractères|N/D
Clé SSH (Type d’authentification = Clé publique)|Clé de shell sécurisée utilisée pour la session à distance.|| N/D
Abonnement| Abonnement vers lequel vous déployez le réseau de consortium.||N/D
Groupe de ressources| Groupe de ressources sur lequel déployez le réseau de consortium.||N/D
Lieu| Région Azure du groupe de ressources. ||N/D



### <a name="operations-management-suite"></a>Operations Management Suite

Le panneau Operations Management Suite (OMS) permet de configurer une ressource OMS pour votre réseau. OMS collecte et fournit des métriques et journaux utiles à partir de votre réseau, en offrant la possibilité de vérifier rapidement l’intégrité du réseau ou les problèmes de débogage. L’offre gratuite OMS échoue une fois que la capacité est atteinte.

![Création d’une instance OMS](./media/ethereum-deployment/new-oms.png)

Nom du paramètre|Description| Valeurs autorisées|Valeurs par défaut
---|---|---|---
Se connecter à une instance OMS existante|Créer une instance Log Analytics ou rejoindre une instance existante|Créer/Rejoindre existant|Créer un emplacement Log Analytics|Région où sera déployée la nouvelle instance Log Analytics (visible si l’option *Créer* est sélectionnée).
ID d’espace de travail OMS existant|ID d’espace de travail de l’instance existante (visible si l’option *Rejoindre existant* est sélectionnée) niveau du service OMS.|Choisissez le niveau tarifaire de la nouvelle instance. Plus d’informations sur https://azure.microsoft.com/pricing/details/log-analytics/ (visible si l’option *Rejoindre existant* est sélectionnée).|Autonome et gratuit par nœud|Gratuit
Clé primaire OMS existante|La clé primaire est utilisée pour se connecter à l’instance OMS existante (visible si l’option *Rejoindre existant* est sélectionnée).

### <a name="deployment-regions"></a>Régions de déploiement

Ensuite, sous **Régions de déploiement**, indiquez des valeurs pour le **nombre de régions** vers lesquelles déployer le réseau de consortium et la sélection des régions Azure en fonction du nombre de régions donné. L’utilisateur peut effectuer le déploiement dans un maximum de cinq régions.

![Régions de déploiement](./media/ethereum-deployment/deployment-regions.png)

Nom du paramètre| Description| Valeurs autorisées |Valeurs par défaut
---|---|---|---
Nombre de régions| Nombre de régions dans lesquelles déployer le réseau de consortium|1, 2, 3, 4, 5| 2
Première région| Première région dans laquelle déployer le réseau de consortium|Toutes les régions sont autorisées.| USA Ouest
Deuxième région |Autre région dans laquelle déployer le réseau de consortium (visible uniquement lorsque le nombre de régions sélectionné est 2).|Toutes les régions sont autorisées.| USA Est
Troisième région| Troisième région dans laquelle déployer le réseau de consortium (visible uniquement lorsque le nombre de régions sélectionné est 3).|Toutes les régions sont autorisées.| USA Centre
Quatrième région| Quatrième région dans laquelle déployer le réseau de consortium (visible uniquement lorsque le nombre de régions sélectionné est 4).|Toutes les régions sont autorisées.| USA Est 2
Cinquième région| Cinquième région dans laquelle déployer le réseau de consortium (visible uniquement lorsque le nombre de régions sélectionné est 5).|Toutes les régions sont autorisées.| USA Ouest 2

### <a name="network-size-and-performance"></a>Taille et performances du réseau

Ensuite, sous **Taille et performances du réseau**, spécifiez des valeurs pour la taille du réseau de consortium, telles que le nombre et taille des nœuds d’exploration de données et des nœuds de transaction.

![Taille et performances du réseau](./media/ethereum-deployment/network-size-performance.png)

Nom du paramètre |Description |Valeurs autorisées| Valeurs par défaut
---|---|---|---
Nombre de nœuds d’exploration de données|Nombre de nœuds d’exploration de données déployés par région|2 - 15| 2
Performances de stockage des nœuds d’exploration de données|Type de disque managé prenant en charge chacun des nœuds d’exploration de données déployés.|Standard ou Premium|standard
Taille de machine virtuelle du nœud d’exploration de données|Taille de machine virtuelle utilisée pour les nœuds d’exploration de données.|Standard A, <br />Standard D, <br />Standard D-v2, <br />Standard F série, <br />Standard DS, <br />et Standard FS|Standard D1v2
Nombre de nœuds de transaction avec équilibrage de charge|Nombre de nœuds de transaction à configurer dans le réseau.|1 - 5| 2
Performances de stockage des nœuds de transaction|Type de disque managé prenant en charge chacun des nœuds de transaction déployés.|Standard ou Premium|standard
Taille de machine virtuelle du nœud de transaction|Taille de machine virtuelle utilisée pour les nœuds de transaction.|Standard A, <br />Standard D, <br />Standard D-v2, <br />Standard F série, <br />Standard DS, <br />et Standard FS|Standard D1v2

### <a name="ethereum-settings"></a>Paramètres Ethereum

Ensuite, sous **Paramètres Ethereum**, spécifiez les paramètres de configuration Ethereum, comme l’ID réseau et le mot de passe du compte Ethereum ou le bloc de genèse.

![Paramètres Ethereum](./media/ethereum-deployment/standalone-leader-deployment.png)

Nom du paramètre |Description |Valeurs autorisées|Valeurs par défaut
---|---|---|---
ID de membre de consortium|ID associé à chaque membre participant au réseau de consortium, qui sert à configurer les espaces d’adressage IP pour éviter les collisions. <br /><br />L’ID de membre doit être unique dans les différentes organisations du même réseau. Un ID de membre unique est nécessaire même si une organisation effectue des déploiements dans plusieurs régions.<br /><br />Notez la valeur de ce paramètre, car vous devrez le partager avec les autres membres participants.|0 - 255
ID de réseau Ethereum|ID du réseau Ethereum de consortium en cours de déploiement. Chaque réseau Ethereum possède son propre ID de réseau, 1 étant l’ID du réseau public. Même si l’accès réseau est limité pour les nœuds d’exploration de données, nous vous recommandons toutefois d’utiliser un nombre élevé pour empêcher les collisions.|5 - 999 999 999| 10101010
Bloc de genèse personnalisé|Option permettant de générer un bloc de genèse automatiquement ou d’en fournir un personnalisé.|Oui/Non| Non 
Mot de passe du compte Ethereum (Bloc de genèse personnalisé = Non)|Mot de passe d’administrateur utilisé pour sécuriser le compte Ethereum importé dans chaque nœud. Le mot de passe doit contenir les éléments suivants : une majuscule, une minuscule et un chiffre.|12 caractères ou plus|N/D
Phrase secrète de clé privée Ethereum (Bloc de genèse personnalisé = Non)|Phrase secrète utilisée pour générer la clé privée ECC associée au compte Ethereum par défaut qui est généré. Une clé privée prégénérée n’a pas besoin d’être explicitement transmise.<br /><br />Envisagez une phrase secrète suffisamment aléatoire afin de garantir une clé privée forte et aucun chevauchement avec d’autres membres du consortium. La phrase secrète doit contenir au moins les éléments suivants : une majuscule, une minuscule et un chiffre.<br /><br />Notez que, si deux membres utilisent la même phrase secrète, les comptes générés doit être identiques. La même phrase secrète est utile si une organisation tente un déploiement dans plusieurs régions et souhaite partager un seul compte (base de jetons) sur tous les nœuds.|12 caractères ou plus|N/D
Bloc de genèse (Bloc de genèse personnalisé = Oui)|Chaîne JSON représentant le bloc de genèse personnalisé. Vous trouverez plus d’informations sur le format du bloc de genèse sous Réseaux personnalisés.<br /><br />Un compte Ethereum est toujours créé lors de la fourniture d’un bloc de genèse personnalisé. Envisagez de spécifier un compte Ethereum prépayé dans le bloc de genèse pour ne pas attendre l’exploration de données.|JSON valide |N/D
Clé partagée pour la connexion|Clé partagée pour la connexion entre les passerelles de réseau virtuel.| 12 caractères ou plus|N/D
URL de données du consortium|URL pointant vers les données de configuration de consortium pertinentes, fournies par le déploiement d’un autre membre. <br /><br />Ces informations sont fournies par un membre déjà connecté qui dispose d’un déploiement. Si vous avez déployé le reste du réseau, l’URL est la sortie de déploiement de modèle nommée CONSORTIUM-DATA.||N/D
Passerelle de réseau virtuel à laquelle se connecter|Chemin d’accès de ressource de la passerelle de réseau virtuel à laquelle se connecter.<br />Ces informations sont fournies par un membre déjà connecté qui dispose d’un déploiement. Si vous avez déployé le reste du réseau, l’URL est la sortie de déploiement de modèle nommée CONSORTIUM_MEMBER_GATEWAY_ID. Remarque : vous devez utiliser l’URL des données de consortium et la ressource de passerelle de réseau virtuel du même membre.||N/D
Point de terminaison du bureau d’enregistrement des informations de pair|Point de terminaison des informations de pair fourni par le déploiement d’un autre membre.|Point de terminaison valide du premier membre du consortium.|N/D
Clé du bureau d’enregistrement des informations de pair|Clé primaire des informations de pair fourni par le déploiement d’un autre membre.|Clé primaire valide du premier membre du consortium.|N/D

### <a name="summary"></a>Résumé

Cliquez sur le panneau de résumé pour passer en revue les entrées spécifiées et pour exécuter une validation de base avant le déploiement.

![Résumé](./media/ethereum-deployment/summary.png)

Passez en revue les conditions juridiques et de confidentialité, puis cliquez sur **Achat** pour lancer le déploiement. Si le déploiement concerne plusieurs régions ou un réseau de consortium, ce modèle pré-déploie les passerelles VPN nécessaires pour prendre en charge la connectivité réseau avec d’autres membres. Le déploiement de la passerelle peut prendre jusqu’à 45 ou 50 minutes.

## <a name="post-deployment-sanity-checks"></a>Contrôles de validité post-déploiement

### <a name="administrator-page"></a>Page de l’administrateur

Une fois que le déploiement est terminé et que toutes les ressources ont été configurées, vous pouvez accéder à la page de l’administrateur pour obtenir une vue de votre réseau blockchain et vérifier l’état du déploiement. L’URL de la page d’administration est le nom DNS de l’équilibreur de charge ; Il est présent dans la section de sortie du déploiement de modèle nommée ADMIN_SITE.

Pour la rechercher, sélectionnez le groupe de ressources qui a été déployé. Ensuite, sélectionnez **Vue d’ensemble**, puis cliquez sur le lien situé immédiatement sous **Déploiements** indiquant le nombre de réussites.

![Vue d’ensemble du groupe de ressources](./media/ethereum-deployment/resource-overview.png)

Le nouvel écran affiche l’historique de déploiement. Sélectionnez la première ressource de déploiement (par exemple, microsoft-azure-blockchain.azure-blockchain-servi...) et recherchez la section **Sorties** dans la moitié inférieure de la page. Vous verrez l’URL de la page d’administration répertoriée dans le paramètre de sortie de déploiement de modèle en tant que ADMIN_SITE.

![Déploiements de ressources](./media/ethereum-deployment/resource-deployments.png)

![Sortie du déploiement](./media/ethereum-deployment/deployment-output.png)

Pour accéder à la page d’administration, copiez la sortie **ADMIN-SITE** et ouvrez-la dans un autre onglet.

Sur la page d’administration, vous pouvez obtenir une vue d’ensemble de la topologie déployée en consultant la section sur l’état du nœud Ethereum. Elle inclut tous les noms d’hôte de nœud, le nombre de pairs et le dernier bloc vu. Le nombre de pairs pour chaque nœud est compris entre le minimum d’un de moins que le *nombre total de nœuds* et le nombre maximal de pairs configuré. Notez que le nombre de pairs ne limite pas le nombre de nœuds qui peuvent être déployés au sein du réseau.
Parfois, vous verrez que le nombre de pairs fluctue et est inférieure à la formule (nombre total de nœuds - 1). La différence n’est pas toujours signe que les nœuds sont défectueux, étant donné que les duplications (forks) du registre peuvent entraîner des modifications mineures dans le nombre de pairs. Enfin, vous pouvez inspecter le dernier bloc vu par chaque nœud du réseau afin de déterminer les duplications (forks) ou les décalages dans le système.

![État du nœud](./media/ethereum-deployment/node-status.png)

L’état du nœud est actualisé toutes les 10 secondes. Rechargez la page via le navigateur ou via le bouton **Recharger** pour mettre à jour la vue.

### <a name="oms-portal"></a>Portail OMS

Vous pouvez localiser votre portail OMS en suivant le lien dans la sortie de déploiement (OMSPORTALURL) ou en sélectionnant la ressource OMS dans votre groupe de ressources déployé.

![URL OMS](./media/ethereum-deployment/oms-url.png)

![Ressource OMS](./media/ethereum-deployment/oms-resource.png)

Le portail affiche tout d’abord les statistiques réseau de haut niveau comme une vue d’ensemble.

![Vue d’ensemble OMS](./media/ethereum-deployment/oms-overview.png)

Cliquez sur la vue d’ensemble pour accéder à un portail affichant des statistiques par nœud.

![Statistiques par nœud](./media/ethereum-deployment/per-node-stats.png)

### <a name="accessing-nodes"></a>Accès aux nœuds

Vous pouvez vous connecter à distance aux machines virtuelles des nœuds de transaction via SSH avec le nom d’utilisateur administrateur et le mot de passe/clé SSH fournis. Étant donné que les machines virtuelles de nœud de transaction n’ont pas leurs propres adresses IP publiques, vous devez y accéder via l’équilibreur de charge et spécifier le numéro de port. La commande SSH à exécuter pour accéder au premier nœud de transaction est répertoriée dans le paramètre de sortie de déploiement de modèle, **SSH_TO_FIRST_TX_NODE** (pour l’exemple de déploiement : ssh -p 4000 gethadmin@leader4vb.eastus.cloudapp.azure.com). Pour obtenir des nœuds de transaction supplémentaires, incrémentez le numéro de port d’une unité (par exemple, le premier nœud de transaction est sur le port 4000).

Étant donné que les machines virtuelles sur lesquelles sont exécutés les nœuds d’exploration de données ne sont pas accessibles depuis l’extérieur, vous devez y accéder via un des nœuds de transaction. Une fois que vous disposez d’une session SSH pour un nœud de transaction, installez votre clé privée sur le nœud de transaction ou utilisez votre mot de passe pour démarrer une session SSH dans un nœud d’exploration de données.

**Remarque :**

Les noms d’hôtes peuvent être obtenus à partir du site d’administration ou à partir du portail Azure. Dans le portail Azure, les noms d’hôtes de nœuds présents dans le groupe de machines virtuelles identiques sont répertoriés sous **Instances**, ce qui diffère des noms d’hôtes réels. Par exemple, le nom d’hôte dans le portail Azure peut ressembler à **mn-asdfmv-reg1_0**, mais le nom d’hôte réel serait **mn-asdfmv-reg**.

Par exemple : 

Nom d’hôte du portail Azure| Nom d’hôte réel
---|---
mn-ethwvu-reg1_0| mn-ethwvu-reg1000000
mn-ethwvu-reg1_1 |mn-ethwvu-reg1000001
mn-ethwvu-reg1_2 |mn-ethwvu-reg1000002

## <a name="adding-a-new-consortium-member"></a>Ajout d’un nouveau membre de consortium

### <a name="sharing-data"></a>Partage de données

En tant que premier membre (ou membre connecté) du consortium, vous devez fournir à d’autres membres quelques éléments d’information qui leur permettent de participer et d’établir leur connexion. Plus précisément :

1. **Données de configuration de consortium partagées** : il existe un ensemble de données qui sont utilisées pour orchestrer la connexion Ethereum entre deux membres. Les informations nécessaires, y compris le bloc de genèse, l’ID de réseau de consortium et les nœuds de démarrage, sont écrits dans un fichier sur les nœuds de transaction du leader ou d’un autre membre déployé. L’emplacement de ce fichier est répertorié dans le paramètre de sortie de déploiement de modèle nommé **CONSORTIUM-DATA**.
2. **Point de terminaison d’informations de pairs**  point de terminaison du bureau d’enregistrement des informations de pairs permettant d’obtenir des informations sur tous les nœuds déjà connectés au réseau Ethereum à partir de leaders ou du déploiement d’un autre membre. Les magasins de base de données constituent un ensemble d’informations sur chaque nœud connecté du réseau, comme le nom d’hôte du nœud, l’adresse IP privée, etc. Il s’agit du paramètre de sortie de déploiement de modèle nommé **PEER_INFO_ENDPOINT**.
3. **Clé primaire d’informations de pairs** : clé primaire du bureau d’enregistrement des informations de pairs permettant d’accéder à la clé primaire des informations de pairs du leader ou d’un autre membre. Il s’agit du paramètre de sortie de déploiement de modèle nommé **PEER_INFO_PRIMARY_KEY**.


4. **Passerelle de réseau virtuel** : chaque membre établit une connexion au réseau blockchain tout entier via un membre existant. Pour vous connecter un réseau virtuel, vous avez besoin du chemin d’accès de ressource à la passerelle de réseau virtuel du membre auquel vous vous connectez. Il s’agit du paramètre de sortie de déploiement de modèle nommé **CONSORTIUM_MEMBER_GATEWAY_ID**.
5. **Clé partagée** : clé secrète préétablie entre deux membres du réseau de consortium qui établissent une connexion. Il s’agit d’une chaîne alphanumérique (entre 1 et 128 caractères) convenue en dehors du contexte du déploiement. (Par exemple, **MySharedKeyAbc123**)

### <a name="acceptance-of-new-member"></a>Acceptation d’un nouveau membre

Cette étape doit être effectuée après que le nouveau membre a déployé son réseau. Pour qu’un membre puisse rejoindre le réseau et voir le trafic de transaction, un membre existant doit effectuer une configuration finale sur la passerelle VPN pour accepter la connexion. Cela signifie que les nœuds Ethereum du nouveau membre ne fonctionneront qu’une fois la connexion établie. Cette configuration peut être effectuée via PowerShell ou l’interface de ligne de commande XPlat. Un module PowerShell ou un script d’interface de ligne de commande xPlat sont également stockés sur le nœud de transaction, en même temps que les données du consortium. L’emplacement du script correspond aux paramètres de sortie de déploiement nommés **PAIR-GATEWAY-PS-MODULE** et **PAIR-GATEWAY-AZURE-CLISCRIPT**, respectivement.

**Configuration de PowerShell/de l’interface CLI**

Pour plus d’informations sur la prise en main des cmdlets Azure PowerShell et de l’interface de ligne de commande Azure xPlat, consultez la documentation sur Azure.

Vous avez besoin de la dernière version des cmdlets Azure installées localement et d’une session ouverte. Veillez à vous connecter à la session avec les informations d’identification de votre abonnement Azure.

**PowerShell : établir la connexion**

Téléchargez le module PowerShell et stockez-le localement. L’emplacement du module PowerShell est spécifié en tant que paramètre de sortie de déploiement de modèle **PAIR-GATEWAY-PS-MODULE**.

S’il n’est pas encore activé, utilisez la cmdlet **Set-ExecutionPolicy** pour que la session locale permette l’exécution d’un module non signé.

**Set-ExecutionPolicy Unrestricted CurrentUser**

Ensuite, connectez-vous à l’abonnement Azure dans lequel vous avez effectué le déploiement du leader à l’aide de

**Login-AzureRmAccount**

Ensuite, importez le module :

**Import-Module <filepath to downloaded file>**

Enfin, exécutez la fonction avec l’entrée appropriée :

- **MyGatewayResourceId** : chemin d’accès de ressource de votre passerelle. Il s’agit du paramètre de sortie de déploiement de modèle nommé **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId** : chemin d’accès de ressource de la passerelle du nouveau membre. Il est fourni par le nouveau membre et il s’agit du paramètre de sortie de déploiement de modèle également nommé **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName** : nom pour identifier cette connexion à la passerelle.
- **Clé partagée** : clé secrète préétablie entre deux membres du réseau de consortium qui établissent une connexion.

**CreateConnection** - MyGatewayResourceId <resource path of your Gateway> -OtherGatewayResourceId <chemin d’accès de ressource de la passerelle du nouveau membre> -ConnectionName myConnection -SharedKey "MySharedKeyAbc123"

**Interface de ligne de commande xPlat : établir la connexion**

Téléchargez le script d’interface CLI Azure et stockez-le localement. L’emplacement du script d’interface CLI Azure est spécifié dans le paramètre de déploiement de modèle nommé **PAIR-GATEWAY-AZURE-CLI-SCRIPT**.

Exécutez le script avec l’entrée appropriée :

- **MyGatewayResourceId** : chemin d’accès de ressource de votre passerelle. Il s’agit du paramètre de sortie de déploiement de modèle nommé **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **OtherGatewayResourceId** : chemin d’accès de ressource de la passerelle du nouveau membre. Il est fourni par le nouveau membre et il s’agit du paramètre de déploiement de modèle également nommé **CONSORTIUM_MEMBER_GATEWAY_ID**.
- **ConnectionName** : nom pour identifier cette connexion à la passerelle.
- **Clé partagée** : clé secrète préétablie entre deux membres du réseau de consortium qui établissent une connexion.
- **Emplacement** : région Azure où la ressource de passerelle est déployée.

``` powershell
az network vpn-connection create --name $ConnectionName --resource-group
$MyResourceGroup --vnet-gateway1 $MyGatewayResourceId --shared-key $SharedKey --vnet-
gateway2 $OtherGatewayResourceId --enable-bgp
```

L’architecture ressemblera à celle ci-dessous une fois que vous aurez correctement configuré la connexion entre les déploiements du **leader** et du **membre**.

![Architecture leader/membre](./media/ethereum-deployment/leader-member.png)

## <a name="fund-new-member-account"></a>Financer un nouveau compte de membre

### <a name="generated-genesis-block"></a>Bloc de genèse généré

En tant que premier membre, votre compte Ethereum est financé avec un milliard d’Ethers si le déploiement génère le bloc de genèse (Bloc de genèse personnalisé = Non). Les autres membres disposeront d’un compte non pré-payé et devront attendre d’accumuler des Ethers lorsque leurs nœuds d’exploration de données commenceront à explorer des blocs. Pour éviter que les nouveaux membres doivent attendre des Ethers, vous devez explicitement financer les comptes Ethereum des nouveaux membres.

Pour ce faire, les nouveaux membres doivent vous indiquer le compte Ethereum qui s’affiche sur leur site web d’administration. Vous pouvez ensuite utiliser votre site web d’administration pour transférer des Ethers de votre compte vers le leur en entrant le compte indiqué.

### <a name="custom-genesis-block"></a>Bloc de genèse personnalisé

Si un bloc de genèse personnalisé est fourni avec un compte Ethereum spécifié, vous pouvez utiliser MetaMask ou un autre outil pour transférer des Ethers de ce compte vers le compte Ethereum pré-généré visible sur le site web d’administration. Pour obtenir des instructions sur l’utilisation de MetaMask, consultez la rubrique [Création d’un compte Ethereum](#create-ethereum-account).

Si un bloc de genèse personnalisé est fourni sans compte ou si vous n’avez pas accès aux comptes pré-alloués, vous devez attendre que vos nœuds d’exploration de données commencent leur action afin de générer des Ethers dans votre compte (base de jetons). La vitesse à laquelle les fonds sont générés dépend du niveau de difficulté que vous spécifiez dans le bloc de genèse personnalisé.

## <a name="create-ethereum-account"></a>Créer le compte Ethereum

Pour créer un compte supplémentaire, vous pouvez utiliser un large éventail de solutions. Une de ces solutions est MetaMask, une extension Chrome qui fournit un coffre-fort d’identités et une connexion à un réseau Ethereum (public, de test ou personnalisé). MetaMask formule une transaction pour inscrire le compte dans le réseau.

Cette transaction, comme toutes les autres, accède à un des nœuds de transaction et finit par être explorée dans un bloc, comme illustré ci-dessous.

![Nœud de transaction](./media/ethereum-deployment/transaction-node.png)

Pour installer l’extension dans Chrome, accédez à Personnaliser et contrôler Google Chrome (bouton de dépassement) > Plus d’outils > Extensions > Obtenir plus d’extensions et recherchez MetaMask.

![Extension MetaMask](./media/ethereum-deployment/metamask-extension.png)

Une fois l’extension installée, ouvrez MetaMask et créez un coffre. Par défaut, le coffre est connecté au réseau de test Morden. Vous devez modifier cela pour vous connecter au réseau de consortium privé déployé, en particulier à l’équilibreur de charge devant les nœuds de transaction. À partir de la sortie de modèle, récupérez le point de terminaison RPC Ethereum exposé sur le port 8545, nommé `ETHEREUM-RPC-ENDPOINT`, et entrez-le dans le RPC personnalisé comme indiqué ci-dessous.

![Paramètres MetaMask](./media/ethereum-deployment/metamask-settings.png)

En créant le coffre, vous créez un portefeuille contenant un compte. Pour créer des comptes supplémentaires, sélectionnez **Changer de compte**, puis cliquez sur le bouton **+**.

![Création de compte MetaMask](./media/ethereum-deployment/metamask-create-account.png)

### <a name="initiate-initial-ether-allocation"></a>Lancer l’allocation initiale d’Ethers

Via la page de l’administrateur, vous pouvez formuler une transaction pour transférer des Ethers du compte pré-alloué à un autre compte Ethereum. Ce transfert est une transaction transmise au nœud de transaction et explorée dans un bloc, comme illustré ci-dessous.

![Nœud de transaction](./media/ethereum-deployment/transaction-node-mined.png)

Via l’icône du Presse-papiers dans le portefeuille MetaMask, copiez l’adresse du compte Ethereum auquel vous souhaitez transférer des Ethers et revenez à la page de l’administrateur. Collez le compte copié dans le champ d’entrée pour transférer 1 000 Ethers du compte Ethereum pré-alloué vers votre nouveau compte. Cliquez sur **Envoyer** et attendez que la transaction soit explorée dans un bloc.

![État du nœud](./media/ethereum-deployment/node-status2.png)

Une fois que la transaction est validée dans un bloc exploré, le solde du compte dans MetaMask reflète le transfert de 1 000 Ethers.

![Transfert MetaMask](./media/ethereum-deployment/metamask-transfer.png)

### <a name="transfer-of-ether-between-accounts"></a>Transfert d’Ethers entre des comptes

À ce stade, vous êtes prêt à exécuter des transactions au sein de votre réseau de consortium privé. La transaction la plus simple consiste à transférer des Ethers d’un compte à un autre. Pour formuler une telle transaction, vous pouvez encore utiliser MetaMask, en transférant de l’argent du premier compte utilisé ci-dessus vers un deuxième compte.

À partir du **portefeuille 1** dans MetaMask, cliquez sur Envoyer. Copiez l’adresse du deuxième portefeuille créé dans le champ d’entrée **Adresse du destinataire** et la quantité d’Ethers à transférer dans le champ d’entrée **Quantité**. Cliquez sur **Envoyer** et acceptez la transaction.

![Envoi de transaction MetaMask](./media/ethereum-deployment/metamask-send.png)

Une fois encore, lorsque la transaction est explorée et validée dans un bloc, les soldes des comptes sont modifiés en conséquence. Notez que le solde du **portefeuille 1** est débité de plus de 15 Ethers, étant donné que vous deviez payer des frais d’exploration de données pour traiter la transaction.

![Portefeuille 1](./media/ethereum-deployment/wallet1.png)

![Portefeuille 2](./media/ethereum-deployment/wallet2.png)

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez désormais vous concentrer sur le développement d’applications et de contrats dynamiques sur votre réseau blockchain de consortium privé.
