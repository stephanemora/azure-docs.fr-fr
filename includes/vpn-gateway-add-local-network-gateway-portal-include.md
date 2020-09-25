---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bc9e18427bb3b8094911d2ac7f285d271ecd1a21
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025251"
---
1. Dans le menu du [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**.

   ![Ressource](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. Dans le champ **Rechercher dans la Place de marché**, tapez **Passerelle réseau local**, puis appuyez sur **Entrée** pour lancer la recherche. Une liste de résultats apparaîtra. Cliquez sur **Passerelle de réseau local**, puis cliquez sur le bouton **Créer** pour ouvrir la page **Créer une passerelle de réseau local**.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway-ip.png" alt-text="Créer une adresse IP de passerelle de réseau local":::

3. Dans la page **Créer une passerelle de réseau local**, spécifiez les valeurs de votre passerelle de réseau local.

   - **Nom :** Spécifiez un nom pour votre objet de passerelle de réseau local.
   - **Point de terminaison :** sélectionnez le type de point de terminaison du périphérique VPN local, à savoir **Adresse IP** ou **Nom de domaine complet (FQDN)** .
      - **Adresse IP** : si une adresse IP publique statique vous a été allouée à partir de votre fournisseur de services Internet pour votre périphérique VPN, sélectionnez l’option Adresse IP et précisez l’adresse IP comme indiqué dans l’exemple. Il s’agit de l’adresse IP publique du périphérique VPN auquel vous souhaitez que la passerelle Azure VPN se connecte. Si vous ne disposez pas de l’adresse IP actuellement, vous pouvez utiliser les valeurs indiquées dans l’exemple. Toutefois, vous devrez revenir en arrière et remplacer votre adresse IP d’espace réservé par l’adresse IP publique de votre périphérique VPN. Dans le cas contraire, Azure ne sera pas en mesure de se connecter.
      - **FQDN** : si vous disposez d’une adresse IP publique dynamique qui peut changer après un certain laps de temps, généralement déterminé par votre fournisseur de services Internet, vous pouvez utiliser un nom DNS constant avec un service DNS dynamique qui pointe vers l’adresse IP publique actuelle de votre périphérique VPN. Votre passerelle VPN Azure résoudra le nom FQDN pour savoir à quelle adresse IP publique se connecter. La capture d’écran ci-dessous montre un exemple d’utilisation du nom FQDN au lieu de l’adresse IP.
   - **Espace d’adressage** fait référence aux plages d’adresses du réseau qui représente ce réseau local. Vous pouvez ajouter plusieurs plages d’espaces d’adressage. Assurez-vous que les plages que vous spécifiez ici ne se chevauchent pas avec les plages d’autres réseaux auxquels vous souhaitez vous connecter. Azure achemine la plage d’adresses que vous spécifiez vers l’adresse IP du périphérique VPN local. *Utilisez ici vos propres valeurs (et non les valeurs indiquées dans l’exemple) si vous voulez vous connecter à votre site local*.
   - **Configurer les paramètres BGP :** À utiliser uniquement durant la configuration de BGP. pour la configuration du protocole BGP.
   - **Abonnement :** Vérifiez que l’abonnement approprié s’affiche.
   - **Groupe de ressources :** Sélectionnez le groupe de ressources à utiliser. Vous pouvez créer un groupe de ressources ou en sélectionner un déjà créé.
   - **Emplacement :** L’emplacement correspond à la **Région** dans d’autres paramètres. Sélectionnez l’emplacement dans lequel cet objet va être créé. Vous pouvez sélectionner l’emplacement dans lequel se trouve votre réseau virtuel, mais vous n’êtes pas obligé de le faire.

   Il s’agit de la même page, mais avec le nom FQDN encadré :

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-gateway-fqdn.png" alt-text="Créer un nom FQDN de passerelle de réseau local":::

   > [!NOTE]
   >
   > * Le VPN Azure ne prend en charge qu’une seule adresse IPv4 par nom FQDN. Si le nom de domaine est résolu en plusieurs adresses IP, la passerelle VPN Azure utilise la première adresse IP retournée par les serveurs DNS. Pour éliminer toute incertitude, nous vous recommandons que le nom FQDN soit toujours résolu en une seule adresse IPv4. IPv6 n’est pas pris en charge.
   > * La passerelle VPN Azure actualise un cache DNS toutes les 5 minutes. Elle ne tente de résoudre les noms FQDN que pour les tunnels déconnectés. La réinitialisation de la passerelle déclenche également la résolution des noms FQDN.
   >

4. Quand vous avez terminé de spécifier les valeurs, sélectionnez le bouton **Créer** en bas de la page pour créer la passerelle de réseau local.
