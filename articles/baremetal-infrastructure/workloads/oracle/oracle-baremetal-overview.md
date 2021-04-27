---
title: Qu’est-ce que BareMetal Infrastructure pour Oracle ?
description: Découvrez les fonctionnalités de BareMetal Infrastructure pour les charges de travail Oracle.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: efb7506a0fa98ca93390bfe7d3cb1401cdbb77e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558624"
---
# <a name="what-is-baremetal-infrastructure-for-oracle"></a>Qu’est-ce que BareMetal Infrastructure pour Oracle ?

Cet article offre une vue d’ensemble des fonctionnalités de BareMetal Infrastructure pour les charges de travail Oracle.

BareMetal Infrastructure pour Oracle est basé sur le système de calcul unifié (UCS) certifié par Oracle et FlexPod. La plateforme FlexPod fournit des technologies de stockage, de mise en réseau et de serveur prévalidées. Elle offre un stockage NFS, en fournissant une intégration à l’aide du protocole DirectNFS. Les serveurs BareMetal sont dédiés, sans hyperviseur sur les instances BareMetal. 

Ces instances sont destinées à l’exécution d’applications critiques nécessitant une charge de travail Oracle. Les instances BareMetal fournissent une faible latence (0,35 ms) à vos applications qui s’exécutent sur des machines virtuelles Azure. BareMetal fournit un disque de stockage partagé et prend en charge la multi-diffusion requise pour la communication entre nœuds avec un réseau d’interconnexion privé dédié. 

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
