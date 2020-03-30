---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74566259"
---
Cet article suppose que vous possédez :

1. Un **VPN site à site** ou une connexion **Express Route** déjà établie entre votre réseau local et le réseau virtuel Microsoft Azure.
2. Un compte d’utilisateur avec les autorisations nécessaires pour créer une machine virtuelle dans l’abonnement Azure dans lequel les machines virtuelles ont été basculées.
3. Un abonnement avec un minimum de 8 cœurs disponibles pour faire tourner une nouvelle machine virtuelle de serveur de processus.
4. La **phrase secrète du serveur de configuration**.

> [!TIP]
> Vérifiez que vous êtes en mesure de connecter le port 443 du serveur de configuration (qui s’exécute en local) à partir du réseau virtuel Azure dans lequel les machines virtuelles ont été basculées.
