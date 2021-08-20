---
title: Connecter ExpressRoute à la passerelle de réseau virtuel
description: Procédure pour connecter ExpressRoute à la passerelle de réseau virtuel.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: a3e759c12496613848f6f0bc8de596a5099b904b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438210"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->


1. Demandez une clé d’autorisation ExpressRoute :

   [!INCLUDE [request-authorization-key](request-authorization-key.md)]

1. Accédez à la passerelle de réseau virtuel que vous prévoyez d’utiliser et sélectionnez **Connexions**  >  **+ Ajouter**.

1. Dans la page **Ajouter une connexion**, spécifiez des valeurs pour les champs, puis sélectionnez **OK**. 

   | Champ | Valeur |
   | --- | --- |
   | **Nom**  | Attribuez un nom à cette connexion.  |
   | **Type de connexion**  | Sélectionnez **ExpressRoute**.  |
   | **Utiliser l’autorisation**  | Vérifiez que cette case est sélectionnée.  |
   | **Passerelle de réseau virtuel** | Passerelle de réseau virtuel que vous avez l’intention d’utiliser.  |
   | **Clé d’autorisation**  | Collez la clé d’autorisation que vous avez copiée précédemment. |
   | **URI du circuit pair**  | Collez l’ID ExpressRoute que vous avez copié précédemment.  |

   :::image type="content" source="../media/tutorial-configure-networking/add-connection.png" alt-text="Capture d’écran montrant la page Ajouter une connexion qui permet de connecter ExpressRoute à la passerelle de réseau virtuel.":::

Ceci crée la connexion entre votre circuit ExpressRoute et votre réseau virtuel.

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="Capture d’écran montrant une connexion réussie à la passerelle de réseau virtuel.":::