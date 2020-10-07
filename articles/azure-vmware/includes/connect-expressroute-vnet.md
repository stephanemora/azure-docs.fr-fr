---
title: Connecter ExpressRoute à la passerelle de réseau virtuel
description: Procédure pour connecter ExpressRoute à la passerelle de réseau virtuel.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 241919e3a69b8d1c3c24e6c894bcbf20aea62d5f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578345"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Accédez au cloud privé que vous avez créé dans le tutoriel précédent et sélectionnez **Connectivité** sous **Gérer**, puis sélectionnez l’onglet **ExpressRoute**.

1. Copiez la clé d’autorisation. S’il n’existe pas de clé d’autorisation, vous devez en créer une. Sélectionnez **+ Demander une clé d’autorisation**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="Copiez la clé d’autorisation. S’il n’existe pas de clé d’autorisation, vous devez en créer une. Sélectionnez + Demander une clé d’autorisation." border="true":::

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

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="Copiez la clé d’autorisation. S’il n’existe pas de clé d’autorisation, vous devez en créer une. Sélectionnez + Demander une clé d’autorisation." border="true":::

Ceci crée la connexion entre votre circuit ExpressRoute et votre réseau virtuel.