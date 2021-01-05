---
title: Gestion de l’accès sécurisé aux ressources dans les réseaux virtuels en étoile pour les clients P2S
titleSuffix: Azure Virtual WAN
description: Cet article aide à utiliser les règles Azure Virtual WAN et Pare-feu Azure pour gérer l’accès sécurisé aux réseaux virtuels des clients VPN d’utilisateur (point à site).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 12/11/2020
ms.author: cherylmc
ms.openlocfilehash: b0937bbd72460b1d46ce0394af1933e858424966
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360320"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>Gestion de l’accès sécurisé aux ressources dans les réseaux virtuels en étoile pour les clients VPN d’utilisateur

Cet article explique comment utiliser des filtres et des règles Virtual WAN et Pare-feu Azure pour gérer l’accès sécurisé dans le cadre de connexions IKE v2 point à site ou VPN ouvert à vos ressources dans Azure. Cette configuration est utile si vous souhaitez restreindre l’accès des utilisateurs distants aux ressources Azure ou sécuriser vos ressources dans Azure.

La procédure décrite dans cet article aide à créer l’architecture du diagramme suivant pour permettre aux clients VPN d’utilisateur d’accéder à une ressource spécifique (VM1) dans un réseau virtuel en étoile connecté au hub virtuel, mais pas à d’autres ressources (VM2). Utilisez cet exemple d’architecture comme instructions de base.

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="Schéma : Hub virtuel sécurisé" :::

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* Vous disposez des valeurs disponibles pour la configuration d’authentification que vous souhaitez utiliser (par exemple un serveur RADIUS, l’authentification Azure Active Directory ou [Génération et exportation de certificats](../vpn-gateway/vpn-gateway-certificates-point-to-site.md)).

## <a name="create-a-virtual-wan"></a>Créer un WAN virtuel

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>Définition des paramètres de configuration P2S

La configuration point à site (P2S) définit les paramètres permettant de connecter des clients distants. Cette section aide à définir les paramètres de configuration P2S, puis à créer la configuration qui sera utilisée pour le profil client VPN. Les instructions à suivre dépendent de la méthode d’authentification choisie.

### <a name="authentication-methods"></a>Méthodes d’authentification

Il existe trois méthodes d’authentification possibles. Chacune comporte des exigences spécifiques. Sélectionnez-en une, puis suivez la procédure.

* **Authentification Azure Active Directory :** récupérez les informations suivantes :

   * **L’ID de l’application** Azure VPN Enterprise inscrite dans votre locataire Azure AD
   * **L’émetteur**. Exemple : `https://sts.windows.net/your-Directory-ID`.
   * Le **locataire Azure AD**. Exemple : `https://login.microsoftonline.com/your-Directory-ID`.

* **Authentification RADIUS :** récupérez l’adresse IP et le secret du serveur RADIUS, ainsi que les informations de certificat.

* **Certificats Azure :** avec cette configuration, les certificats sont obligatoires. Vous devez en générer ou en récupérer. Il faut un certificat client par client. En outre, les informations de certificat racine (clé publique) doivent être chargées. Pour plus d’informations sur les certificats requis, consultez [Génération et exportation de certificats](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

L’exemple suivant montre l’authentification par certificat Azure.

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>Création du hub et de la passerelle

Dans cette section, vous allez créer le hub virtuel avec une passerelle point à site. Lors de la configuration, vous pouvez utiliser les exemples de valeurs suivants :

* **Espace d’adressage IP privé du hub :** 10.0.0.0/24
* **Pool d'adresses des clients :** 10.5.0.0/16
* **Serveurs DNS personnalisés :** jusqu’à cinq serveurs DNS

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Générer les fichiers de configuration du client VPN

Dans cette section, vous allez générer et télécharger les fichiers du profil de configuration. Ils servent à configurer le client VPN natif sur l’ordinateur client. Pour plus d’informations sur le contenu des fichiers de profil client, consultez [Configuration point à site – Certificats](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md).

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>Configurer les clients VPN

Pour configurer les clients avec accès à distance, utilisez le profil téléchargé. La procédure pour chaque système d’exploitation étant différente, suivez les instructions qui s’appliquent à votre système.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>Connexion du réseau virtuel en étoile

Dans cette section, vous allez attacher le réseau virtuel en étoile au hub Virtual WAN.

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>Créer des machines virtuelles

Dans cette section, vous allez créer deux machines virtuelles dans votre réseau virtuel, VM1 et VM2. Dans le diagramme du réseau sont utilisés 10.18.0.4 et 10.18.0.5. Quand vous configurez vos machines virtuelles, veillez à sélectionner le réseau virtuel que vous avez créé (situé dans l’onglet Mise en réseau). Pour connaître la procédure de création d’une machine virtuelle, consultez [Démarrage rapide : Créer une machine virtuelle](../virtual-machines/windows/quick-create-portal.md).

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>Sécurisation du hub virtuel

Un hub virtuel standard ne dispose d’aucune stratégie de sécurité intégrée pour protéger les ressources des réseaux virtuels en étoile. Un hub virtuel sécurisé utilise le Pare-feu Azure ou un fournisseur tiers pour gérer le trafic entrant et sortant afin de protéger les ressources dans Azure.

Convertissez le hub en hub sécurisé suivant l’article [Configuration du Pare-feu Azure dans un hub Virtual WAN](howto-firewall.md).

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a>Création de règles pour gérer et filtrer le trafic

Créez des règles qui déterminent le comportement du Pare-feu Azure. En sécurisant le hub, vous veillez à ce que tous les paquets qui entrent dans le hub virtuel soient soumis au traitement du pare-feu avant d’accéder à vos ressources Azure.

Après avoir suivi cette procédure, vous aurez créé une architecture permettant aux utilisateurs VPN d’accéder à la machine virtuelle avec l’adresse IP privée 10.18.0.4, mais **NON** avec l’adresse IP privée 10.18.0.5.

1. Sur le **Portail Azure**, accédez à Firewall Manager.
1. Sous Sécurité, sélectionnez **Stratégies de Pare-feu Azure**.
1. Sélectionnez **Créer une stratégie de pare-feu Azure**.
1. Sous **Détails de la stratégie**, tapez un nom et sélectionnez la région dans laquelle votre hub virtuel est déployé.
1. Sélectionnez **Suivant : Paramètres DNS (préversion)** .
1. Sélectionnez **Suivant : Règles**.
1. Dans l’onglet **Règles**, sélectionnez **Ajouter une collection de règles**.
1. Donnez un nom à la connexion. Définissez le type **Réseau**. Ajoutez la valeur de priorité **100**.
1. Renseignez le nom de la règle, le type de source, la source, le protocole, les ports de destination et le type de destination, comme dans l’exemple ci-dessous. Sélectionnez ensuite **Ajouter**. Cette règle permet à n’importe quelle adresse IP du pool du client VPN d’accéder à la machine virtuelle avec l’adresse IP privée 10.18.04, mais non aux autres ressources connectées au hub virtuel. Créez les règles de votre choix en fonction de l’architecture et des règles d’autorisation souhaitées.

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="Règles de pare-feu" :::

1. Sélectionnez **Suivant : Renseignement sur les menaces**.
1. Sélectionnez **Suivant : Hubs**.
1. Sous l’onglet **Hubs**, sélectionnez **Associer des hubs virtuels**.
1. Sélectionnez le hub virtuel que vous avez créé, puis **Ajouter**.
1. Sélectionnez **Revoir + créer**.
1. Sélectionnez **Create** (Créer).

Ce processus peut prendre cinq minutes, voire davantage.

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>Routage du trafic via le Pare-feu Azure

Dans cette section, vous devez vous assurer que le trafic est acheminé via le Pare-feu Azure.

1. Sur le portail, sélectionnez **Hubs virtuels sécurisés** dans **Firewall Manager**.
1. Sélectionnez le hub virtuel que vous avez créé.
1. Sous **Paramètres**, sélectionnez **Configuration de la sécurité**.
1. Sous **Trafic privé**, sélectionnez **Envoyer via le Pare-feu Azure**.
1. Vérifiez que le trafic privé de connexion Réseau virtuel et Branche est sécurisé par le Pare-feu Azure.
1. Sélectionnez **Enregistrer**.

## <a name="validate"></a><a name="validate"></a>Valider

Vérifiez la configuration de votre hub sécurisé.

1. Connectez-vous au **Hub virtuel sécurisé** par VPN à partir de votre appareil client.
1. Effectuez un test ping sur l’adresse IP **10.18.0.4** à partir de votre client. Une réponse apparaît.
1. Effectuez un test ping sur l’adresse IP **10.18.0.5** à partir de votre client. Aucune réponse n’apparaît.

### <a name="considerations"></a>Considérations

* Veillez à ce que la **Table des itinéraires effectifs** présente sur le hub virtuel sécurisé comporte le tronçon suivant pour le trafic privé par le Pare-feu. Pour accéder à cette table, accédez à votre ressource **Hub virtuel**. Sous **Connectivité**, sélectionnez **Routage**, puis **Itinéraires effectifs**. Sélectionnez alors la table de route **Par défaut**.
* Vérifiez que vous avez créé des règles dans la section [Créer des règles](#create-rules). Si ces étapes ne sont pas respectées, les règles créées ne seront pas associées au hub. Par ailleurs, ni la table de route ni le flux de paquets n’utiliseront le Pare-feu Azure.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ sur Virtual WAN](virtual-wan-faq.md).
* Pour plus d’informations sur le Pare-feu Azure, consultez la [FAQ du Pare-feu Azure](../firewall/firewall-faq.md).
