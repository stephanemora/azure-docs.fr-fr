---
title: Configurer la préférence de routage réseau
titleSuffix: Azure Storage
description: Configurez une préférence de routage réseau pour votre compte de stockage Azure afin de spécifier comment le trafic réseau est acheminé vers votre compte à partir de clients sur Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/21/2020
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fb427de170764e5cd1fca57f9fb2d1410829e521
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743033"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Configuration d’une préférence de routage réseau pour le Stockage Azure

Cet article explique comment configurer la préférence de routage réseau et les points de terminaison propres à l’itinéraire pour un compte de stockage. 

La préférence de routage réseau spécifie la façon dont le trafic réseau est acheminé vers votre compte à partir de clients sur Internet. Les points de terminaison propres à l’itinéraire constituent de nouveaux points de terminaison créés par le Stockage Azure pour le compte de stockage. Ils acheminent le trafic sur l’itinéraire souhaité sans modifier vos préférences de routage par défaut. Pour plus d’informations, consultez [Configuration d’une préférence de routage réseau pour le Stockage Azure](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Configuration de la préférence de routage pour le point de terminaison public par défaut

Par défaut, la préférence de routage pour le point de terminaison public du compte de stockage est définie sur le réseau global Microsoft. Vous pouvez choisir entre le routage réseau Microsoft mondial et le routage Internet comme préférence de routage par défaut pour le point de terminaison public de votre compte de stockage. Pour plus d’informations sur la différence entre ces deux types de routage, consultez [Préférence de routage réseau pour le Stockage Azure](network-routing-preference.md). 

Pour faire du routage Internet votre préférence de routage, procédez comme suit :

1. Accédez à votre compte de stockage dans le portail.

2. Sous **Paramètres**, choisissez **Mise en réseau**.

    > [!div class="mx-imgBorder"]
    > ![Option de menu Mise en réseau](./media/configure-network-routing-preference/networking-option.png)

3.  Dans l’onglet **Pare-feu et réseaux virtuels**, sous **Routage réseau**, changez le paramètre **Préférence de routage** en **Routage Internet**.

4.  Cliquez sur **Enregistrer**.

    > [!div class="mx-imgBorder"]
    > ![Option de routage Internet](./media/configure-network-routing-preference/internet-routing-option.png)

## <a name="configure-a-route-specific-endpoint"></a>Configuration d’un point de terminaison propre à l’itinéraire

Vous pouvez également configurer un point de terminaison propre à l’itinéraire. Par exemple, vous pouvez définir la préférence de routage du point de terminaison par défaut sur *Routage Internet*, puis publier un point de terminaison propre à l’itinéraire qui autorise le trafic entre les clients sur Internet et votre compte de stockage à être acheminé sur le réseau global Microsoft.

1.  Accédez à votre compte de stockage dans le portail.

2.  Sous **Paramètres**, choisissez **Mise en réseau**.

3.  Dans l’onglet **Pare-feu et réseaux virtuels**, sous **Publier des points de terminaison propres à l’itinéraire**, choisissez la préférence de routage de votre point de terminaison propre à l’itinéraire, puis cliquez sur **Enregistrer**. Cette préférence n’a une incidence que sur le point de terminaison propre à l’itinéraire. Elle n’affecte pas la préférence de routage par défaut.  

    L’illustration suivante montre l’option **Routage réseau Microsoft** sélectionnée.

    > [!div class="mx-imgBorder"]
    > ![Option Routage réseau Microsoft](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Identification du nom d’un point de terminaison propre à l’itinéraire

Si vous avez configuré un point de terminaison propre à l’itinéraire, vous le trouverez dans les propriétés de votre compte de stockage.

1.  Sous **Paramètres**, choisissez **Propriétés**.

    > [!div class="mx-imgBorder"]
    > ![Option de menu Propriétés](./media/configure-network-routing-preference/properties.png)

2.  Le point de terminaison **Routage réseau Microsoft** est affiché pour chacun des services qui prennent en charge les préférences de routage. Cette image montre le point de terminaison des services Blob et Fichier.

    > [!div class="mx-imgBorder"]
    > ![Option de routage réseau Microsoft pour les points de terminaison propres à l’itinéraire](./media/configure-network-routing-preference/routing-url.png)


## <a name="see-also"></a>Voir aussi

- [Préférence de routage réseau](network-routing-preference.md)
- [Configurer des pare-feu et des réseaux virtuels dans Stockage Azure](storage-network-security.md)
- [Recommandations de sécurité pour Stockage Blob](../blobs/security-recommendations.md)
