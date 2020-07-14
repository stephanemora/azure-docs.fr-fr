---
title: 'Scénario : Router vers des réseaux virtuels de services partagés'
titleSuffix: Azure Virtual WAN
description: 'Scénarios de routage : configurez des routes pour accéder à un réseau virtuel de service partagé avec une charge de travail à laquelle vous souhaitez que chaque réseau virtuel et chaque branche aient accès.'
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 64bb4c85399f811c0ab7ff84b297b64734efc491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567899"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Scénario : Router vers des réseaux virtuels de services partagés

Lorsque vous travaillez avec le routage de hub virtuel Virtual WAN, il existe un certain nombre de scénarios disponibles. Dans ce scénario, l’objectif est de configurer des routes pour accéder à un réseau virtuel de **service partagé** avec une charge de travail à laquelle vous souhaitez que chaque réseau virtuel et chaque branche (VPN/ER/P2S) aient accès. Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Workflow du scénario

Pour configurer ce scénario, prenez en compte les étapes suivantes :

1. Identifiez le réseau virtuel des **services partagés**.
2. Créez une table de routage personnalisée. Dans l’exemple, cette table de routage est nommée **RT_SHARED**. Pour connaître la procédure de création d’une table de routage, consultez le [Guide pratique pour configurer le routage de hub virtuel](how-to-virtual-hub-routing.md). Utilisez les valeurs suivantes comme repère :

   * **Association**
     * Pour les **réseaux virtuels, *à l’exception* du réseau virtuel de services partagés**, sélectionnez les réseaux virtuels à isoler. Cela implique que tous ces réseaux virtuels (à l’exception du réseau virtuel de services partagés) sont en mesure d’atteindre la destination en fonction des routes de la table de routage RT_SHARED.

   * **Propagation**
      * Pour les **branches**, propagez les routes vers cette table de routage, en plus des autres tables de routage que vous avez déjà sélectionnées. Grâce à cette étape, la table de routage RT_SHARED apprend les routes de toutes les connexions de branche (VPN/ER/VPN utilisateur).
      * Pour les **réseaux virtuels**, sélectionnez le **réseau virtuel des services partagés**. Grâce à cette étape, la table de routage RT_SHARED apprend les routes de la connexion au réseau virtuel de services partagés.

     Cela entraîne des modifications de configuration de routage, comme illustré dans la figure ci-dessous.

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Réseau virtuel des services partagés":::

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
* Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).
