---
title: Qu’est-ce qu’Oracle WebLogic Server sur Azure ?
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
ms.openlocfilehash: fe247e75040f658beb94a66176f802993268a7d8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851854"
---
# <a name="what-is-oracle-weblogic-server-on-azure"></a>Qu’est-ce qu’Oracle WebLogic Server sur Azure ?

Cette page décrit les solutions permettant d’exécuter WebLogic Server (WLS) sur des machines virtuelles Azure.  Ces solutions sont développées conjointement par Oracle et Microsoft.

Oracle WebLogic Server est le premier serveur d’applications de plateforme Java natif dans le cloud au monde, conçu pour le développement et le déploiement d’applications d’entreprise distribuées à plusieurs niveaux. Les offres Azure WebLogic Server vous permettent d’adopter le cloud computing.  Vous bénéficiez d’un choix et d’une flexibilité accrus pour la migration de WebLogic, y compris l’élévation et le déplacement de vos applications Java EE vers le cloud Azure.   WLS sur Azure produit un impact important avec peu d’efforts. Les offres vous permettent de lancer rapidement vos applications métier.  Chaque offre approvisionne automatiquement des ressources de réseau virtuel, de stockage et Linux.  Sans aucun effort, WebLogic Server est installé.  WLS sur Azure configure la sécurité avec un groupe de sécurité réseau, l’équilibrage de charge avec Azure Application Gateway, l’authentification avec Azure Active Directory et se connecte automatiquement à votre base de données existante.

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Vous pouvez utiliser le portail Azure pour déployer WebLogic Server sur Azure":::

Quatre offres sont disponibles pour répondre à différents scénarios : nœud unique sans serveur d’administration, nœud unique avec un serveur d’administration, cluster et cluster dynamique.  Essayez les offres, elles sont disponibles gratuitement.

_Ces offres sont des BYOL (apportez votre propre licence)_ . Elles supposent que vous avez déjà obtenu les licences appropriées auprès d’Oracle et que vous disposez d’une licence adéquate pour exécuter des offres dans Microsoft Azure.

_Si vous souhaitez travailler étroitement sur vos scénarios de migration avec l’équipe d’ingénierie développant ces offres, sélectionnez le bouton [ME CONTACTER](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)_ sur [Place de marché Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Les responsables de programme, les architectes et les ingénieurs reviendront rapidement vers vous pour lancer la collaboration.

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server avec nœud unique

Cette offre approvisionne une seule machine virtuelle et y installe WLS. Elle ne crée pas de domaine ni ne démarre le serveur d’administration. Un nœud unique est utile pour les scénarios ayant une configuration de domaine hautement personnalisée.

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server avec serveur d’administration

Cette offre approvisionne une seule machine virtuelle et y installe WLS. Elle crée un domaine et démarre le serveur d’administration, ce qui vous permet de gérer le domaine.

## <a name="oracle-weblogic-server-cluster"></a>Cluster Oracle WebLogic Server

Cette offre crée un cluster hautement disponible de machines virtuelles WLS. Le serveur d’administration et tous les serveurs gérés sont démarrés par défaut, ce qui vous permet de gérer le domaine.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Cluster dynamique Oracle WebLogic Server

Cette offre crée un cluster dynamique évolutif et hautement disponible de machines virtuelles WLS. Le serveur d’administration et tous les serveurs gérés sont démarrés par défaut, ce qui vous permet de gérer le domaine.

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
