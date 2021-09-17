---
ms.author: cherylmc
author: cherylmc
ms.date: 08/18/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 53c6980ea6e5d868a842b1e14031f27ff51f655a
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122638271"
---
1. Accédez à votre **HUB virtuel -> VPN (point à site)** .

1. Vous devrez peut-être cliquer sur **Association de hub : Connecté à ce hub** afin d’effacer les filtres et de visualiser vos sites.

1. Cochez la case du site que vous voulez connecter, puis cliquez sur **Connecter les sites VPN**.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connect-site.png" alt-text="Capture d’écran montrant la connexion au site.":::

1. Dans la page **Connecter des sites**, configurez les paramètres nécessaires.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Capture d’écran montrant le volet Sites connectés pour le hub virtuel, prêt pour une clé prépartagée et les paramètres associés.":::

   * **Clé pré-partagée (PSK)** : Entrez la clé pré-partagée utilisée par votre périphérique VPN. Si vous n’entrez pas de clé, Azure en génère automatiquement une pour vous. Vous utiliserez ensuite cette clé lors de la configuration de votre périphérique VPN.
   * **Protocole et IPsec** : Vous pouvez soit laisser les paramètres par défaut pour Protocole (IKEv2) et IPsec (par défaut), soit configurer des paramètres personnalisés. Pour plus d’informations, consultez [IPsec par défaut/personnalisé](../articles/virtual-wan/virtual-wan-ipsec.md).
   * **Propager la route par défaut** : Ne modifiez ce paramètre sur **Activer** que si vous savez que vous souhaitez propager la route par défaut. Dans le cas contraire, laissez la valeur sur **Désactiver**. Vous pouvez toujours modifier ce paramètre ultérieurement. 
   
     L’option **Activer** permet au hub virtuel de propager un itinéraire par défaut appris à cette connexion. Cet indicateur active la propagation de l’itinéraire par défaut à une connexion uniquement si l’itinéraire par défaut est déjà appris par le hub Virtual WAN suite au déploiement d’un pare-feu dans le hub, ou si le tunneling forcé est activé sur un autre site connecté. L’itinéraire par défaut ne provient pas du hub Virtual WAN. 
   * **Utiliser le sélecteur de trafic basé sur une stratégie** : laissez ce paramètre sur **Désactiver** sauf si vous configurez une connexion à un périphérique utilisant ce paramètre.

1. En bas de la page, cliquez sur **Connect**.

1. Une fois que vous avez cliqué sur **Connexion**, l’état de la connectivité affiche **Mise à jour**. Une fois la mise à jour effectuée, le site affiche la connexion et l’état de la connectivité.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connectivity-status.png" alt-text="Capture d’écran montrant une connexion de site à site et un état de connectivité." lightbox="./media/virtual-wan-tutorial-connect-vpn-site-include/connectivity-status.png":::

   **État du provisionnement de la connexion** : Il s’agit de l’état de la ressource Azure pour la connexion qui connecte le site VPN à la passerelle VPN du hub Azure. Une fois cette opération de plan de contrôle réussie, la passerelle VPN Azure et le périphérique VPN local continuent d’établir la connectivité.

   **État de la connectivité** : Il s’agit de l’état de connectivité réel (chemin d’accès aux données) entre la passerelle VPN d’Azure dans le hub et le site VPN. Une fois la mise à jour terminée, elle peut afficher l’un des états suivants :

    * **Inconnue** : Cet état est généralement observé si les systèmes principaux travaillent à la transition vers un autre état.
    * **Connexion en cours** : La passerelle VPN Azure tente d’accéder au site VPN local réel.
    * **Connecté** : La connectivité est établie entre la passerelle VPN et le site VPN local.
   * **Non connecté** : la connectivité n’est pas établie.
    * **Déconnecté** : Cet état s’affiche si, pour une raison quelconque (localement ou dans Azure), la connexion a été déconnectée.
1. Si vous souhaitez apporter des modifications à votre site, sélectionnez votre site, puis cliquez sur le menu contextuel **...** .

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/edit.png" alt-text="Capture d’écran montrant la modification, la suppression et le téléchargement." lightbox="./media/virtual-wan-tutorial-connect-vpn-site-include/expand/edit.png":::

   Sur cette page, vous pouvez effectuer les opérations suivantes : 

   * Modifier ou supprimer la connexion VPN.
   * Supprimer la connexion VPN à ce hub.
   * Téléchargez une configuration spécifique à une branche pour plus d’informations sur le site Azure. Si vous souhaitez télécharger le fichier de configuration de tous les sites connectés dans votre hub, sélectionnez plutôt **Télécharger la configuration VPN** dans le menu au sommet de la page.
