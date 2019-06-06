---
title: Intégrer Microsoft Azure avec l’Infrastructure de Cloud Oracle | Microsoft Docs
description: En savoir plus sur les solutions qui intègrent des applications Oracle s’exécutant sur Microsoft Azure avec les bases de données dans Cloud Infrastructure OCI (Oracle).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 711fce9627537e68171c3b170121900d6b14ca1e
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743649"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Solutions d’applications Oracle l’intégration de Microsoft Azure et l’Infrastructure de Cloud Oracle (version préliminaire)

Microsoft et Oracle ont collaboré pour offrir une latence faible, une connectivité entre clouds un débit élevé, ce qui vous permet de tirer parti du meilleur des deux clouds. 

À l’aide de cette connectivité entre clouds, vous pouvez partitionner une application multiniveau pour exécuter votre niveau de base de données sur Cloud Infrastructure OCI (Oracle) et de l’application et d’autres niveaux sur Microsoft Azure. L’expérience est similaire à l’exécution de la pile de l’ensemble de la solution dans un seul cloud. 

> [!IMPORTANT]
> Cette fonctionnalité le cloud est actuellement en version préliminaire, certaines [limitations s’appliquent](#preview-limitations). Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

Si vous êtes intéressé par déployer des solutions Oracle entièrement sur l’infrastructure Azure, consultez [images de machine virtuelle Oracle et leur déploiement sur Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Présentation du scénario

Le cloud connectivité fournit une solution pour vous permettent d’exécuter des applications de pointe d’Oracle et de vos propres applications personnalisées, sur des machines virtuelles Azure en profitant des avantages des services de base de données hébergée dans OCI. 

Vous pouvez exécuter dans une configuration cloud entre les applications :

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Applications de vente au détail d’Oracle
* Oracle Hyperion de gestion financière

Le diagramme suivant est une vue d’ensemble de la solution connectée. Par souci de simplicité, le diagramme illustre uniquement une couche d’application et une couche de données. En fonction de l’application architecutre, votre solution peut inclure des niveaux supplémentaires comme une couche web dans Azure. Pour plus d’informations, consultez les sections suivantes.

![Présentation de la solution OCI Azure](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>Limitations de la version préliminaire

* Connectivité entre clouds en version préliminaire est limitée à la région Azure East US (eastus) et la région (us-ashburn-1) OCI Ashburn.

## <a name="networking"></a>Mise en réseau

Les clients d’entreprise choisissent souvent de diversifier et déployer des charges de travail sur plusieurs clouds pour différentes entreprises et les raisons opérationnelles. Pour diversifier, les clients interconnectent des réseaux en nuage à l’aide d’internet, IPSec VPN, ou à l’aide de la solution de connectivité directe du fournisseur du cloud via votre réseau local. Interconnexion de réseaux en nuage peut nécessiter des investissements significatifs dans le temps, d’argent, conception, d’approvisionnement, installation, test et opérations. 

Pour répondre à ces défis, Oracle et Microsoft ont activé une expérience cloud multi intégrée. Mise en réseau entre le cloud est établie en vous connectant une [ExpressRoute](../../../expressroute/expressroute-introduction.md) circuit dans Microsoft Azure avec un [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) OCI le circuit. Cette connectivité est possibles où un emplacement d’homologation ExpressRoute de Azure à proximité ou dans le même emplacement d’homologation en tant que l’interface OCI FastConnect. Cette configuration permet une connectivité sécurisée et rapide entre les deux clouds sans avoir besoin d’un fournisseur de services intermédiaires.

À l’aide d’ExpressRoute et FastConnect, les clients peuvent homologuer un réseau virtuel dans Azure avec un réseau virtuel cloud dans OCI, autant que l’espace d’adressage IP privé ne se chevauche pas. L’homologation les deux réseaux permet à une ressource dans le réseau virtuel pour communiquer avec une ressource dans le réseau virtuel cloud OCI comme si elles sont toutes deux dans le même réseau.

## <a name="network-security"></a>Sécurité du réseau

Sécurité réseau est un élément essentiel des applications d’entreprise et est le cœur de cette solution multicloud. Tout le trafic entrant sur ExpressRoute et FastConnect passe sur un réseau privé. Cette configuration permet de sécuriser la communication entre un réseau virtuel Azure et un réseau virtuel cloud de Oracle. Vous n’avez pas besoin de fournir une adresse IP publique à toutes les machines virtuelles dans Azure. De même, vous n’avez pas besoin dans l’interface OCI passerelle internet. Toutes les communications transitent par le biais de l’adresse IP privée des machines.

En outre, vous pouvez configurer [les listes de sécurité](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) sur vos règles de sécurité réseau et de cloud virtuel OCI (attaché à Azure [groupes de sécurité réseau](../../../virtual-network/security-overview.md)). Ces règles permettent de contrôler le trafic qui transite entre des ordinateurs dans les réseaux virtuels. Règles de sécurité réseau peuvent être ajoutés au niveau de l’ordinateur, à un niveau de sous-réseau, ainsi qu’au niveau du réseau virtuel.
 
## <a name="identity"></a>Identité

Identité est un des principaux piliers du partenariat entre Microsoft et Oracle. Un travail considérable a été fait pour intégrer [Oracle identité Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) avec [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Azure AD est identités et des accès gestion des services de cloud computing de Microsoft. Il aide vos utilisateurs à se connecter et accéder aux diverses ressources. Azure AD vous permet également de gérer vos utilisateurs et leurs autorisations.

Actuellement, cette intégration vous permet de gérer dans un emplacement central, ce qui est Azure Active Directory. Azure AD synchronise les modifications dans le répertoire avec le répertoire Oracle correspondant et est utilisé pour l’authentification unique pour le cloud solutions Oracle.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations et des livres blancs sur OCI, consultez le [du Cloud Oracle](https://docs.cloud.oracle.com/iaas/Content/home.htm) documentation.
