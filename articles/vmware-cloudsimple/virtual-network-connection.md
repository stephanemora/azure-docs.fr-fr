---
title: Connecter un réseau virtuel Azure à CloudSimple à l’aide d’ExpressRoute
description: Indique comment obtenir des informations de peering permettant de connecter le réseau virtuel Azure à votre environnement CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00d49d763dedc5d86557dadd10f5d727e7893dbe
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563061"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Connecter un réseau virtuel Azure à CloudSimple à l’aide d’ExpressRoute

Vous pouvez étendre le réseau de votre cloud privé à votre réseau virtuel et vos ressources Azure. Vous pouvez utiliser la connexion ExpressRoute pour accéder aux ressources s’exécutant dans votre abonnement Azure à partir de votre cloud privé.

## <a name="request-authorization-key"></a>Demande de clé d’autorisation

Une clé d’autorisation est requise pour connecter ExpressRoute à votre cloud privé et à votre réseau virtuel Azure. Pour obtenir une clé, envoyer un ticket avec <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">Support</a>.  Utilisez les informations suivantes dans votre demande :

* Type de problème : **Technique**
* Abonnement : **Sélectionnez l’abonnement dans lequel le service CloudSimple est déployé**
* Service : **Solution VMware de CloudSimple**
* Type de problème : **Demande de service**
* Sous-type de problème : **Clé d’autorisation pour la connexion au réseau virtuel Azure**
* Objet : **Demande de clé d’autorisation pour la connexion au réseau virtuel Azure**

## <a name="obtain-peering-information-for-azure-virtual-network-to-cloudsimple-connection"></a>Obtenir des informations de peering pour la connexion entre le réseau virtuel Azure et CloudSimple

Pour configurer la connexion, vous devez établir un lien entre le réseau virtuel Azure et votre environnement CloudSimple.  Dans le cadre de la procédure, vous devez fournir l’URI du circuit pair et la clé d’autorisation. Obtenez l’URI et la clé d'autorisation à partir du [portail CloudSimple](access-cloudsimple-portal.md).  Sélectionnez **Réseau** dans le menu latéral, puis **Connexion réseau Azure**. Ou sélectionnez **Compte** dans le menu latéral, puis **Connexion réseau Azure**.

Notez les icônes de copie pour l'URI du circuit pair et la clé d'autorisation pour chacune des régions. Pour chaque cloud privé que vous souhaitez connecter :

* Cliquez sur **Copier** pour copier l’URI. Collez-le dans un fichier qui permettra de l’ajouter au portail Azure.  
* Cliquez sur **Copier** pour copier la clé d’autorisation et collez-la également dans le fichier.

![Page de connexion de réseau virtuel](media/network-virt-conn-page.png)

Pour plus d’informations sur la configuration du réseau virtuel Azure au lien CloudSimple, consultez la page [Connecter votre environnement de cloud privé CloudSimple au réseau virtuel Azure à l’aide d’ExpressRoute](azure-expressroute-connection.md).
