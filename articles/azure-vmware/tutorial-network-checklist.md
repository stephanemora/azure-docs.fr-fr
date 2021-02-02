---
title: Tutoriel - Liste de vérification pour la planification réseau
description: Découvrez la configuration réseau requise pour la connectivité réseau et les ports réseau dans Azure VMware Solution.
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: f493443da617f5f76771e5d1b2b3b190b37f9b5d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918583"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Liste de vérification pour la planification réseau pour Azure VMware Solution 

Azure VMware Solution offre un environnement de cloud privé VMware accessible aux utilisateurs et aux applications à partir de ressources ou d’environnements locaux et Azure. La connectivité est fournie à travers des services réseau comme les connexions de réseau privé virtuel et Azure ExpressRoute. Elle a besoin de plages d’adresses réseau et de ports de pare-feu spécifiques pour activer les services. Cet article vous fournit les informations nécessaires pour configurer votre réseau afin qu’il fonctionne correctement avec Azure VMware Solution.

Ce tutoriel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Éléments à prendre en considération pour le réseau virtuel et le circuit ExpressRoute
> * Conditions requises pour le routage et le sous-réseau
> * Ports réseaux nécessaires pour communiquer avec les services
> * Considérations relatives à DHCP et DNS dans Azure VMware Solution

## <a name="prerequisite"></a>Prérequis
Vérifiez que toutes les passerelles, dont le service du fournisseur ExpressRoute, prennent en charge un numéro de système autonome (ASN) à 4 octets. Azure VMware Solution utilise des ASN publics à 4 octets pour publier les routes.

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>Éléments à prendre en considération pour le réseau virtuel et le circuit ExpressRoute
Quand vous créez une connexion de réseau virtuel dans votre abonnement, le circuit ExpressRoute s’établit par peering et utilise la clé d’autorisation et l’ID de peering que vous demandez sur le portail Azure. Le Peering est une connexion privée de type un-à-un entre votre cloud privé et le réseau virtuel.

> [!NOTE] 
> Le circuit ExpressRoute ne fait pas partie d’un déploiement de cloud privé. Le circuit ExpressRoute local dépasse le cadre de ce document. Si vous avez besoin d’une connexion locale à votre cloud privé, vous pouvez utiliser l’un de vos circuits ExpressRoute existants ou en acheter un dans le portail Azure.

Lors du déploiement d’un cloud privé, vous recevez des adresses IP pour vCenter et NSX-T Manager. Pour accéder à ces interfaces de gestion, vous devez créer des ressources supplémentaires dans le réseau virtuel de votre abonnement. Les procédures de création de ces ressources et d’établissement de [Peering privé ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) sont accessibles dans les tutoriels.

La mise en réseau logique du cloud privé inclut un environnement NSX-T pré-approvisionné. Une passerelle de niveau 0 et une passerelle de niveau 1 sont pré-approvisionnées pour vous. Vous pouvez créer un segment et l’attacher à la passerelle de niveau 1 existante ou à une nouvelle passerelle de niveau 1 que vous définissez. Les composants de réseau logique NSX-T fournissent une connectivité Est-Ouest entre les charges de travail et une connectivité Nord-Sud aux services Internet et Azure.

## <a name="routing-and-subnet-considerations"></a>Éléments à prendre en considération en matière de routage et de sous-réseaux
Le cloud privé Azure VMware Solution est connecté à votre réseau virtuel Azure au moyen d’une connexion Azure ExpressRoute. Cette connexion à bande passante élevée et à faible latence vous permet d’accéder aux services qui s’exécutent dans votre abonnement Azure à partir de votre environnement cloud privé. Le routage est basé sur le protocole BGP (Border Gateway Protocol), automatiquement provisionné et activé par défaut pour chaque déploiement de cloud privé. 

Les clouds privés Azure VMware Solution nécessitent au minimum un bloc d’adresses réseau CIDR `/22` pour les sous-réseaux, comme indiqué ci-dessous. Ce réseau complète vos réseaux locaux. Le bloc d’adresses ne doit pas chevaucher les blocs d’adresses utilisés dans d’autres réseaux virtuels situés dans votre abonnement et des réseaux locaux. Dans ce bloc d’adresses, la gestion, le provisionnement et les réseaux vMotion sont provisionnés automatiquement.

>[!NOTE]
>Les plages autorisées pour votre bloc d’adresses sont les espaces d’adressage privés RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), à l’exception de 172.17.0.0/16.

Exemple de bloc d’adresses réseau CIDR `/22` : `10.10.0.0/22`

Les sous-réseaux :

| Utilisation du réseau             | Subnet | Exemple          |
| ------------------------- | ------ | ---------------- |
| Gestion de cloud privé  | `/26`  | `10.10.0.0/26`   |
| Migrations de la gestion HCX       | `/26`  | `10.10.0.64/26`  |
| Global Reach, réservé     | `/26`  | `10.10.0.128/26` |
| ExpressRoute, réservé     | `/27`  | `10.10.0.192/27` |
| Appairage ExpressRoute      | `/27`  | `10.10.0.224/27` |
| Gestion ESXi           | `/25`  | `10.10.1.0/25`   |
| Réseau vMotion           | `/25`  | `10.10.1.128/25` |
| Réseau de réplication       | `/25`  | `10.10.2.0/25`   |
| vSAN                      | `/25`  | `10.10.2.128/25` |
| Liaison montante HCX                | `/26`  | `10.10.3.0/26`   |
| Réservé                  | `/26`  | `10.10.3.64/26`  |
| Réservé                  | `/26`  | `10.10.3.128/26` |
| Réservé                  | `/26`  | `10.10.3.192/26` |



## <a name="required-network-ports"></a>Ports réseau requis

| Source | Destination | Protocol | Port | Description  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| Serveur DNS de cloud privé | Serveur DNS local | UDP | 53 | Client DNS – Transfère les requêtes du PC vCenter pour toutes les requêtes DNS locales (consultez la section DNS ci-dessous) |  
| Serveur DNS local   | Serveur DNS de cloud privé | UDP | 53 | Client DNS – Transfère les requêtes des services locaux aux serveurs DNS du cloud privé (consultez la section DNS ci-dessous) |  
| Réseau local  | Serveur vCenter de cloud privé  | TCP(HTTP)  | 80 | vCenter Server a besoin du port 80 pour les connexions HTTP directes. Le port 80 redirige les requêtes vers le port HTTPS 443. Cette redirection est utile si vous utilisez `http://server` au lieu de `https://server`.  <br><br>WS-Management (exige également que le port 443 soit ouvert) <br><br>Si vous utilisez une base de données Microsoft SQL personnalisée et non la base de données SQL Server 2008 en bundle sur le serveur vCenter Server, le port 80 est utilisé par les services SQL Reporting Services. Quand vous installez vCenter Server, le programme d’installation vous invite à changer le port HTTP pour le serveur vCenter Server. Remplacez le port HTTP du serveur vCenter Server par une valeur personnalisée pour garantir la réussite de l’installation. Microsoft Internet Information Services (IIS) utilise également le port 80. Consultez Conflit entre vCenter Server et IIS pour le port 80. |  
| Réseau de gestion de cloud privé | Active Directory local  | TCP  | 389 | Ce port doit être ouvert sur l’instance locale et sur toutes les instances distantes de vCenter Server. Ce port est le numéro de port LDAP des services d’annuaire pour le groupe vCenter Server. Le système vCenter Server doit être lié au port 389, même si vous ne joignez pas cette instance vCenter Server à un groupe en mode lié. Si un autre service s’exécute sur ce port, il peut être préférable de le supprimer ou de le remplacer par un autre port. Vous pouvez exécuter le service LDAP sur n’importe quel port compris entre 1025 et 65535.  Si cette instance est utilisée pour Microsoft Windows Active Directory, remplacez le numéro de port 389 par un port disponible compris entre 1025 et 65535. Ce port est facultatif : pour la configuration locale d’AD en tant que source d’identité sur le vCenter du cloud privé. |  
| Réseau local  | Serveur vCenter de cloud privé  | TCP(HTTPS)  | 443 | Ce port vous permet d’accéder à vCenter depuis un réseau local. Port par défaut utilisé par le système vCenter Server pour écouter les connexions à partir du client vSphere. Pour permettre au système vCenter Server de recevoir des données à partir du client vSphere, ouvrez le port 443 dans le pare-feu. Le système vCenter Server utilise également le port 443 pour superviser le transfert de données à partir des clients du SDK. Ce port est aussi utilisé pour les services suivants : WS-Management (nécessite aussi que le port 80 soit ouvert). Accès du client vSphere à vSphere Update Manager. Connexions des clients de gestion réseau de tiers à vCenter Server. Accès des clients de gestion réseau tiers aux hôtes. |  
| Navigateur web  | Gestionnaire de cloud hybride  | TCP(HTTPS) | 9443 | Interface de gestion des appliances virtuelles du Gestionnaire de cloud hybride pour la configuration du système du Gestionnaire de cloud hybride. |
| Réseau d’administration  | Gestionnaire de cloud hybride | SSH | 22 | Accès SSH de l’administrateur au Gestionnaire de cloud hybride. |
| HCM | Passerelle cloud | TCP(HTTPS) | 8123 | Envoyer des instructions du service de réplication basé sur l’hôte à la passerelle cloud hybride. |
| HCM | Passerelle cloud | HTTP TCP(HTTPS) | 9443 | Envoyer des instructions de gestion à la passerelle cloud hybride locale en utilisant l’API REST. |
| Passerelle cloud | L2C | TCP(HTTPS) | 443 | Envoyer des instructions de gestion de la passerelle cloud à L2C quand L2C utilise le même chemin que la passerelle cloud hybride. |
| Passerelle cloud | Hôtes ESXi | TCP | 80,902 | Gestion et déploiement OVF. |
| Passerelle cloud (locale)| Passerelle cloud (distante) | UDP | 4500 | Nécessaire pour IPSEC<br>   Échange de clés Internet (IKEv2) pour encapsuler les charges de travail pour le tunnel bidirectionnel. NAT-T (Network Address Translation-Traversal) est également pris en charge. |
| Passerelle cloud (locale) | Passerelle cloud (distante)  | UDP | 500 | Nécessaire pour IPSEC<br> Échange de clés Internet (ISAKMP) pour le tunnel bidirectionnel. |
| Réseau vCenter local | Réseau de gestion de cloud privé | TCP | 8000 |  vMotion des machines virtuelles d’un vCenter local vers un vCenter de cloud privé   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>Éléments à prendre en considération pour la résolution DNS et DHCP
Les applications et les charges de travail exécutées dans un environnement de cloud privé nécessitent une résolution de nom et des services DHCP pour la recherche et les affectations d’adresses IP. Une infrastructure DHCP et DNS appropriée est requise pour fournir ces services. Vous pouvez configurer une machine virtuelle pour fournir ces services dans votre environnement de cloud privé.  

Utilisez le service DHCP intégré à NSX ou un serveur DHCP local dans le cloud privé au lieu de router le trafic DHCP de diffusion sur le réseau étendu (WAN) vers l’emplacement local.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert les conditions requises et les éléments à prendre en compte pour déployer un cloud privé Azure VMware Solution. 


Une fois que le réseau approprié est en place, passez au tutoriel suivant pour créer votre cloud privé Azure VMware Solution.

> [!div class="nextstepaction"]
> [Créer un cloud privé Azure VMware Solution](tutorial-create-private-cloud.md)
