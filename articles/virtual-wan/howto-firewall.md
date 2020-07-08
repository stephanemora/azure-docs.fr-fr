---
title: Installer un Pare-feu Azure dans un hub de réseau étendu virtuel
titleSuffix: Azure Virtual WAN
description: Procédure de configuration du Pare-feu Azure dans un hub de réseau étendu virtuel
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 05/21/2020
ms.author: cherylmc
ms.openlocfilehash: f20ed76a72eecce59a7b8795a42b033230a2f7e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753688"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Configurer un Pare-feu Azure dans un hub de réseau étendu virtuel

Un **hub sécurisé** est un hub Azure Virtual WAN doté d’un Pare-feu Azure. Cet article vous guide tout au long des étapes de conversion d’un hub de réseau étendu virtuel en hub sécurisé en installant le Pare-feu Azure directement à partir des pages du portail Azure Virtual WAN.

## <a name="before-you-begin"></a>Avant de commencer

Les étapes décrites dans cet article supposent que vous avez déjà déployé un réseau étendu virtuel avec un ou plusieurs hubs.

Pour créer un nouveau réseau étendu virtuel et un nouveau hub, suivez les étapes décrites dans les articles suivants :

* [Créer un réseau étendu virtuel](virtual-wan-site-to-site-portal.md#openvwan)
* [Créer un hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Afficher les hubs virtuels

La page **Vue d’ensemble** de votre réseau étendu virtuel affiche une liste des hubs virtuels et de hubs sécurisés. L’illustration suivante montre un réseau étendu virtuel sans hub sécurisé.

[ ![vue d’ensemble](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>Convertir en hub sécurisé

1. Sur la page **Vue d’ensemble** de votre réseau étendu virtuel, sélectionnez le hub que vous souhaitez convertir en hub sécurisé. Sur la page du hub virtuel, vous voyez deux options pour déployer le Pare-feu Azure dans ce hub. Sélectionnez l’une ou l’autre de ces options.

   [ ![sécurité](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. Après avoir sélectionné l’une des options, vous voyez la page **Convertir en hub sécurisé**. Sélectionnez un hub à convertir, puis sélectionnez **suivant : Pare-feu Azure** en bas de la page.

   [ ![sélectionner un hub](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. Une fois le flux de travail terminé, sélectionnez **Confirmer**.

   [ ![confirmer](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. Une fois le hub converti en hub sécurisé, vous pouvez l’afficher sur la page **Vue d’ensemble** du réseau étendu virtuel.

   [ ![afficher le hub sécurisé](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>Afficher les ressources d’un hub

Dans la page **Vue d’ensemble** du réseau étendu virtuel, sélectionnez le hub sécurisé. Dans la page du hub, vous pouvez afficher toutes les ressources du hub virtuel, dont le Pare-feu Azure.

[ ![afficher les ressources du hub](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

Pour afficher les paramètres du Pare-feu Azure à partir du hub sécurisé, sous **Sécurité**, sélectionnez **Paramètres du hub virtuel sécurisé**.
[ ![afficher les paramètres du hub](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>Configurer des paramètres supplémentaires

Pour configurer des paramètres de Pare-feu Azure supplémentaires pour le hub virtuel, sélectionnez le lien vers **Azure Firewall Manager**. Pour plus d’informations sur les stratégies de pare-feu, consultez [Azure Firewall Manager](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub).

[ ![paramètres supplémentaires](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

Pour revenir à la page **Vue d’ensemble** du hub, vous pouvez revenir en arrière en cliquant sur le chemin d’accès, comme indiqué par la flèche dans l’illustration suivante.

[ ![revenir à la vue d’ensemble](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
