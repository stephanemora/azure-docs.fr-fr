---
title: Intégrer Microsoft Azure avec l’infrastructure cloud Oracle | Microsoft Docs
description: Découvrez les solutions qui intègrent des applications Oracle exécutées sur Microsoft Azure dans des bases de données de l’infrastructure cloud Oracle (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: e70eedcfcdf548965b79e4a48a3a8bfa643f0396
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687425"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Solutions d’applications Oracle intégrant Microsoft Azure et l’infrastructure cloud Oracle

Microsoft et Oracle ont collaboré pour fournir une latence faible et une connectivité à débit élevé entre clouds, ce qui vous permet de bénéficier du meilleur des deux clouds. 

Cette connectivité entre clouds permet de partitionner une application multiniveau pour exécuter votre niveau de base de données sur une infrastructure cloud Oracle (OCI), et l’application ainsi que d’autres niveaux sur Microsoft Azure. L’expérience est similaire à l’exécution de la pile complète de la solution dans un seul cloud. 

Si vous êtes intéressé par le déploiement complet de solutions Oracle sur l’infrastructure Azure, consultez [Images de machines virtuelles Oracle et leur déploiement sur Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Présentation du scénario

La connectivité entre clouds vous fournit une solution permettant d’exécuter les applications leaders d’Oracle et vos propres applications personnalisées sur des machines virtuelles Azure tout en bénéficiant des avantages de services de base de données hébergés dans OCI. 

> [!IMPORTANT]
> Oracle certifie que ces applications s’exécutent dans Azure lors de l’utilisation de la solution d’interconnexion Azure / Oracle Cloud en mai 2020.

Vous pouvez notamment exécuter les applications suivantes dans une configuration entre clouds :

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Applications Oracle Retail
* Oracle Hyperion Financial Management

Le diagramme suivant est une vue d’ensemble approfondie de la solution connectée. Par souci de simplicité, le diagramme montre uniquement un niveau d’application et un niveau de données. En fonction de l’architecture de l’application, votre solution peut inclure des niveaux supplémentaires, tels qu’une couche web dans Azure. Pour plus d’informations, consultez les sections suivantes.

![Vue d’ensemble de la solution Azure OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Disponibilité dans les régions 

La connectivité intercloud est limitée aux régions suivantes :
* Azure USA Est (eastus) et OCI Ashburn (USA Es)
* Azure Royaume-Uni Sud (uksouth) et OCI London (Royaume-Uni Sud)
* Azure Canada Centre (canadacentral) et OCI Toronto (Canada Sud-Est)
* Azure Europe Ouest (westeurope) et OCI Amsterdam (Pays-Bas Nord-Ouest)
* Azure Japon Est (japaneast) & OCI Tokyo (Japon Est)

## <a name="networking"></a>Mise en réseau

Les clients d’entreprise choisissent souvent de diversifier et de déployer des charges de travail sur plusieurs clouds pour différentes raisons commerciales et opérationnelles. Pour diversifier, les clients interconnectent des réseaux cloud avec Internet, IPSec ou un VPN, ou avec la solution de connectivité directe du fournisseur du cloud via leur réseau local. L’interconnexion de réseaux cloud peut nécessiter des investissements significatifs en matière de temps, d’argent, de conception, d’approvisionnement, d’installation, de tests et d’opérations. 

Pour répondre à ces défis des clients, Oracle et Microsoft ont activé une expérience multicloud intégrée. La mise en réseau entre clouds est établie en connectant un circuit [ExpressRoute](../../../expressroute/expressroute-introduction.md) dans Microsoft Azure avec un circuit [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) dans OCI. Cette connectivité est possible là où un emplacement de peering ExpressRoute est au même emplacement de peering qu’OCI FastConnect ou à proximité. Cette configuration fournit une connectivité sécurisée et rapide entre les deux clouds sans avoir besoin d’un fournisseur de services intermédiaire.

À l’aide d’ExpressRoute et de FastConnect, les clients peuvent homologuer un réseau virtuel dans Azure avec un réseau virtuel cloud dans OCI, à condition que l’espace d’adressage IP privé ne se chevauche pas. Le peering des deux réseaux permet à une ressource du réseau virtuel de communiquer avec une ressource du réseau cloud virtuel OCI comme si elles étaient toutes deux dans le même réseau.

## <a name="network-security"></a>Sécurité du réseau

La sécurité réseau est une composante essentielle de toute application d'entreprise et est au centre de cette solution multicloud. Tout le trafic qui passe sur ExpressRoute et FastConnect traverse un réseau privé. Cette configuration permet une communication sécurisée entre un réseau virtuel Azure et un réseau cloud virtuel Oracle. Vous n’avez pas besoin de fournir une adresse IP publique à des machines virtuelles dans Azure. De même, vous n’avez pas besoin de passerelle Internet dans OCI. Toute la communication transite par l’adresse IP privée des ordinateurs.

De plus, vous pouvez configurer des [listes de sécurité](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) sur votre réseau cloud virtuel OCI et des règles de sécurité (attachées aux [groupes de sécurité réseau](../../../virtual-network/security-overview.md) Azure). Ces règles permettent de contrôler le flux du trafic entre les ordinateurs dans les réseaux virtuels. Des règles de sécurité réseau peuvent être ajoutées au niveau de l’ordinateur, à un niveau de sous-réseau, ainsi qu’au niveau du réseau virtuel.
 
## <a name="identity"></a>Identité

L’identité est un des principaux piliers du partenariat entre Microsoft et Oracle. Un travail considérable a été effectué pour intégrer [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) dans [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Azure AD est le service Microsoft basé sur le cloud qui gère les identités et les accès. Il aide vos utilisateurs à se connecter et à accéder aux différentes ressources. Azure AD permet également de gérer vos utilisateurs et leurs autorisations.

Actuellement, cette intégration permet de gérer à un emplacement central, à savoir Azure Active Directory. Azure AD synchronise toutes les modifications dans le répertoire contenant le répertoire Oracle correspondant et est utilisé pour l’authentification unique pour les solutions Oracle entre clouds.

## <a name="next-steps"></a>Étapes suivantes

Prise en main d’un [réseau entre des clouds](configure-azure-oci-networking.md) Azure et OCI. 

Pour plus d’informations et de livres blancs sur OCI, consultez la documentation [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm).
