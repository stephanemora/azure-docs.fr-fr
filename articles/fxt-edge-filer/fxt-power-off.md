---
title: Comment arrêter une unité de serveur Microsoft Azure FXT Edge Filer
description: Procédures sécurisées de démarrage et d’arrêt d’un nœud Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 92364de82bc3de8229eced4ee02997a27afbde45
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506410"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Comment mettre hors tension en toute sécurité le matériel Azure FXT Edge Filer

Bien que vous puissiez utiliser le bouton d’alimentation physique pour basculer vers un nœud individuel, vous ne devez pas l’utiliser pour arrêter l’unité dans des circonstances normales.

Dès lors qu’un nœud Azure FXT Edge Filer est activé sur un cluster, vous devez utiliser le logiciel du Panneau de configuration du cluster pour arrêter le matériel. 

> [!NOTE] 
> Pour éviter toute perte ou altération de données, vous devez toujours utiliser le logiciel du Panneau de configuration pour arrêter Azure FXT Edge Filer. N’utilisez pas le bouton d’alimentation physique pour la procédure d’arrêt, sauf si vous êtes invité à le faire par le Support technique et le Service clientèle Microsoft.
> 
> En cas d’urgence électrique, débranchez les câbles d’alimentation ou utilisez le mécanisme de déconnexion électronique de votre centre de données.

## <a name="shut-down-a-node-from-the-control-panel"></a>Arrêter un nœud à partir du Panneau de configuration

Suivez ces instructions pour mettre hors tension en toute sécurité un nœud Azure FXT Edge Filer :

1. Connectez-vous au Panneau de configuration du cluster (voir instructions dans [Ouvrir les pages de paramètres](fxt-cluster-create.md#open-the-settings-pages)).
1. Cliquez sur l’onglet **Paramètres**, puis chargez la page **Cluster** > **Nœuds FXT**.
1. Dans la liste des nœuds de cluster, recherchez celui que vous souhaitez arrêter. Cliquez sur le bouton **Hors tension** situé dans la colonne **Actions**. 
1. Patientez quelques minutes. Le nœud s’arrête et se met hors tension.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les voyants d’état et les autres indicateurs, voir [Superviser l’état du matériel Azure FXT Edge Filer](fxt-monitor.md).
* Pour en savoir plus sur l’alimentation d’Azure FXT Edge Filer, voir [Brancher les câbles d’alimentation](fxt-network-power.md#connect-power-cables).
