---
title: Utiliser la passerelle de données locale pour les sources de données de réseau virtuel Azure | Microsoft Docs
description: Découvrez comment configurer un serveur afin d’utiliser une passerelle pour les sources de données sur un réseau virtuel.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 675d8ecd3d6a3310a9b102df37df18bed02df3de
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70958804"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Utiliser la passerelle pour les sources de données sur un réseau virtuel Azure (VNet)

Cet article décrit le **AlwaysUseGateway** propriété de serveur à utiliser lorsque les sources de données se trouvent sur un [réseau virtuel Azure (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Serveur d’accès aux sources de données du réseau virtuel

Si vos sources de données sont accessibles via un réseau virtuel, votre serveur Azure Analysis Services doit se connecter à ces sources de données comme si elles étaient accessibles en local, dans votre propre environnement. Vous pouvez configurer la propriété de serveur **AlwaysUseGateway** pour indiquer le serveur qui accédera à toutes les sources de données via une [passerelle locale](analysis-services-gateway.md). 

Les sources de données Azure SQL Database Managed Instance s’exécutent dans Azure VNet avec une adresse IP privée. Si le point de terminaison public est activé sur l’instance, aucune passerelle n’est requise. Si le point de terminaison public n’est pas activé, une passerelle de données locale est requise et la propriété AlwaysUseGateway doit avoir la valeur true.

> [!NOTE]
> Cette propriété est effective uniquement quand une [passerelle de données locale](analysis-services-gateway.md) est installée et configurée. La passerelle peut se trouver sur le réseau virtuel.

## <a name="configure-alwaysusegateway-property"></a>Configurer la propriété AlwaysUseGateway

1. Dans SSMS > serveur > **Propriétés** > **Général**, sélectionnez **Afficher les propriétés avancées (toutes)** .
2. Dans **ASPaaS\AlwaysUseGateway**, sélectionnez **true**.

    ![Toujours utiliser la propriété de la passerelle](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Voir aussi
[Connexion à des sources de données locales](analysis-services-gateway.md)   
[Installer et configurer une passerelle de données locale](analysis-services-gateway-install.md)   
[Réseau virtuel Azure (VNET)](../virtual-network/virtual-networks-overview.md)   

