---
title: 'Tutoriel : Check-list du réseau'
description: Prérequis pour les réseaux et détails sur la connectivité réseau et les ports réseau
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 42bd579a455c7efe3b8f6e4d4154e687726adb1d
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82739524"
---
# <a name="networking-checklist-for-azure-vmware-solution-avs"></a>Check-list du réseau pour Azure VMware Solution (AVS)

Azure VMware Solution (AVS) offre un environnement de cloud privé VMware, qui est accessible aux utilisateurs et aux applications à partir de ressources ou d’environnements locaux ou Azure. La connectivité est assurée via des services réseau comme Azure ExpressRoute et des connexions VPN, et elle nécessite certaines plages d’adresses réseau spécifiques et certains ports de pare-feu pour activer les services. Cet article vous fournit les informations dont vous avez besoin pour configurer correctement votre réseau afin qu’il fonctionne avec AVS.

Dans ce tutoriel, vous allez découvrir des informations sur :

> [!div class="checklist"]
> * Les exigences de connectivité réseau
> * DHCP dans AVS

## <a name="network-connectivity"></a>Connectivité réseau

Le cloud privé AVS est connecté à votre réseau virtuel Azure avec une connexion Azure ExpressRoute. Cette connexion à bande passante élevée et à faible latence vous permet d’accéder aux services qui s’exécutent dans votre abonnement Azure à partir de votre environnement cloud privé.

Les clouds privés AVS nécessitent au minimum un bloc d’adresses réseau CIDR `/22` pour les sous-réseaux, comme indiqué ci-dessous. Ce réseau complète vos réseaux locaux. Pour pouvoir vous connecter à des environnements locaux et à des réseaux virtuels, il doit s’agir d’un bloc d’adresses réseau qui ne les chevauche pas.

Exemple de bloc d’adresses réseau CIDR `/22` : `10.10.0.0/22`

Les sous-réseaux :

| Utilisation du réseau             | Subnet |  Exemple        |
| ------------------------- | ------ | -------------- |
| Gestion de cloud privé            | `/24`    | `10.10.0.0/24`   |
| Réseau vMotion       | `/24`    | `10.10.1.0/24`   |
| Charges de travail de machine virtuelle | `/24`   | `10.10.2.0/24`   |
| Appairage ExpressRoute | `/24`    | `10.10.3.8/30`   |

### <a name="network-ports-required-to-communicate-with-the-service"></a>Ports réseau nécessaires pour communiquer avec le service

Source|Destination|Protocol |Port |Description  |Commentaire
---|-----|:-----:|:-----:|-----|-----
Serveur DNS de cloud privé  |Serveur DNS local  |UDP |53|Client DNS - Transférer les demandes du PC vCenter pour toutes les requêtes DNS locales (consultez la section DNS ci-dessous) |
Serveur DNS local  |Serveur DNS de cloud privé  |UDP |53|Client DNS - Transférer les demandes des services locaux aux serveurs DNS du cloud privé (consultez la section DNS ci-dessous)
Réseau local  |Serveur vCenter de cloud privé  |TCP(HTTP)  |80|vCenter Server nécessite le port 80 pour les connexions HTTP directes. Le port 80 redirige les requêtes vers le port HTTPS 443. Cette redirection vous aide si vous utilisez `http://server` au lieu de `https://server`.  <br><br>WS-Management (nécessite également que le port 443 soit ouvert) <br><br>Si vous utilisez une base de données Microsoft SQL personnalisée et non pas la base de données SQL Server 2008 du bundle sur le serveur vCenter Server, le port 80 est utilisé par les services SQL Reporting Services. Quand vous installez vCenter Server, le programme d’installation vous invite à changer le port HTTP du serveur vCenter Server. Remplacez le port HTTP du serveur vCenter Server par une valeur personnalisée pour garantir une installation correcte. Microsoft Internet Information Services (IIS) utilise également le port 80. Consultez Conflit entre vCenter Server et IIS pour le port 80.
Réseau de gestion de cloud privé |Active Directory local  |TCP  |389|Ce port doit être ouvert sur l’instance locale et sur toutes les instances distantes de vCenter Server. Ce port est le numéro de port LDAP des services d’annuaire pour le groupe vCenter Server. Le système vCenter Server doit être lié au port 389, même si vous ne joignez pas à cette instance vCenter Server à un groupe en mode lié. Si un autre service s’exécute sur ce port, il peut être préférable de le supprimer ou de le remplacer par un autre port. Vous pouvez exécuter le service LDAP sur n’importe quel port compris entre 1 025 et 65 535.  Si cette instance est utilisée pour Microsoft Windows Active Directory, remplacez le numéro de port 389 par un port disponible compris entre 1 025 et 65 535. Ce port est facultatif : pour configurer AD local en tant que source d’identité sur le cloud privé vCenter
Réseau local  |Serveur vCenter de cloud privé  |TCP(HTTPS)  |443|Ce port vous permet d’accéder à vCenter depuis un réseau local. Le port par défaut utilisé par le système vCenter Server pour écouter les connexions à partir du client vSphere. Pour permettre au système vCenter Server de recevoir des données à partir du client vSphere, ouvrez le port 443 dans le pare-feu. Le système vCenter Server utilise également le port 443 pour surveiller le transfert de données à partir des clients du SDK. Ce port est également utilisé pour les services suivants : WS-Management (nécessite également que le port 80 soit ouvert). Accès du client vSphere à vSphere Update Manager. Connexions des clients de gestion réseau de tiers à vCenter Server. Accès des clients de gestion réseau de tiers aux hôtes. 
Navigateur web  | Gestionnaire de cloud hybride  | TCP(HTTPS) | 9443 | Interface de gestion des appliances virtuelles du Gestionnaire de cloud hybride pour la configuration du système du Gestionnaire de cloud hybride.
Réseau d’administration  | Gestionnaire de cloud hybride |ssh |22| Accès SSH de l’administrateur au Gestionnaire de cloud hybride.
HCM |   Passerelle cloud|TCP(HTTPS) |8123| Envoyer des instructions du service de réplication basé sur l’hôte à la passerelle cloud hybride.
HCM |   Passerelle cloud  |    HTTP TCP(HTTPS) |    9443  |  Envoyer des instructions de gestion à la passerelle cloud hybride locale en utilisant l’API REST.
Passerelle cloud|      L2C |    TCP (HTTP)  |   443 |   Envoyer des instructions de gestion de la passerelle cloud à L2C quand L2C utilise le même chemin que la passerelle cloud hybride.
Passerelle cloud |     Hôtes ESXi |     TCP |    80,902  |   Gestion et déploiement OVF
Passerelle cloud (locale)|     Passerelle cloud (distante)|     UDP  |   4500 | Nécessaire pour IPSEC<br>   Échange de clés Internet (IKEv2) pour encapsuler les charges de travail pour le tunnel bidirectionnel. NAT-T (Network Address Translation-Traversal) est également pris en charge.
Passerelle cloud (locale)  |   Passerelle cloud (distante)  |   UDP  |   500   | Nécessaire pour IPSEC<br> Échange de clés Internet (ISAKMP) pour le tunnel bidirectionnel.
Réseau vCenter local|      Réseau de gestion de cloud privé|      TCP  |    8000    |  vMotion des machines virtuelles d’un vCenter local vers un vCenter de cloud privé   |     

## <a name="dhcp"></a>DHCP

Les applications et les charges de travail exécutées dans un environnement de cloud privé nécessitent une résolution de nom et des services DHCP pour la recherche et l’affectation d’adresses IP. Une infrastructure DHCP et DNS appropriée est nécessaire pour fournir ces services. Vous pouvez configurer une machine virtuelle pour fournir ces services dans votre environnement de cloud privé.  
Il est recommandé d’utiliser le service DHCP intégré à NSX ou un serveur DHCP local dans le cloud privé au lieu de router le trafic DHCP de diffusion sur le réseau étendu (WAN) vers l’emplacement local.

Ce didacticiel vous à présenté les point suivants :

> [!div class="checklist"]
> * Les exigences de connectivité réseau
> * DHCP dans AVS

## <a name="next-steps"></a>Étapes suivantes

Une fois que le réseau approprié est en place, passez au tutoriel suivant pour créer votre cloud privé AVS.

> [!div class="nextstepaction"]
> [Tutoriel : Créer un cloud privé AVS](tutorial-create-private-cloud.md)
