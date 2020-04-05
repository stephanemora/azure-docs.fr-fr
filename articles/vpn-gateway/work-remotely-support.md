---
title: 'Travail à distance avec P2S : Passerelle VPN Azure'
description: Cette page décrit comment tirer parti d’Azure Bastion pour travailler à distance dans le contexte de la pandémie de COVID-19.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: alzam
ms.openlocfilehash: b751817467bd263e8b7c64ccc95ec82ef1579836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337121"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Travail à distance à l’aide d’une connexion point à site par passerelle VPN Azure

>[!NOTE]
>Cet article décrit comment vous pouvez tirer parti d’une Passerelle VPN Azure, d’Azure, du réseau Microsoft et de l’écosystème de partenaires Azure pour travailler à distance et atténuer les problèmes de réseau auxquels vous faites face dans le contexte de la crise de COVID-19.
>

Cet article décrit les options disponibles pour les organisations afin de configurer l’accès à distance à leurs utilisateurs ou de compléter leurs solutions existantes avec une capacité supplémentaire pendant l’épidémie de COVID-19.

La solution point à site Azure, basée sur le cloud, peut être approvisionnée rapidement pour répondre à la demande croissante des utilisateurs désireux de travailler à partir de leur domicile. Elle peut monter en puissance facilement et se désactiver tout aussi facilement et rapidement lorsque la capacité augmentée n’est plus nécessaire.

## <a name="about-point-to-site-vpn"></a>À propos du VPN de point à site

Une connexion par passerelle VPN point à site (P2S) vous permet de créer une connexion sécurisée à votre réseau virtuel à partir d’un ordinateur de client individuel. Une connexion P2S est établie en étant démarrée à partir de l’ordinateur client. Cette solution est utile pour les télétravailleurs désireux de se connecter à des réseaux virtuels Azure ou à des centres de données locaux à partir d’un emplacement distant tel que leur domicile ou une salle de conférence. Cet article explique comment permettre aux utilisateurs de travailler à distance dans diverses circonstances.

Le tableau ci-dessous présente les systèmes d’exploitation clients et les options d’authentification disponibles. Il serait utile de sélectionner la méthode d’authentification en fonction du système d’exploitation client déjà utilisé. Par exemple, sélectionnez OpenVPN avec l’authentification basée sur les certificats si vous avez plusieurs systèmes d’exploitation clients qui doivent se connecter. Notez également qu’un VPN point à site est pris en charge uniquement sur des passerelles VPN basées sur un itinéraire.

![point à site](./media/working-remotely-support/ostable.png "Système d''exploitation")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a>Scénario 1 : les utilisateurs doivent accéder à des ressources dans Azure uniquement

Dans ce scénario, les utilisateurs distants doivent accéder uniquement à des ressources qui se trouvent dans Azure.

![point à site](./media/working-remotely-support/scenario1.png "Scénario 1")

À un niveau élevé, les étapes suivantes sont nécessaires pour permettre aux utilisateurs de se connecter aux ressources Azure en toute sécurité :

1. Créer une passerelle de réseau virtuel (s’il n’en existe pas)
2. Configurer un VPN point à site sur la passerelle
    3. [Pour l’authentification par certificat, suivez ce lien](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw).
    2.  [Pour OpenVPN, suivez ce lien](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn).
    3.  [Pour l’authentification Azure AD, suivez ce lien](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-tenant).
    4.  [Pour résoudre les problèmes de connexion point à site, suivez ce lien](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems).
3. Télécharger et distribuer la configuration de client VPN
4. Distribuer les certificats (si l’authentification par certificat est sélectionnée) aux clients
5. Se connecter au VPN Azure

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a>Scénario 2 : les utilisateurs doivent accéder à des ressources dans Azure et/ou localement

Dans ce scénario, les utilisateurs distants doivent accéder à des ressources qui se trouvent dans Azure et dans des centres de données locaux.

![point à site](./media/working-remotely-support/scenario2.png "Scénario 2")

À un niveau élevé, les étapes suivantes sont nécessaires pour permettre aux utilisateurs de se connecter aux ressources Azure en toute sécurité :

1. Créer une passerelle de réseau virtuel (s’il n’en existe pas)
2. Configurer un VPN point à site sur la passerelle (voir le Scénario 1 ci-dessus)
3. Configurer un tunnel site à site sur la passerelle de réseau virtuel Azure avec BGP activé
4. Configurer un appareil local pour se connecter à la passerelle de réseau virtuel Azure
5. Télécharger le profil point à site à partir du portail Azure et le distribuer aux clients

[Suivez ce lien pour savoir comment configurer un tunnel VPN site à site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Forum Aux Questions (FAQ) sur l’authentification par certificat Azure native

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Forum Aux Questions (FAQ) sur l’authentification RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Configurer une connexion P2S - Authentification Azure AD](openvpn-azure-ad-tenant.md)

* [Configurer une connexion P2S - Authentification RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurer une connexion P2S - Authentification par certificat natif Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**« OpenVPN » est une marque d’OpenVPN Inc.**