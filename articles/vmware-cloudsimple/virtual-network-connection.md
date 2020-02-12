---
title: Connecter un réseau virtuel Azure à AVS à l’aide d’ExpressRoute
description: Décrit comment obtenir des informations de peering permettant de connecter le réseau virtuel Azure à votre environnement AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1ff11bbafe6f9057ce70c799e0437691d89ccb40
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014400"
---
# <a name="connect-azure-virtual-network-to-avs-using-expressroute"></a>Connecter un réseau virtuel Azure à AVS à l’aide d’ExpressRoute

Vous pouvez étendre le réseau de votre cloud privé AVS à votre réseau virtuel et vos ressources Azure. Une connexion ExpressRoute vous permet d’accéder aux ressources s’exécutant dans votre abonnement Azure à partir de votre cloud privé AVS.

## <a name="request-authorization-key"></a>Demande de clé d’autorisation

Une clé d’autorisation est requise pour connecter ExpressRoute à votre cloud privé AVS et à votre réseau virtuel Azure. Pour obtenir une clé, envoyer un ticket avec <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">Support</a>. Utilisez les informations suivantes dans votre demande :

* Type de problème : **Technique**
* Abonnement : **Sélectionnez l’abonnement dans lequel le service AVS est déployé**
* Service : **VMware Solutions (AVS)**
* Type de problème : **Demande de service**
* Sous-type de problème : **Clé d’autorisation pour la connexion au réseau virtuel Azure**
* Objet : **Demande de clé d’autorisation pour la connexion au réseau virtuel Azure**

## <a name="get-peering-information-from-avs-portal"></a>Recevoir des informations de peering à partir du portail AVS

Pour configurer la connexion, vous devez établir une connexion entre le réseau virtuel Azure et votre environnement AVS. Dans le cadre de la procédure, vous devez fournir l’URI du circuit pair et la clé d’autorisation. Obtenez l’URI et la clé d'autorisation à partir du [portail AVS](access-cloudsimple-portal.md). Sélectionnez **Réseau** dans le menu latéral, puis **Connexion réseau Azure**. Ou sélectionnez **Compte** dans le menu latéral, puis **Connexion réseau Azure**.

Copiez l’URI du circuit pair et la clé d’autorisation pour chacune des régions à l’aide de l’icône de *copie*. Pour chaque région AVS que vous souhaitez connecter :

1. Cliquez sur **Copier** pour copier l’URI. Collez-le dans un fichier qui permettra de l’ajouter au portail Azure. 
2. Cliquez sur **Copier** pour copier la clé d’autorisation et collez-la également dans le fichier.

Copiez la clé d’autorisation et l’URI du circuit pair qui se trouve à l’état **Disponible**. L’état **Utilisé** indique que la clé a déjà été utilisée pour créer une connexion de réseau virtuel.

![Page de connexion de réseau virtuel](media/virtual-network-connection.png)

Pour plus d’informations sur la configuration du réseau virtuel Azure au lien AVS, consultez la page [Connecter votre environnement de cloud privé AVS au réseau virtuel Azure à l’aide d’ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Étapes suivantes

* [Connexion entre un réseau virtuel Azure et le cloud privé AVS](azure-expressroute-connection.md)
* [Se connecter à un réseau local à l’aide d’Azure ExpressRoute](on-premises-connection.md)
