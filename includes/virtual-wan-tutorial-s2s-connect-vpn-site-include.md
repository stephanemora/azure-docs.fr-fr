---
title: Fichier Include
description: Fichier Include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bcc4af837cc28a18345e49cd39ddf88d24e44dd8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489014"
---
1. Sélectionnez **Connecter des sites VPN** pour ouvrir la page **Connecter des sites**.

    ![connecterer](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "connecter")

   Renseignez les champs suivants :

   * Entrez une clé prépartagée. Si vous n’entrez pas de clé, Azure en génère automatiquement une pour vous.
   * Sélectionnez les paramètres Protocole et IPsec. Reportez-vous aux détails IPSec par défaut/personnalisés (placer le lien vers la page)
   * Sélectionnez l’option appropriée pour **Propager l’itinéraire par défaut**. L’option **Activer** permet au hub virtuel de propager un itinéraire par défaut appris à cette connexion. Cet indicateur active la propagation de l’itinéraire par défaut à une connexion uniquement si l’itinéraire par défaut est déjà appris par le hub Virtual WAN suite au déploiement d’un pare-feu dans le hub, ou si le tunneling forcé est activé sur un autre site connecté. L’itinéraire par défaut ne provient pas du hub Virtual WAN.

2. Sélectionnez **Connecter**.
3. En quelques minutes, le site affiche la connexion et l’état de la connectivité.

   ![statut](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **État de la connexion :** Il s’agit de l’état de la ressource Azure pour la connexion qui connecte le site VPN à la passerelle VPN du hub Azure. Une fois cette opération de plan de contrôle réussie, la passerelle VPN Azure et le périphérique VPN local continuent d’établir la connectivité.

   **État de la connectivité :** Il s’agit de l’état de connectivité réel (chemin d’accès aux données) entre la passerelle VPN d’Azure dans le hub et le site VPN. Il peut afficher l’un des états suivants :

    * **Inconnue** : Cet état est généralement observé si les systèmes principaux travaillent à la transition vers un autre état.
    * **Connexion en cours** : La passerelle VPN Azure tente d’accéder au site VPN local réel.
    * **Connecté** : La connectivité est établie entre la passerelle VPN Azure et le site VPN local.
    * **Déconnecté** : Cet état s’affiche si, pour une raison quelconque (localement ou dans Azure), la connexion a été déconnectée.
4. Au sein d’un site VPN hub, vous pouvez également effectuer les opérations suivantes : 

   * Modifier ou supprimer la connexion VPN.
   * Supprimer le site dans le Portail Azure.
   * Télécharger une configuration spécifique à une branche pour plus d’informations sur la partie Azure à l’aide du menu contextuel (…) à côté du site. Si vous souhaitez télécharger la configuration de tous les sites connectés dans votre hub, sélectionnez **Télécharger la configuration VPN** dans le menu supérieur.