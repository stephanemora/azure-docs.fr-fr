---
title: Connecter ExpressRoute à la passerelle de réseau virtuel
description: Procédure pour connecter ExpressRoute à la passerelle de réseau virtuel.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 6e2e3748dbfd8d69b53dcc4c3a09809756ac48dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103494350"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Accédez au cloud privé que vous avez créé dans le tutoriel [Déployer un cluster vSphere dans Azure](../tutorial-create-private-cloud.md). Sélectionnez **Connectivité** sous **Gérer**, puis sélectionnez l’onglet **ExpressRoute**.

1. Copiez la clé d’autorisation. S’il n’existe pas de clé d’autorisation, vous devez en créer une. Sélectionnez **+ Demander une clé d’autorisation**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Copiez la clé d’autorisation. S’il n’existe pas de clé d’autorisation, vous devez en créer une. Sélectionnez + Demander une clé d’autorisation." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Accédez à la passerelle de réseau virtuel que vous avez créée à l’étape précédente et, sous **Paramètres**, sélectionnez **Connexions**. Dans la page **Connexions**, sélectionnez **+ Ajouter**.

1. Dans la page **Ajouter une connexion**, spécifiez des valeurs pour les champs, puis sélectionnez **OK**. 

   | Champ | Valeur |
   | --- | --- |
   | **Nom**  | Attribuez un nom à cette connexion.  |
   | **Type de connexion**  | Sélectionnez **ExpressRoute**.  |
   | **Utiliser l’autorisation**  | Vérifiez que cette case est sélectionnée.  |
   | **Passerelle de réseau virtuel** | Passerelle de réseau virtuel que vous avez créée précédemment.  |
   | **Clé d’autorisation**  | Copiez et collez la clé d’autorisation à partir de l’onglet ExpressRoute pour votre groupe de ressources. |
   | **URI du circuit pair**  | Copiez et collez l’ID ExpressRoute à partir de l’onglet ExpressRoute pour votre groupe de ressources.  |

   :::image type="content" source="../media/expressroute-global-reach/expressroute-add-connection.png" alt-text="Capture d’écran de la page Ajouter une connexion qui permet de connecter ExpressRoute à la passerelle de réseau virtuel":::

Ceci crée la connexion entre votre circuit ExpressRoute et votre réseau virtuel.

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="Capture d’écran des connexions de passerelle de réseau virtuel":::