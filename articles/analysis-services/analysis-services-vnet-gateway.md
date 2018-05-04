---
title: Utiliser la passerelle de données locale pour les sources de données de réseau virtuel Azure | Microsoft Docs
description: Découvrez comment configurer un serveur afin d’utiliser une passerelle pour les sources de données sur le réseau virtuel.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 61c4463060016ecbd319b027d48c87f3a6a8ad48
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
---
# <a name="use-gateway-for-datasources-on-an-azure-virtual-network-vnet"></a>Utiliser la passerelle pour les sources de données sur un réseau virtuel Azure (VNET)

Cet article décrit le **AlwaysUseGateway** propriété de serveur à utiliser lorsque les sources de données se trouvent sur un [réseau virtuel Azure (VNET)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-datasources"></a>Serveur d’accès aux sources de données du réseau virtuel

Si vos sources de données sont accessibles via un réseau virtuel, votre serveur Azure Analysis Services doit se connecter à ces sources de données comme si elles étaient accessibles en local, dans votre propre environnement. Vous pouvez configurer la propriété du serveur **AlwaysUseGateway** pour indiquer le serveur d’accès à toutes ls données d’une source de données via une [passerelle locale](analysis-services-gateway.md). 

> [!NOTE]
> Cette propriété est effective uniquement lorsqu’une [passerelle de données locale](analysis-services-gateway.md) est installée et configurée. La passerelle peut se trouver sur le réseau virtuel.

## <a name="configure-alwaysusegateway-property"></a>Configurer la propriété AlwaysUseGateway

1. Dans SSMS > serveur > **Propriétés** > **Général**, sélectionnez **Afficher les propriétés avancées (toutes)**.
2. Dans **ASPaaS\AlwaysUseGateway**, sélectionnez **true**.

    ![Toujours utiliser la propriété de la passerelle](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Voir aussi
[Connexion à des sources de données locales](analysis-services-gateway.md)   
[Installer et configurer une passerelle de données locale](analysis-services-gateway-install.md)   
[Réseau virtuel Azure (VNET)](../virtual-network/virtual-networks-overview.md)   

