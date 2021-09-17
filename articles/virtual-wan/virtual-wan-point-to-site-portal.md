---
title: 'Tutoriel : Utiliser Azure Virtual WAN pour créer une connexion de point à site vers Azure'
description: Dans ce tutoriel, découvrez comment utiliser Azure Virtual WAN pour créer une connexion VPN utilisateur (point à site) à Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: cherylmc
ms.openlocfilehash: e08123ce666efebf7db2e4c07167125a76b46609
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779897"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutoriel : Créer une connexion de VPN utilisateur à l’aide d’Azure Virtual WAN

Ce tutoriel vous montre comment utiliser Virtual WAN pour vous connecter à vos ressources dans Azure via une connexion OpenVPN ou IPsec/IKE (IKEv2) à l’aide d’une configuration d’utilisateur VPN (P2S). Pour utiliser ce type de connexion, le client VPN natif doit être configuré sur chaque ordinateur client connecté.
* Si vous souhaitez créer une connexion VPN utilisateur en utilisant l’authentification Azure AD, utilisez plutôt l’article [Configurer une connexion VPN utilisateur - Authentification Azure Active Directory](virtual-wan-point-to-site-azure-ad.md).
* Pour plus d’informations sur Azure Virtual WAN, consultez l’article [Vue d’ensemble d’Azure Virtual WAN](virtual-wan-about.md).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un WAN virtuel
> * Créer la configuration VPN d’utilisateur
> * Créer le hub virtuel et la passerelle
> * Générer les fichiers de configuration du client
> * Configurer les clients VPN
> * Connexion à un réseau virtuel
> * Afficher votre WAN virtuel
> * Modifier les paramètres

:::image type="content" source="./media/virtual-wan-about/virtualwanp2s.png" alt-text="Diagramme WAN virtuel.":::

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Créer un WAN virtuel

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-user-vpn-configuration"></a><a name="p2sconfig"></a>Créer une configuration VPN d’utilisateur

La configuration VPN (P2S) d’utilisateur définit les paramètres permettant de connecter des clients distants. Les instructions à suivre dépendent de la méthode d’authentification choisie.

Lors des étapes suivantes, il existe trois méthodes d’authentification possibles. Chacune comporte des exigences spécifiques. Sélectionnez-en une, puis suivez la procédure.

* **Certificats Azure :** avec cette configuration, les certificats sont obligatoires. Vous devez en générer ou en récupérer. Il faut un certificat client par client. En outre, les informations de certificat racine (clé publique) doivent être chargées. Pour plus d’informations sur les certificats requis, consultez [Génération et exportation de certificats](certificates-point-to-site.md).

* **Authentification Azure Active Directory :** Utilisez l’article [Configurer une connexion VPN utilisateur - Authentification Azure Active Directory](virtual-wan-point-to-site-azure-ad.md), qui contient les étapes spécifiques nécessaires à cette configuration.

* **Authentification RADIUS :** récupérez l’adresse IP et le secret du serveur RADIUS, ainsi que les informations de certificat.

### <a name="configuration-steps"></a>Étapes de configuration

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Créer un hub virtuel et une passerelle

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-client-configuration-files"></a><a name="download"></a>Générer les fichiers de configuration du client

Lorsque vous vous connectez à un réseau virtuel à l’aide du VPN (P2S) utilisateur, vous utilisez le client VPN qui est installé en mode natif sur le système d’exploitation à partir duquel vous vous connectez. Tous les paramètres de configuration nécessaires aux clients VPN sont contenus dans un fichier config zip de client VPN. Les paramètres dans le fichier zip vous aident à configurer facilement les clients VPN. Les fichiers de configuration du client VPN que vous générez sont spécifiques à la configuration VPN utilisateur de votre passerelle. Dans cette section, vous allez générer et télécharger les fichiers utilisés pour configurer vos clients VPN.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>Configurer les clients VPN

Pour configurer les clients VPN avec accès à distance, utilisez le package de profil téléchargé. La procédure est différente pour chaque système d’exploitation. Suivez les instructions qui s’appliquent à votre système.
Une fois que vous avez terminé de configurer votre client, vous pouvez vous connecter.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-vnet-to-hub"></a><a name="connect-vnet"></a>Connecter un réseau virtuel à un hub

Dans cette section, vous créez une connexion entre votre hub virtuel et votre réseau virtuel. Pour ce tutoriel, vous n’avez pas besoin de configurer les paramètres de routage.

[!INCLUDE [Connect virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="view-a-virtual-wan"></a><a name="viewwan"></a>Afficher un WAN virtuel

1. Accédez à votre **WAN virtuel**.

1. Sur la page **Vue d’ensemble**, chaque point sur la carte représente un hub.

1. Dans la section **Hubs et connexions**, vous pouvez voir l’état du hub, le site, la région, l’état de la connexion VPN et les octets entrés et sortis.

## <a name="modify-settings"></a>Modifier les paramètres

### <a name="modify-client-address-pool"></a><a name="address-pool"></a>Modifier un pool d’adresses client

[!INCLUDE [Modify client address pool](../../includes/virtual-wan-client-address-pool-include.md)]

### <a name="modify-dns-servers"></a><a name="dns"></a>Modifier des serveurs DNS

1. Accédez à votre **HUB virtuel -> VPN utilisateur (point à site)** .

1. Cliquez sur la valeur à côté de **Serveurs DNS personnalisés** pour ouvrir la page **Modifier la passerelle VPN de l’utilisateur**.

1. Sur la page **Modifier la passerelle VPN utilisateur**, modifiez le champ **Serveurs DNS personnalisés**. Entrez les adresses IP du serveur DNS dans les zones de texte **Serveurs DNS personnalisés**. Vous pouvez spécifier jusqu’à cinq serveurs DNS.

1. Cliquez au bas de la page sur **Modifier** pour valider vos paramètres.

1. Cliquez sur **Confirmer** pour enregistrer vos paramètres. L’application des changements apportés dans cette page peut prendre jusqu’à 30 minutes.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Supprimer des ressources

Quand vous n’avez plus besoin des ressources que vous avez créées, supprimez-les. Certaines des ressources Virtual WAN doivent être supprimées dans un certain ordre en raison des dépendances. La suppression peut prendre environ 30 minutes.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes


> [!div class="nextstepaction"]
> * [Gestion de l’accès sécurisé aux ressources dans les réseaux virtuels en étoile](manage-secure-access-resources-spoke-p2s.md)
