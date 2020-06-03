---
title: Applications Azure Oracle WebLogic Server | Microsoft Docs
description: Découvrez comment exécuter Oracle WebLogic Server sur Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: 9ba4e3a66ae8ef71b39aa26fd54048381237c2fa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664047"
---
# <a name="oracle-weblogic-server-azure-applications"></a>Applications Azure Oracle WebLogic Server

## <a name="oracle-weblogic-server-is-a-scalable-enterprise-ready-java-ee-application-server"></a>Oracle WebLogic Server est un serveur d’applications Java EE évolutif et adapté à l’entreprise.

Oracle WebLogic Server est le premier serveur d’applications de plateforme Java natif dans le cloud au monde, conçu pour le développement et le déploiement d’applications d’entreprise distribuées à plusieurs niveaux. Les offres Azure WebLogic Server vous permettent d’adopter le cloud computing en fournissant un meilleur choix et une plus grande flexibilité pour la migration de WebLogic, y compris le lift-and-shift de vos applications Java EE vers le Cloud Azure, avec l’effort le plus petit et l’impact le plus important. Les offres vous permettent de démarrer rapidement vos applications métier en approvisionnant automatiquement les ressources de réseau virtuel, de stockage et Linux, en installant WebLogic Server, en configurant la sécurité avec un groupe de sécurité réseau, l’équilibrage de charge avec Azure App Gateway, l’authentification avec Azure Active Directory et en facilitant la connectivité de la base de données.

Quatre offres sont disponibles pour répondre à différents scénarios : nœud unique sans serveur d’administration, nœud unique avec un serveur d’administration, cluster et cluster dynamique.  N’hésitez pas à les essayer, les offres sont disponibles gratuitement.

_Ces offres sont des BYOL (apportez votre propre licence)_ . Elles supposent que vous avez déjà acheté les licences appropriées avec Oracle et que vous disposez d’une licence adéquate pour exécuter des offres dans Microsoft Azure.

_Si vous souhaitez travailler étroitement sur vos scénarios de migration avec l’équipe d’ingénierie développant ces offres, il vous suffit de cliquer sur le bouton [ME CONTACTER](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)_ dans la [Place de marché Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Les responsables de programme, les architectes et les ingénieurs reviendront rapidement vers vous pour lancer la collaboration.

### <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server avec nœud unique

Cette offre approvisionne une seule machine virtuelle et y installe Oracle WebLogic Server. Elle ne crée pas de domaine ou ne démarre pas le serveur d’administration. Cela est utile pour les scénarios avec une configuration de domaine hautement personnalisée.

### <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server avec serveur d’administration

Cette offre approvisionne une seule machine virtuelle et y installe Oracle WebLogic Server. Elle crée un domaine et démarre le serveur d’administration, ce qui vous permet de gérer le domaine.

### <a name="oracle-weblogic-server-cluster"></a>Cluster Oracle WebLogic Server

Cette offre crée un cluster hautement disponible des machines virtuelles Oracle WebLogic Server. Le serveur d’administration et tous les serveurs gérés sont démarrés par défaut, ce qui vous permet de gérer le domaine.

### <a name="oracle-weblogic-server-dynamic-cluster"></a>Cluster dynamique Oracle WebLogic Server

Cette offre crée un cluster dynamique hautement disponible et évolutif de machines virtuelles Oracle WebLogic Server. Le serveur d’administration et tous les serveurs gérés sont démarrés par défaut, ce qui vous permet de gérer le domaine.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les offres sur la Place de marché Azure.

> [!div class="nextstepaction"]
> [Oracle WebLogic Server avec nœud unique](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server avec serveur d’administration](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Cluster Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Cluster dynamique Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
