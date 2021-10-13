---
title: Connecter ExpressRoute à la passerelle de réseau virtuel
description: Procédure pour connecter ExpressRoute à la passerelle de réseau virtuel.
ms.topic: include
ms.date: 12/08/2020
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 721a298deb1c3122a1781c5803c93492b43a4b59
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638632"
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
