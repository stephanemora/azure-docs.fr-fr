---
title: FAQ - Solution VMware par CloudSimple
description: Forum aux questions sur Azure VMware Solution par CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e727398e1b7bfa406166574ab40320c68dac5709
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358527"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Forum aux questions sur la VMware Solution par CloudSimple

Forum aux questions et réponses sur la VMware Solution par CloudSimple qui vous aident à comprennent le service et comment l’utiliser.  Les questions et réponses sont organisées dans les catégories suivantes.

* Service de CloudSimple
* Connectivité
* Mise en réseau
* Sécurité
* Calcul
* Stockage
* VMware
* Intégration d’Azure
  
## <a name="cloudsimple-service"></a>Service de CloudSimple

**Qu’est la VMware Solution par CloudSimple ?**

**Solution de VMware par CloudSimple** transforme et étend les charges de travail VMware vers des clouds privés, dédiés sur Azure en quelques minutes. Nous nous occupons du provisionnement, gestion de l’infrastructure et orchestrer des charges de travail entre en local et Azure. Étant donné que vos applications s’exécutent exactement le même en local et dans Azure, vous bénéficiez de l’élasticité et les services du cloud sans la complexité du remaniement de vos applications. CloudSimple réduit le coût total de possession avec un modèle de consommation cloud qui fournit à la demande approvisionnement, payer en tant que les extensions et optimisation de la capacité.  Consultez [What ' s VMware Solution sur Azure par CloudSimple](cloudsimple-vmware-solutions-overview.md) pour les fonctionnalités, avantages et scénarios.

**Qu’est un Cloud privé de CloudSimple ?**

Vous configurez un cloud privé et dédié composé de calcul haute performance, stockage et l’environnement déployé sur l’infrastructure Microsoft Azure (matériel et centre de données de l’espace) de mise en réseau dans les emplacements Azure.  Cloud privé fournit une plateforme de VMware native 'as-a-service'. En termes de VMware, chaque Cloud privé contient exactement une instance du serveur vCenter. Le serveur vCenter Server gère plusieurs nœuds ESXi contenus dans un ou plusieurs Clusters vSphere, ainsi que le stockage réseau SAN virtuel (vSAN) correspondant. Un Service de CloudSimple peut contenir plusieurs clouds privés dans votre abonnement Azure.  Pour plus d’informations sur les clouds privés, consultez [vue d’ensemble du Cloud privé](cloudsimple-private-cloud.md).

**Où les CloudSimple service est-il disponible ?**

CloudSimple est disponible dans les régions est et ouest des États-Unis.

**Comment activer mon abonnement CloudSimple ?**

Vous pouvez contacter votre responsable de compte Microsoft à [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) pour activer votre abonnement pour le service de CloudSimple. Fournissez votre ID d’abonnement dans le courrier électronique pour lequel vous souhaitez service CloudSimple est activé.  

**Comment accéder au portail CloudSimple ?**

Vous accéder CloudSimple portail à partir du portail Azure.  Consultez [l’accès à des solutions de VMware par portail CloudSimple à partir du portail Azure](https://docs.azure.cloudsimple.com/access-cloudsimple-portal) article pour plus d’informations sur l’accès au portail de CloudSimple. 

**Comment accroître la capacité d’un nuage privé ?**

Vous achetez des nœuds à partir du portail Azure et que vous développez votre Cloud privé à partir du portail CloudSimple.  Vous pouvez développer votre Cloud privé en ajoutant des nœuds supplémentaires au cluster vSphere existant ou en créant le nouveau cluster vSphere.  Consultez [développez un Cloud privé de CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud) article pour connaître la procédure.

**Qu’advient-il de mes Cloud privé lors de la maintenance ?**

CloudSimple fournit des notifications périodiques de jours avant la maintenance planifiée.  Maintenance est effectuée de façon à garantir la disponibilité de votre nuage privé sans interruption de service.  Maintenance peut être des types suivants :

1. **Infrastructure de CloudSimple :**  Le CloudSimple Infrastructure est conçue pour être hautement disponible.  Pendant la maintenance, la connectivité et la disponibilité de votre nuage privé est assurée en mettant à jour des composants redondants celui à la fois sans aucun impact.  Vous aurez accès à votre vCenter du Cloud privé, toutes les machines virtuelles, connexion internet à partir de votre Cloud privé et connexions à local ou sur Azure.
2. **CloudSimple portail :** Lors de la maintenance, certaines fonctionnalités sur le portail CloudSimple peut-être pas accessible ou désactivé.  Notification de maintenance inclut les détails de ce qui peut être fait sur le portail.

## <a name="connectivity"></a>Connectivité

**Quelles sont mes options de connectivité au réseau de région CloudSimple ?**

CloudSimple offre trois options de connectivité différents pour vous connecter à votre réseau de la région CloudSimple.  Les trois peuvent être utilisés ensemble.

1. Connexion ExpressRoute à partir de votre centre de données local pour CloudSimple région réseau - connexion privée sécurisées à haut débit à faible latence pontage votre circuit ExpressRoute d’en local avec votre circuit CloudSimple ExpressRoute à l’aide de la portée mondiale. Consultez [se connecter en local à CloudSimple à l’aide d’ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection) article pour configurer la connexion.
2. Connexion à partir de votre réseau virtuel Azure ExpressRoute à votre réseau de région CloudSimple - connexion privée sécurisées à haut débit à faible latence pontage de votre réseau virtuel sur Azure avec votre circuit CloudSimple ExpressRoute à l’aide de passerelles de réseau virtuel.  Consultez [connecter votre environnement de Cloud privé de CloudSimple au réseau virtuel Azure à l’aide d’ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection) article pour configurer la connexion.
3. Connexion VPN de site à Site à partir de votre centre de données local à votre réseau de région CloudSimple - sécuriser le réseau privé virtuel à partir de votre périphérique VPN sur site et votre région de Cloud privé de CloudSimple.  Consultez [configuration de la connexion VPN entre votre réseau local et le Cloud privé CloudSimple] l’article pour configurer la connexion VPN.

**Comment se connecter à un Cloud privé ?**

Vous pouvez afficher les détails de votre nuage privé dans le portail CloudSimple. Pour vous connecter au serveur vCenter correspondant à votre Cloud privé, vérifiez la connexion réseau est établie à l’aide de Site à Site, Point-to-Site ou ExpressRoute. Ensuite, ouvrir le portail de CloudSimple dans portail Azure, cliquez sur *lancer le Client vSphere* sur la page d’accueil ou dans la page de détails du Cloud privé.

**Quel est l’avantage de circuit ExpressRoute ?**

Circuit Azure ExpressRoute fournit une connexion sécurisée à haut débit à faible latence.  CloudSimple fournit un ExpressRoute Circuit dédié par région et par client.  À l’aide de ce circuit, vous pouvez établir une connexion sécurisée à partir de locaux et/ou de votre abonnement Azure.

**Quelles sont les coûts de réseau pour se connecter à CloudSimple. Les frais d’acheminement vers/depuis CloudSimple vers Azure ? Régions ?**

Aucun frais n’est toute sortie du réseau.  Les tarifs Azure standards s’appliquent à tout le trafic sortant à partir de votre réseau virtuel ou de circuit ExpressRoute de local.

## <a name="networking"></a>Mise en réseau

**Quelles fonctionnalités de mise en réseau sont disponibles pour mon Cloud privé ?**

Vous pouvez configurer des réseaux locaux virtuels (et leurs sous-réseaux), tables, de pare-feu et attribuer des adresses IP publiques et mapper à une machine virtuelle en cours d’exécution dans votre Cloud privé.  Pour plus d’informations, consultez [vue d’ensemble des réseaux locaux virtuels/sous-réseaux](cloudsimple-vlans-subnets.md), [vue d’ensemble de Tables de pare-feu](cloudsimple-firewall-tables.md), et [présentation de l’adresse IP publique](cloudsimple-public-ip-address.md) articles.

**Comment configurer des sous-réseaux différents pour mes applications dans mon Cloud privé ?**

Vous pouvez créer des réseaux locaux virtuels sur votre Cloud privé à partir de votre portail CloudSimple.  Une fois que vous créez le réseau local virtuel, vous pouvez créer un groupe de ports distribué sur votre vCenter de Cloud privé à l’aide du réseau local virtuel et créer des machines virtuelles connectées au groupe de ports distribué.  Vous pouvez activer la table de pare-feu pour le réseau local virtuel/sous-réseau et définir des règles de pare-feu pour sécuriser le trafic réseau.

**Quels paramètres de pare-feu sont disponibles pour vos Clouds privés ?**

Vous pouvez configurer des règles pour le trafic nord-sud et est-ouest.  Les règles sont définies dans une table de pare-feu.  Le tableau de pare-feu peut être jointe au VLAN sur votre Cloud privé.  Consultez [définir des tables de pare-feu et des règles pour les Clouds privés](https://docs.azure.cloudsimple.com/firewall) article pour la procédure d’installation.

**Puis-je attribuer des adresses IP publiques pour les machines virtuelles dans mon environnement de Cloud privé ?**

Dans le portail CloudSimple, vous pouvez facilement allouer une nouvelle adresse IP publique et l’associer à une adresse IP privée de votre machine virtuelle ou une appliance.  Vous pouvez également créer des règles de pare-feu ou appliquer des règles de pare-feu existantes pour l’autoriser le trafic provenant des ports spécifiques et/ou spécifique d’adresses IP dans le portail. Consultez [allouer des adresses IP publiques pour un environnement de Cloud privé](https://docs.azure.cloudsimple.com/public-ips) pour la procédure d’installation.

## <a name="security"></a>Sécurité

**Quelles sont mes options de sécurité sur CloudSimple ?**

Cloud privé de CloudSimple fournit les fonctionnalités de sécurité suivantes pour sécuriser votre environnement de Cloud privé :

1. **Données de chiffrement au repos**: Vous pouvez chiffrer les données au repos résidant sur le stockage réseau vSAN dans votre Cloud privé. vSAN prend en charge le serveur de gestion de clés externes, ce qui peut être déployé dans votre environnement local ou de réseau virtuel Azure.  Consultez [configurer le chiffrement réseau vSAN pour votre Cloud privé de CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption) pour plus d’informations.
2. **Sécurité réseau**: Contrôle réseau trafic depuis/vers votre Cloud privé à partir d’Internet, en local et au sein des sous-réseaux de votre nuage privé à l’aide de règles de pare-feu.
3. **Sécuriser, connexion privée**: Sécuriser une connexion privée entre votre réseau local et votre abonnement Azure.

## <a name="compute"></a>Calcul

**Quelles sont les hôtes sont disponibles ?**

CloudSimple propose deux types d’hôtes :

* **Nœud de CS28 :** CPU:2 x 2,2 GHz, nombre Total de 28 cœurs, 48 HT.  RAM : 256 GO.  Stockage : Cache de 1600 Go NVMe, données 5760 Go (exclusivement Flash). Réseau : carte réseau 2x25Gbe
* **Nœud de CS36 :** Processeur 2 x 2.3 GHz, Total 36 cœurs, 72 HT.  RAM : 512 GO.  Stockage : 3 200 Go NVMe Cache 11 520 Go de données (exclusivement Flash).  Réseau : carte réseau 2x25Gbe

**Comment sont gérées les défaillances matérielles ?**

Toute l’infrastructure CloudSimple est surveillé en continu par la plateforme CloudSimple et nos équipes d’opérations de service.  Si une défaillance matérielle est détectée, un nouveau nœud est ajouté à votre Cloud privé et le nœud défaillant est supprimé de garantir la haute disponibilité de votre nuage privé.

## <a name="storage"></a>Stockage

**Quel type de stockage est pris en charge sur un Cloud privé ?**

Offre CloudSimple **exclusivement flash VMware vSAN stockage** avec chaque Cloud privé.  Chaque vSphere est créé avec son propre magasin de données vSAN.  Consultez [composants VMware de Cloud privé - vSAN stockage](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage) article pour plus d’informations.

**Est le chiffrement de données pris en charge ?**
Oui.  Vous pouvez configurer le stockage vSAN sur votre Cloud privé à utiliser un serveur de gestion de clés (KMS) qui est déployé en local ou sur Azure pour le chiffrement des données stockées sur le réseau vSAN

**Comment sont gérées les disques défaillants ?**

Surveillance de CloudSimple surveille en permanence de tous les composants matériels du Cloud privé.  Si une panne de disque est détectée ou n’importe quel disque est identifié comme défaillant (basé sur l’heuristique), un nouveau nœud est automatiquement ajouté dans le Cloud privé.  Le nœud avec le disque défaillant ou est supprimé à partir du Cloud privé.

## <a name="vmware"></a>VMware

**Comment procéder à grande échelle chargement/migrer des applications et des données en local ?**

CloudSimple fournit des solutions de natives VMware vSphere.  N’importe quel outil utilisé pour la migration de données en bloc peut être utilisé avec le Cloud privé de CloudSimple.  Certaines des options disponibles sont :

1. HCX VMware pour la migration en bloc de données.
2. À froid la migration des données à l’aide de Storage vMotion sur site à CloudSimple.

**Puis-je installer les outils VMware ?**

CloudSimple fournit des solutions de natives VMware vSphere.  N’importe quel outil permettant de gérer des vSphere environnement local peut être utilisé sur CloudSimple.  CloudSimple prend en charge le modèle Bring-Your-propre licence (BYOL) pour l’installation des outils VMware.

**Comment sont gérées les mises à jour et mises à niveau ?**

CloudSimple gère et met à jour tous les composants d’infrastructure de votre nuage privé de façon transparente sans interruption de service.  N’importe quel correctif de mise à jour ou de sécurité publié par les fournisseurs de VMware ou de l’infrastructure est planifié pour la mise à jour dès qu’elle est qualifiée par CloudSimple.

CloudSimple n’effectue pas de mises à niveau ou des mises à jour des applications installées sur le Cloud privé.

## <a name="azure-integration"></a>Intégration d’Azure

**Quels services Azure sont pris en charge ?**

CloudSimple fournit une connexion Azure ExpressRoute à votre abonnement sur Azure.  Tous les services en cours d’exécution dans votre abonnement a une connectivité réseau dans votre Cloud privé et peut se connecter à votre Cloud privé.  Exemples :

1. **Azure Active Directory** comme source d’identité pour votre vCenter CloudSimple
2. **Stockage Azure** pour le stockage des sauvegardes, les images et les autres données à partir de votre Cloud privé
3. **Applications hybrides** -vous pouvez créer d’architecture d’application qui s’étend sur des clouds publics et privés.  Par exemple, vous pouvez créer des serveurs Web dans Azure qui accèdent aux serveurs d’applications et de base de données sur le Cloud privé de CloudSimple.
4. **Azure monitor** et **centre de sécurité Azure** -charge de travail exécutées sur VMware permettre utiliser ces valeurs pour la journalisation, les mesures de performances et la gestion de la sécurité.

**Comment mapper mes clients VMware vers Azure ?**

CloudSimple fournit le seul à pouvoir gérer vos machines virtuelles VMware sur le Cloud privé à partir du portail Azure.  Un pool de ressources de vCenter (configuré avec les contraintes de la ressource souhaitée) peut être mappé à votre abonnement par l’administrateur général.  

**Les avantages des licences obtenir auprès d’Azure ?**

Avec CloudSimple, vous pouvez tirer parti de l’utilisation de l’offre Azure Hybrid Benefit et économisez jusqu'à 90 % sur les licences en conservant votre investissement dans Licenses de Microsoft, en réduisant votre coût total de possession par rapport aux autres clouds. En outre, vous obtenez étendu mises à jour de sécurité pour Windows Server 2008 et Microsoft SQL Server 2008.  Limiter les coûts avec vos propres licences BYOL (apportez) vers le cloud pour les applications courantes telles que Veeam, Zerto et d’autres utilisateurs.  