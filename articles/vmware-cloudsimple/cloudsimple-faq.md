---
title: Questions fréquentes (FAQ) - solution VMware de CloudSimple
description: Questions fréquentes sur la solution Azure VMware de CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80380a1c33927029e000e59a5834f297340f5be3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816229"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Questions fréquentes sur la solution VMware de CloudSimple

Passez en revue les questions fréquentes sur la solution Azure VMware de CloudSimple et les réponses données pour comprendre le service et savoir comment l’utiliser. Les questions/réponses sont organisées en plusieurs catégories :

* Service CloudSimple
* Connectivité
* Mise en réseau
* Sécurité
* Calcul
* Stockage
* VMware
* Intégration à Azure
 
## <a name="cloudsimple-service"></a>Service CloudSimple

**En quoi consiste la solution Azure VMware de CloudSimple ?**

La solution Azure VMware de CloudSimple transforme et étend les charges de travail VMware à des clouds privés et dédiés sur Azure en quelques minutes. La solution provisionne, gère l’infrastructure et orchestre les charges de travail entre le niveau local et Azure. Vos applications s’exécutant exactement de la même façon au niveau local et dans Azure, vous bénéficiez de l’élasticité et des services du cloud sans qu’il soit nécessaire de procéder à une refonte complexe de vos applications. CloudSimple réduit votre coût total de possession avec un modèle de consommation cloud qui fournit des caractéristiques telles que le provisionnement à la demande, le paiement en fonction de l’évolution et l’optimisation de la capacité. Pour examiner les fonctionnalités, avantages et scénarios, consultez [En quoi consiste la solution Azure VMware de CloudSimple ?](cloudsimple-vmware-solutions-overview.md).

**Qu’est-ce qu’un cloud privé CloudSimple ?**

Vous provisionnez un cloud privé et dédié qui se compose d’un environnement hautes performances (calcul, stockage et réseau) déployé sur l’infrastructure Microsoft Azure (espace de centre de données et matériel) dans des emplacements Azure. Le cloud privé fournit une plateforme VMware native en tant que service. Pour VMware, chaque cloud privé contient exactement une instance du serveur vCenter Server. Ce dernier gère plusieurs nœuds ESXi contenus dans un ou plusieurs clusters vSphere, ainsi que le stockage vSAN correspondant. Un service CloudSimple peut contenir plusieurs clouds privés dans votre abonnement Azure. Pour plus d’informations sur les clouds privés, consultez [Vue d’ensemble du cloud privé](cloudsimple-private-cloud.md).

**Où le service CloudSimple est-il disponible ?**

CloudSimple est disponible dans les régions USA Est et USA Ouest.

**Comment activer mon abonnement à CloudSimple ?**

Contactez le responsable de votre compte Microsoft à l’adresse [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) pour activer votre abonnement au service CloudSimple. Indiquez dans votre e-mail l’ID d’abonnement pour lequel vous souhaitez activer le service CloudSimple. 

**Comment accéder au portail CloudSimple ?**

L’accès au portail CloudSimple s’effectue à partir du portail Azure. Pour plus d’informations sur la façon d’accéder au portail CloudSimple, consultez [Accéder au portail de al solution VMware de CloudSimple à partir du portail Azure](https://docs.azure.cloudsimple.com/access-cloudsimple-portal).

**Comment accroître la capacité d’un cloud privé ?**

Vous provisionnez des nœuds à partir du portail Azure et développez votre cloud privé à partir du portail CloudSimple. Vous pouvez développer votre cloud privé en ajoutant des nœuds à un cluster vSphere existant ou en créant un cluster vSphere. Pour plus d’informations sur la procédure, consultez [Développer un cloud privé CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud).

**Qu’advient-il de mon cloud privé durant la maintenance ?**

CloudSimple fournit régulièrement des notifications plusieurs jours avant la maintenance planifiée. La maintenance est effectuée de façon à garantir la disponibilité de votre cloud privé sans interrompre le service. La maintenance se présente sous plusieurs formes :

- **Infrastructure CloudSimple** : L’infrastructure CloudSimple est conçue pour être hautement disponible. Durant la maintenance, les composants redondants sont mis à jour un à un, ce qui élimine tout impact et garantit la connectivité et la disponibilité de votre cloud privé. Vous avez accès au serveur vCenter de votre cloud privé, à toutes les machines virtuelles, à la connexion internet à partir de votre cloud privé et aux connexions au réseau local ou à Azure.
- **Portail CloudSimple** : Durant la maintenance, certaines fonctionnalités du portail CloudSimple peuvent être inaccessibles ou désactivées. La notification de maintenance inclut des informations sur les opérations que vous pouvez effectuer dans le portail.

## <a name="connectivity"></a>Connectivité

**Quelles sont les options à ma disposition pour me connecter au réseau de ma région CloudSimple ?**

CloudSimple offre trois options de connectivité différentes pour vous connecter au réseau de votre région CloudSimple. Vous pouvez utiliser ces trois options ensemble :

- Connexion Azure ExpressRoute de votre centre de données local au réseau de la région CloudSimple : connexion privée, sécurisée, haut débit et à faible latence qui relie votre circuit ExpressRoute local à votre circuit ExpressRoute CloudSimple à l’aide de Global Reach. Pour configurer la connexion, consultez [Connecter un réseau local à CloudSimple à l’aide d’ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection).
- Connexion ExpressRoute de votre réseau virtuel Azure au réseau de votre région CloudSimple : connexion privée, sécurisée, haut débit et à faible latence qui relie votre réseau virtuel sur Azure à votre circuit ExpressRoute CloudSimple à l’aide de passerelles de réseau virtuel. Pour configurer la connexion, consultez [Connecter votre environnement de cloud privé de CloudSimple au réseau virtuel Azure à l’aide d’ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
- Connexion VPN de site à site de votre centre de données local au réseau de votre région CloudSimple : réseau privé virtuel sécurisé de votre périphérique VPN local à la région de votre cloud privé CloudSimple. Pour configurer la connexion, consultez [Configurer une connexion VPN entre votre réseau local et le cloud privé CloudSimple](https://docs.azure.cloudsimple.com/set-up-vpn).

**Comment se connecter à un cloud privé ?**

Vous pouvez afficher les détails de votre cloud privé dans le portail CloudSimple. Pour vous connecter au serveur vCenter qui correspond à votre cloud privé, vérifiez qu’une connexion réseau est établie (site à site, point à site ou ExpressRoute). Ensuite, lancez le portail CloudSimple à partir du portail Azure. Sélectionnez **Launch vSphere Client** dans la page d’accueil ou dans la page de détails du cloud privé.

**Quel est l’avantage d’un circuit ExpressRoute ?**

Un circuit Azure ExpressRoute fournit une connexion sécurisée à haut débit et à faible latence. CloudSimple fournit un circuit ExpressRoute dédié par région et par client. Ce circuit vous permet d’établir une connexion sécurisée à partir de votre réseau local et de votre abonnement Azure.

**Quels sont les coûts réseau d’une connexion à destination et à partir de CloudSimple ? Existe-t-il des frais de sortie entre CloudSimple et Azure ? Existe-t-il des frais de sortie entre les régions ?**

La sortie du réseau n’engendre pas de frais. Les tarifs Azure standard s’appliquent à tout le trafic de sortie issu de votre réseau virtuel ou d’un circuit ExpressRoute local.

## <a name="networking"></a>Mise en réseau

**Quelles sont les fonctionnalités réseau disponibles pour mon cloud privé ?**

Vous pouvez provisionner des réseaux locaux virtuels et leurs sous-réseaux et tables de pare-feu. Vous pouvez affecter des adresses IP publiques et les mapper à une machine virtuelle qui s’exécute dans votre cloud privé. Pour plus d’informations, consultez [Vue d’ensemble des réseaux locaux virtuels et des sous-réseaux](cloudsimple-vlans-subnets.md), [Vue d’ensemble des tables de pare-feu](cloudsimple-firewall-tables.md) et [Vue d’ensemble des adresses IP publiques](cloudsimple-public-ip-address.md).

**Comment configurer différents sous-réseaux pour les applications de mon cloud privé ?**

Vous pouvez créer des réseaux locaux virtuels sur votre cloud privé à partir de votre portail CloudSimple. Après avoir créé le réseau local virtuel, vous pouvez créer un groupe de ports distribué sur le serveur vCenter de votre cloud privé en utilisant le réseau local virtuel et créer des machines virtuelles connectées au groupe de ports distribué. Vous pouvez activer une table de pare-feu pour le réseau local virtuel ou le sous-réseau et définir des règles de pare-feu pour sécuriser le trafic réseau.

**Quels sont les paramètres de pare-feu disponibles pour mes clouds privés ?**

Vous pouvez configurer des règles pour le trafic Nord-Sud et Est-Ouest. Les règles sont définies dans une table de pare-feu. La table de pare-feu peut être associée à des réseaux locaux virtuels sur votre cloud privé. Pour obtenir la procédure d’installation, consultez [Configurer des tables et des règles de pare-feu pour les clouds privés](https://docs.azure.cloudsimple.com/firewall).

**Puis-je affecter des adresses IP publiques aux machines virtuelles de mon environnement de cloud privé ?**

Dans le portail CloudSimple, vous pouvez facilement allouer une nouvelle adresse IP publique et l’associer à une adresse IP privée de votre machine virtuelle ou d’une appliance. Vous pouvez également créer des règles de pare-feu ou appliquer des règles de pare-feu existantes pour autoriser le trafic issu de ports et d’ensembles d’adresses IP spécifiques dans le portail. Pour obtenir la procédure d’installation, consultez [Allouer des adresses IP publiques pour un environnement de cloud privé](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Sécurité

**Quelles sont mes options de sécurité sur CloudSimple ?**

Un cloud privé CloudSimple fournit les fonctionnalités de sécurité suivantes pour sécuriser votre environnement de cloud privé :

- **Chiffrement des données au repos :** vous pouvez chiffrer les données au repos qui résident sur le stockage vSAN dans votre cloud privé. vSAN prend en charge un serveur gestionnaire de clés externe qui peut être déployé dans votre réseau virtuel Azure ou votre environnement local. Pour plus d’informations, consultez [Configurer le chiffrement vSAN pour votre cloud privé CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption).
- **Sécurité réseau :** contrôlez le trafic réseau en provenance et à destination de votre cloud privé à partir d’internet, du réseau local et au sein des sous-réseaux de votre cloud privé à l’aide de règles de pare-feu.
- **Connexion privée et sécurisée :** bénéficiez d’une connexion privée et sécurisée entre votre réseau local et votre abonnement Azure.

## <a name="compute"></a>Calcul

**Quels sont les types d’hôtes disponibles ?**

CloudSimple propose deux types d’hôtes :

* **Nœud CS28** : Processeur : 2 x 2,2 GHz, 28 cœurs en tout, 48 HT. RAM : 256 Go. Stockage : 1 600 Go de cache NVMe, 5 760 Go de données (entièrement Flash). Réseau : carte d’interface réseau 2x25 GbE.
* **Nœud CS36** : Processeur : 2 x 2,3 GHz, 36 cœurs en tout, 72 HT. RAM : 512 Go. Stockage : 3 200 Go de cache NVMe, 11 520 Go de données (entièrement Flash). Réseau : carte d’interface réseau 2x25 GbE.

**Comment sont gérées les pannes matérielles ?**

Toute l’infrastructure de CloudSimple est supervisée en permanence par la plateforme CloudSimple et ses équipes en charge des opérations de service. Si une panne matérielle est détectée, un nouveau nœud est ajouté à votre cloud privé. Le nœud défaillant est supprimé pour garantir la haute disponibilité de votre cloud privé.

## <a name="storage"></a>Stockage

**Quel type de stockage est pris en charge sur un cloud privé ?**

CloudSimple offre un **stockage VMware vSAN entièrement Flash** avec chaque cloud privé. Chaque vSphere est créé avec a propre banque de données vSAN. Pour plus d’informations, consultez [Composants VMware de cloud privé - Stockage vSAN](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**Le chiffrement des données est-il pris en charge ?**
Oui. Vous pouvez configurer le stockage vSAN sur votre cloud privé pour utiliser un serveur de gestionnaire de clés (KMS), qui est déployé localement ou sur Azure pour chiffrer les données stockées sur vSAN.

**Comment sont gérés les disques défaillants ?**

La supervision CloudSimple supervise en permanence tous les composants matériels du cloud privé. Si une panne de disque est détectée ou qu’un disque est identifié comme défaillant en fonction de paramètres heuristiques, un nouveau nœud est automatiquement ajouté au cloud privé. Le nœud avec un disque défaillant ou en cours d défaillance est supprimé du cloud privé.

## <a name="vmware"></a>VMware

**Comment faire pour effectuer le chargement et la migration à grande échelle d’applications et de données à partir d’un réseau local ?**

CloudSimple fournit une solution VMware vSphere native. Vous pouvez vous servir de n’importe quel outil utilisé pour la migration de données en bloc avec un cloud privé CloudSimple. Certaines des options disponibles sont les suivantes :

- VMware HCX pour la migration en bloc de données.
- Migration à froid des données à l’aide de Storage vMotion à partir d’un réseau local vers CloudSimple.

**Puis-je installer des outils VMware ?**

CloudSimple fournit une solution VMware vSphere native. Vous pouvez vous servir de n’importe quel outil utilisé pour gérer un environnement vSphere localement sur CloudSimple. CloudSimple prend en charge un modèle BYOL (apportez votre propre licence) pour installer les outils VMware.

**Comment sont gérées les mises à jour et les mises à niveau ?**

CloudSimple gère et met à jour tous les composants d’infrastructure de votre cloud privé de manière transparente et sans interruption de service. L’application des mises à jour et des correctifs de sécurité publiés par VMware ou les fournisseurs d’infrastructure est planifiée dès que CloudSimple les valide.

CloudSimple n’effectue ni la mise à niveau ni la mise à jour des applications installées sur le cloud privé.

## <a name="azure-integration"></a>Intégration à Azure

**Quels sont les services Azure pris en charge ?**

CloudSimple fournit une connexion Azure ExpressRoute à votre abonnement sur Azure. Tous les services qui s’exécutent dans votre abonnement ont une connectivité réseau à votre cloud privé et peuvent s’y connecter. Exemples :

- **Azure Active Directory** : utilisez Azure Active Directory comme source d’identité pour votre vCenter CloudSimple.
- **Stockage Azure** : utilisez ce stockage pour stocker les sauvegardes, images et autres données de votre cloud privé.
- **Applications hybrides** : vous pouvez créer une architecture d’application qui s’étend aux clouds publics et privés. Par exemple, vous pouvez créer des serveurs web dans Azure qui accèdent aux serveurs d’application et de base de données dans un cloud privé CloudSimple.
- **Azure Monitor** et **Azure Security Center** : une charge de travail qui s’exécute sur VMware peut utiliser Monitor et Security Center pour la journalisation, les métriques de performances et la gestion de la sécurité.

**Comment mapper mes clients VMware à Azure ?**

CloudSimple est la seule solution qui vous permet de gérer vos machines virtuelles VMware sur un cloud privé à partir du portail Azure. Un pool de ressources vCenter configuré avec les contraintes de ressources souhaitées peut être mappé à votre abonnement par l’administrateur général. 

**Quels sont les avantages qu’offrent les licences Azure ?**

Avec CloudSimple, vous pouvez tirer parti de l’offre Azure Hybrid Benefit et économiser jusqu’à 90 % du prix des licences pour préserver votre investissement dans les licences Microsoft et réduire votre coût total de possession par rapport à d’autres clouds. Vous bénéficiez également de mises à jour de sécurité étendues pour Windows Server 2008 et Microsoft SQL Server 2008. Limitez vos coûts en implémentant le modèle BYOL (apportez votre propre licence) sur le cloud pour les applications courantes telles que Veeam, Zerto, etc. 
