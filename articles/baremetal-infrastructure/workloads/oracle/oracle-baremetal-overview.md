---
title: Qu’est-ce que BareMetal Infrastructure pour Oracle ?
description: Découvrez les fonctionnalités de BareMetal Infrastructure pour les charges de travail Oracle.
ms.topic: conceptual
ms.subservice: baremetal-oracle
ms.date: 09/03/2021
ms.openlocfilehash: e74d1a3e3b6cb1b2cd0b99927a57a88117715e7b
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542437"
---
# <a name="what-is-baremetal-infrastructure-for-oracle"></a>Qu’est-ce que BareMetal Infrastructure pour Oracle ?

Dans cet article, nous offrirons une vue d’ensemble des fonctionnalités de BareMetal Infrastructure pour les charges de travail Oracle.

BareMetal Infrastructure pour Oracle est basé sur le système de calcul unifié (UCS) certifié par Oracle et FlexPod. La plateforme FlexPod fournit des technologies de stockage, de mise en réseau et de serveur prévalidées. Il offre un stockage NFS (Network File System) NetApp qui assure l’intégration à l’aide du protocole DirectNFS. Les serveurs BareMetal sont dédiés, sans hyperviseur sur les instances BareMetal. 

Ces instances sont destinées à l’exécution d’applications critiques nécessitant une charge de travail Oracle. Les instances BareMetal fournissent une faible latence (0,35 ms) à vos applications qui s’exécutent sur des machines virtuelles Azure. BareMetal fournit un stockage partagé. La solution prend également en charge la multi-diffusion requise pour la communication entre nœuds avec un réseau d’interconnexion privé dédié. 

Les autres fonctionnalités de BareMetal Infrastructure pour Oracle sont les suivantes :

- Lames UCS certifiées par Oracle : UCSB200-M5, UCSB460-M4, UCSB480-M5
- Communication Oracle Real Application Clusters (RAC) nœud à nœud (multidiffusion) avec un LAN virtuel privé : 40 Gb.
- Matériel géré par Microsoft
  - Stockage redondant, réseau, alimentation, gestion
  - Surveillance de l’infrastructure, des réparations et du remplacement
  - Comprend Azure ExpressRoute sur le contrôleur de domaine du client
  - Sécurité physique et réseau, peut accéder à tous les services cloud Azure

### <a name="supported-protocols"></a>Protocoles pris en charge

Les protocoles suivants sont utilisés pour différents points de montage au sein des serveurs BareMetal pour la charge de travail Oracle.

- Montage du système d’exploitation : iSCSI
- Données/journaux : NFSv3
- Sauvegardes/archivage : NFSv4

### <a name="licensing"></a>Licences

- Vous apportez votre propre système d’exploitation local et vos licences Oracle.

### <a name="operating-system"></a>Système d’exploitation

Les serveurs sont préchargés avec le système d’exploitation RHEL 7.6.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les références SKU pour les charges de travail Oracle BareMetal.

> [!div class="nextstepaction"]
> [Références SKU BareMetal pour les charges de travail Oracle](oracle-baremetal-skus.md)
