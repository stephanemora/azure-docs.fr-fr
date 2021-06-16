---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/26/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1345df8991acba09feb65eb11e9eadc21b9c7af4
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579691"
---
Le client VPN Windows 10, Always On, est doté d’une nouvelle fonctionnalité, qui permet de maintenir une connexion VPN. Always On permet au profil VPN actif de se connecter automatiquement et rester connecté en fonction de déclencheurs, tels qu’une connexion d’utilisateur, un changement de l’état du réseau ou l’activation de l’écran de l’appareil.

Il est possible d’utiliser les passerelles avec Windows 10 Always On pour établir des tunnels d’appareil et des tunnels d’utilisateur persistants vers Azure.

Les connexions VPN Always On incluent l’un de ces types de tunnels :

* **Tunnel de d’appareil** : se connecte à des serveurs VPN spécifiés avant que les utilisateurs ne se connectent à l’appareil. Les scénarios de connectivité de préconnexion et de gestion des appareils utilisent un tunnel d’appareil.

* **Tunnel utilisateur** : établit une connexion uniquement lorsque l’utilisateur se connecte à son appareil. En utilisant un tunnel d’utilisateur, vous pouvez accéder aux ressources de votre entreprise ou organisation via des serveurs VPN.

Le tunnel appareil et le tunnel utilisateur fonctionnent tous deux de façon indépendante vis-à-vis de leurs profils VPN. Ils peuvent être connectés simultanément et utiliser différentes méthodes d’authentification et d’autres paramètres de configuration VPN selon les besoins.
