---
title: Considérations relatives à la mise en réseau Azure Files | Microsoft Docs
description: Vue d’ensemble des options de réseau pour Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 596479652478bffb6d18a90fc53d5972b3839408
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73126535"
---
# <a name="azure-files-networking-considerations"></a>Considérations relatives à la mise en réseau Azure Files 
Vous pouvez vous connecter à un partage de fichiers Azure de deux manières :

- En accédant au partage directement via les protocoles SMB ou FileREST. Ce modèle d’accès est principalement utilisé pour éliminer autant de serveurs locaux que possible.
- En créant un cache du partage de fichiers Azure sur un serveur local avec Azure File Sync, puis en accédant aux données du partage de fichiers à partir du serveur local avec le protocole de votre choix (SMB, NFS, FTPS, etc.) en fonction de votre cas d’utilisation. Ce modèle d’accès est pratique car il combine le meilleur des performances locales et de l’échelle du cloud, ainsi que des services pouvant être attachés sans serveur (serverless), tels que Sauvegarde Azure.

Cet article explique comment configurer la mise en réseau lorsque votre cas d’utilisation demande à accéder directement au partage de fichiers Azure au lieu d’utiliser Azure File Sync. Pour plus d’informations sur les considérations relatives à la mise en réseau pour un déploiement d’Azure File Sync, voir [Paramètres de proxy et de pare-feu d’Azure File Sync](storage-sync-files-firewall-and-proxy.md).

## <a name="storage-account-settings"></a>Paramètres du compte de stockage
Un compte de stockage est une construction de gestion qui représente un pool de stockage partagé dans lequel vous pouvez déployer plusieurs partages de fichiers, ainsi que d’autres ressources de stockage, telles que des conteneurs d’objets blob ou des files d’attente. Des comptes de stockage Azure exposent deux ensembles de paramètres de base pour sécuriser le réseau : le chiffrement en transit, et les pare-feu et réseaux virtuels.

### <a name="encryption-in-transit"></a>Chiffrement en transit
Par défaut, le chiffrement en transit est activé pour tous les comptes de stockage Azure. Cela signifie que, lorsque vous montez un partage de fichiers sur SMB ou y accédez en utilisant le protocole FileREST (par exemple, via le portail Azure, PowerShell/CLI ou des kits de développement logiciel (SDK) Azure), Azure Files n’autorise la connexion que si elle est établie à l’aide des protocoles SMB 3.0+ avec chiffrement ou HTTPS. Les clients qui ne prennent pas en charge le protocole SMB 3.0, ou qui prennent en charge le protocole SMB 3.0 mais pas le chiffrement SMB, ne peuvent pas monter le partage de fichiers Azure si le chiffrement en transit est activé. Pour plus d’informations sur les systèmes d’exploitation prenant en charge SMB 3.0 avec chiffrement, consultez notre documentation détaillée pour [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) et [Linux](storage-how-to-use-files-linux.md). Toutes les versions actuelles de PowerShell, de CLI et des SDK prennent en charge le protocole HTTPS.  

Vous pouvez désactiver le chiffrement en transit pour un compte de stockage Azure. Lorsque le chiffrement est désactivé, Azure Files autorise également les protocoles SMB 2.1 et SMB 3.0 sans chiffrement, ainsi que les appels d’API FileREST non chiffrés via le protocole HTTP. La principale raison justifiant de désactiver le chiffrement en transit est la nécessité de prendre en charge une application héritée devant être exécutée sur un système d’exploitation plus ancien, tel que Windows Server 2008 R2 ou une distribution Linux non récente. Azure Files n’autorise que les connexions SMB 2.1 au sein de la même région Azure que le partage de fichiers Azure. Ainsi, un client SMB 2.1 situé en dehors de la région Azure dans laquelle se trouve le partage de fichiers Azure, par exemple, localement ou dans une autre région Azure, ne peut pas accéder au partage de fichiers.

Pour plus d’informations sur le chiffrement en transit, voir [ Exiger un transfert sécurisé dans Stockage Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="firewalls-and-virtual-networks"></a>Pare-feu et réseaux virtuels 
Un pare-feu est une stratégie réseau dont les demandes sont autorisées à accéder aux partages de fichiers Azure et à d’autres ressources de stockage de votre compte de stockage. Quand un compte de stockage est créé avec les paramètres réseau par défaut, il n’est pas limité à un réseau spécifique et est donc accessible via Internet. Cela ne signifie pas que quiconque sur Internet peut accéder aux données stockées sur les partages de fichiers Azure hébergés dans votre compte de stockage, mais que le compte de stockage accepte des demandes autorisées de tout réseau. Les demandes peuvent être autorisées avec une clé de compte de stockage, un jeton de signature d’accès partagé (SAP) (uniquement FileREST) ou avec un principal d’utilisateur Active Directory. 

La stratégie de pare-feu d’un compte de stockage peut être utilisée pour restreindre l’accès à certaines adresses ou plages d’adresses IP, ou à un réseau virtuel. En général, la plupart des stratégies de pare-feu pour un compte de stockage restreignent l’accès réseau à un réseau virtuel. 

Un [réseau virtuel](../../virtual-network/virtual-networks-overview.md), ou VNet, est similaire à un réseau traditionnel que vous exploiteriez dans votre propre centre de données. Il vous permet de créer un canal de communication sécurisé pour vos ressources Azure, telles que des partages de fichiers Azure, des machines virtuelles, des bases de données SQL et autres, afin qu’elles puissent communiquer entre elles. À l’instar d’un compte de stockage Azure ou d’une machine virtuelle Azure, un réseau virtuel est une ressource Azure déployée dans un groupe de ressources. Avec une configuration réseau supplémentaire, des réseaux virtuels Azure peuvent également être connectés à vos réseaux locaux.

Quand des ressources telles qu’une machine virtuelle Azure sont ajoutées à un réseau virtuel, une interface de réseau virtuel (NIC) attachée à la machine virtuelle est limitée à ce réseau virtuel. Cela est possible parce que les machines virtuelles Azure sont des ordinateurs virtualisés qui disposent bien entendu de NIC. Des machines virtuelles sont proposées dans le cadre de la gamme de produits d’infrastructure en tant que service (IaaS, infrastructure-as-a-service) d’Azure. Étant donné que les partages de fichiers Azure sont serverless, ils ne disposent pas de NIC que vous pouvez ajouter à un réseau virtuel. Autrement dit, le service Azure Files est proposé dans le cadre de la gamme de produits de plateforme en tant que service (PaaS, platform-as-a-service) d’Azure. Pour permettre à un compte de stockage de faire partie d’un réseau virtuel, Azure prend en charge un concept de services PaaS appelé points de terminaison de service. Un point de terminaison de service permet à des services PaaS de faire partie d’un réseau virtuel. Pour en savoir plus sur les points de terminaison de service, voir [Points de terminaison de service de réseau virtuel](../../virtual-network/virtual-network-service-endpoints-overview.md).

Un compte de stockage peut être ajouté à un ou plusieurs réseaux virtuels. Pour en savoir plus sur l’ajout de votre compte de stockage à un réseau virtuel, ou la configuration d’autres paramètres de pare-feu, voir [Configurer des pare-feux et des réseaux virtuels dans Stockage Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="azure-networking"></a>Mise en réseau Azure
Par défaut, les services Azure, y compris Azure Files, sont accessibles via Internet. Dans la mesure où le trafic vers votre compte de stockage est chiffré par défaut (et que les montages SMB 2.1 ne sont jamais autorisés en dehors d’une région Azure), il n’est pas intrinsèquement non sûr d’accéder à vos partages de fichiers Azure sur Internet. En fonction de la stratégie de votre organisation ou d’exigences réglementaires uniques, vous pouvez avoir besoin d’une communication plus restrictive avec Azure. Par conséquent, Azure offre plusieurs méthodes pour limiter la façon dont le trafic provenant de l’extérieur d’Azure est acheminé vers Azure Files. Vous pouvez renforcer la sécurité de votre mise en réseau lorsque vous accédez à votre partage de fichiers Azure en utilisant les offres de services suivantes :

- [Passerelle VPN Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md) : une passerelle VPN est un type spécifique de passerelle de réseau virtuel utilisé pour envoyer du trafic chiffré d’un réseau virtuel Azure vers un autre emplacement (par exemple local) via Internet. Une passerelle VPN Azure est une ressource Azure qui peut être déployée dans un groupe de ressources aux côtés d’un compte de stockage ou d’autres ressources Azure. Les passerelles VPN exposent deux types de connexion :
    - Des connexions de passerelle [VPN point à site (P2S)](../../vpn-gateway/point-to-site-about.md) , qui sont des connexions VPN entre Azure et un client individuel. Cette solution est principalement utile pour des appareils ne faisant pas partie du réseau local de votre organisation. C’est le cas, par exemple, quand des télétravailleurs souhaitent pouvoir monter leur partage de fichiers Azure à partir de chez eux, d’un café ou d’un hôtel quand ils sont en déplacement. Pour utiliser une connexion VPN P2S avec Azure Files, une connexion VPN P2S doit être configurée pour chaque client désireux de se connecter. 
    - Des connexions [VPN site à site (S2S) ](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) entre Azure et le réseau de votre organisation. Une connexion VPN S2S vous permet de configurer une connexion VPN une seule fois pour un serveur VPN ou un appareil hébergé sur le réseau de votre entreprise, au lieu de le faire que pour chaque appareil client devant accéder à votre partage de fichiers Azure.
- [ExpressRoute](../../expressroute/expressroute-introduction.md), qui vous permet de créer un itinéraire défini entre Azure et votre réseau local qui ne traverse pas Internet. ExpressRoute peut être utile quand les performances du réseau doivent être prises en compte, car il fournit un chemin dédié entre votre centre de données local et Azure. ExpressRoute est également une bonne option quand une stratégie ou des exigences réglementaires de votre organisation exigent un chemin d’accès déterministe à vos ressources dans le cloud.

## <a name="securing-access-from-on-premises"></a>Sécurisation de l’accès à partir d’appareils locaux 
Lorsque vous migrez des partages de fichiers à usage général (par exemple, pour des fichiers Office, PDF, CAO, etc.) vers Azure Files, vos utilisateurs continuent généralement d’avoir besoin d’accéder à leurs fichiers à partir d’appareils locaux tels que leurs postes de travail, ordinateurs portables et autres tablettes. La principale considération en lien avec un partage de fichiers à usage général est la manière dont les utilisateurs locaux peuvent accéder en toute sécurité à leurs partages de fichiers via Internet ou un réseau étendu.

Le moyen le plus simple d’accéder à votre partage de fichiers Azure à partir d’un site local consiste à ouvrir votre réseau local sur le port 445, celui qu’utilise le protocole SMB, puis à monter le chemin UNC fourni par le portail Azure. Cela ne nécessite aucune mise en réseau particulière. De nombreux clients sont réticents à l’idée d’ouvrir le port 445 en raison de directives de sécurité obsolètes en lien avec le protocole SMB 1.0 que Microsoft ne juge pas sûr pour Internet. Azure Files n’implémente pas le protocole SMB 1.0. 

Le protocole SMB 3.0 a été conçu avec l’exigence explicite de mettre au point un protocoles de partage de fichiers sûrs pour Internet. Par conséquent, en ce qui concerne l’utilisation du protocole SMB 3.0+ pour la mise en réseau d’un ordinateur, l’ouverture du port 445 ne diffère pas de celle du port 443 utilisé pour des connexions HTTPS. Au lieu de bloquer le port 445 pour empêcher le trafic SMB 1.0 non sécurisé, Microsoft recommande les étapes suivantes :

> [!Important]  
> Même si vous décidez de laisser le port 445 fermé au trafic sortant, Microsoft vous recommande de suivre ces étapes pour supprimer le protocole SMB 1.0 de votre environnement.

1. Assurez-vous que le protocole SMB 1.0 est supprimé ou désactivé sur les appareils de votre organisation. Toutes les versions actuellement prises en charge de Windows et Windows Server prennent en charge la suppression ou la désactivation du protocole SMB 1.0 et, depuis Windows 10 version 1709, le protocole SMB 1.0 n’est plus installé par défaut. Pour en savoir plus sur la façon de désactiver le protocole SMB 1.0, voir les pages spécifiques du système d’exploitation :
    - [Sécurisation de Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Sécurisation de Linux](storage-how-to-use-files-linux.md#securing-linux)
1. Assurez-vous qu’aucun produit au sein de votre entreprise ne nécessite le protocole SMB 1.0, et supprimez ceux qui en ont besoin. Nous maintenons un [centre d’échanges de produits SMB1](https://aka.ms/stillneedssmb1) qui contient tous les produits Microsoft et tiers connus pour exiger le protocole SMB 1.0. 
1. (Facultatif) Utilisez un pare-feu tiers avec le réseau local de votre entreprise pour empêcher le trafic SMB 1.0.

Si votre organisation exige le blocage du port 445 en vertu d’une stratégie ou d’une réglementation, vous pouvez utiliser une passerelle VPN Azure ou ExpressRoute pour canaliser le trafic sur le port 443. Pour en savoir plus sur les étapes spécifiques de ces déploiement, consultez nos pages de procédures spécifiques :
- [Configurer un VPN site à site (S2S) pour une utilisation avec Azure Files](storage-files-configure-s2s-vpn.md)
- [Configurer un VPN point à site (P2S) sous Windows pour une utilisation avec Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Configurer un VPN point à site (P2S) sous Linux pour une utilisation avec Azure Files](storage-files-configure-p2s-vpn-linux.md)

Votre entreprise peut également exiger que le trafic sortant de votre site local suive un chemin d’accès déterministe menant à vos ressources dans le cloud. Si tel est le cas, ExpressRoute est capable de répondre à cette exigence.

## <a name="securing-access-from-cloud-resources"></a>Sécurisation de l’accès à partir de ressources cloud
En règle générale, quand une application locale est migrée par « lift-and-shift » vers le cloud, l’application et ses données sont déplacées simultanément. Cela signifie que la principale précaution à prendre pour une migration par « lift-and-shift » consiste à restreindre l’accès au partage de fichiers Azure aux machines virtuelles ou services Azure spécifiques qui nécessitent un accès au partage de fichiers pour fonctionner. 

Vous pouvez également utiliser des réseaux virtuels pour limiter les machines virtuelles ou autres ressources Azure autorisées à établir des connexions réseau (montages SMB ou appels d’API REST sur votre partage de fichiers Azure). Nous vous recommandons toujours de placer votre partage de fichiers Azure dans un réseau virtuel si vous autorisez un trafic non chiffré vers votre compte de stockage. Autrement, la décision d’utiliser ou non des réseaux virtuels devrait être dictée par vos exigences métier et la stratégie de votre organisation.

La principale raison d’autoriser le trafic non chiffré vers votre partage de fichiers Azure est de prendre en charge Windows Server 2008 R2, Windows 7 ou un autre système d’exploitation plus ancien accédant à votre partage de fichiers Azure en utilisant le protocole SMB 2.1 (ou SMB 3.0 sans chiffrement pour certaines distributions Linux). Il n’est pas conseillé d’utiliser le protocole SMB 2.1 ou SMB 3.0 sans chiffrement sur des systèmes d’exploitation prenant en charge le protocole SMB 3.0+ avec chiffrement.

## <a name="see-also"></a>Voir aussi
- [Vue d’ensemble d’Azure Files](storage-files-introduction.md)
- [Planification d’un déploiement Azure Files](storage-files-planning.md)