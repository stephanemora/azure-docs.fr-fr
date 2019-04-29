---
title: Fichier Include
description: Fichier Include
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: d20ef44fd5c117e4e3a568542bb022c451ac23fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743260"
---
## <a name="scenario"></a>Scénario
Ce document décrit pas à pas un déploiement qui utilise plusieurs cartes d’interface réseau dans des machines virtuelles dans le cadre d’un scénario spécifique. Dans ce scénario, vous avez une charge de travail IaaS à deux niveaux hébergée dans Azure. Chaque niveau est déployé dans son propre sous-réseau dans un réseau virtuel (VNet). Le niveau frontal est composé de plusieurs serveurs web, regroupés dans un équilibreur de charge configuré pour la haute disponibilité. Le niveau principal est constitué de plusieurs serveurs de base de données. Ces serveurs de base de données sont déployés avec deux cartes d’interface réseau chacun, l’une pour l’accès à la base de données et l’autre pour la gestion. Le scénario inclut également les groupes de sécurité réseau (NSG) pour contrôler le trafic autorisé sur chaque sous-réseau, ainsi que la carte d’interface réseau dans le déploiement. La figure ci-après illustre l’architecture de base de ce scénario :

![Scénario à cartes d’interface réseau multiples](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)