---
title: Utiliser le Pare-feu Azure pour protéger Azure Virtual Desktop
description: Découvrez comment utiliser le Pare-feu Azure pour protéger les déploiements d’Azure Virtual Desktop
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 08/09/2021
ms.author: victorh
ms.openlocfilehash: 5c165dc8f00bb21894de06e541c02788bd7b51e5
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424990"
---
# <a name="use-azure-firewall-to-protect-azure-virtual-desktop-deployments"></a>Utilisez le Pare-feu Azure pour protéger les déploiements Azure Virtual Desktop

Azure Virtual Desktop est un service de virtualisation de bureau et d’application qui s’exécute sur Azure. Quand un utilisateur final se connecte à un environnement Azure Virtual Desktop, sa session est exécutée par un pool d’hôtes. Un pool d’hôtes est une collection de machines virtuelles Azure qui s’inscrivent auprès d’Azure Virtual Desktop en tant qu’hôtes de la session. Ces machines virtuelles s’exécutent dans votre réseau virtuel et sont soumises aux contrôles de sécurité de ce dernier. Elles ont besoin d’un accès Internet sortant au service Azure Virtual Desktop pour fonctionner correctement et peuvent également nécessiter un accès Internet sortant pour les utilisateurs finaux. Le Pare-feu Azure peut vous aider à verrouiller votre environnement et à filtrer le trafic sortant.

[ ![Architecture Azure Virtual Desktop](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Suivez les instructions de cet article pour renforcer la protection de votre pool d’hôtes Azure Virtual Desktop à l’aide du Pare-feu Azure.

## <a name="prerequisites"></a>Configuration requise

 - Un environnement Azure Virtual Desktop déployé et un pool d’hôtes.
 - Un Pare-feu Azure déployé avec au moins une stratégie du gestionnaire de pare-feu

   Pour plus d’informations, consultez [Didacticiel : créer un pool d’hôtes à l’aide du portail Azure](../virtual-desktop/create-host-pools-azure-marketplace.md)

Pour plus d’informations sur l’environnement Azure Virtual Desktop, consultez [Environnement Azure Virtual Desktop](../virtual-desktop/environment-setup.md).

## <a name="host-pool-outbound-access-to-azure-virtual-desktop"></a>Accès sortant du pool d’hôtes à Azure Virtual Desktop

Les machines virtuelles Azure que vous créez pour Azure Virtual Desktop doivent avoir accès à plusieurs noms de domaine complets (FQDN) pour fonctionner correctement. Le Pare-feu Azure fournit une étiquette FQDN Azure Virtual Desktop pour simplifier cette configuration. Procédez comme suit pour autoriser le trafic sortant de la plateforme Azure Virtual Desktop :

Vous devrez créer une stratégie de Pare-feu Azure et créer des regroupements de règles pour les règles de réseau et les règles d’application. Attribuez une priorité au regroupement de règles et une action d’autorisation ou de refus.

### <a name="create-network-rules"></a>Créer les règles de réseau

| Nom      | Type de source | Source                    | Protocol | Ports de destination | Type de destination | Destination                       |
| --------- | ----------- | ------------------------- | -------- | ----------------- | ---------------- | --------------------------------- |
| Nom de la règle | Adresse IP  | Adresse IP du réseau virtuel ou du sous-réseau | TCP      | 80                | Adresse IP       | 169.254.169.254, 168.63.129.16    |
| Nom de la règle | Adresse IP  | Adresse IP du réseau virtuel ou du sous-réseau | TCP      | 443               | Étiquette du service      | AzureCloud, WindowsVirtualDesktop |
| Nom de la règle | Adresse IP  | Adresse IP du réseau virtuel ou du sous-réseau | TCP, UDP | 53                | Adresse IP       | *                                 |

> [!NOTE]
> Certains déploiements peuvent ne pas avoir besoin de règles DNS. Par exemple, les contrôleurs de domaine Azure Active Directory transfèrent les requêtes DNS vers Azure DNS sur 168.63.129.16.

### <a name="create-application-rules"></a>Créer des règles d’application

| Nom      | Type de source | Source                    | Protocol   | Type de destination | Destination                                                                                 |
| --------- | ----------- | ------------------------- | ---------- | ---------------- | ------------------------------------------------------------------------------------------- |
| Nom de la règle | Adresse IP  | Adresse IP du réseau virtuel ou du sous-réseau | Https:443  | Étiquette FQDN         | WindowsVirtualDesktop, WindowsUpdate, Windows Diagnostics, MicrosoftActiveProtectionService |
| Nom de la règle | Adresse IP  | Adresse IP du réseau virtuel ou du sous-réseau | Https:1688 | FQDN             | kms.core.windows.net                                                                        |

> [!IMPORTANT]
> Nous vous recommandons de ne pas utiliser l’inspection TLS avec Azure Virtual Desktop. Pour plus d'informations, consultez les [instructions pour le serveur proxy](../virtual-desktop/proxy-server-support.md#dont-use-ssl-termination-on-the-proxy-server).

## <a name="host-pool-outbound-access-to-the-internet"></a>Accès sortant du pool d’hôtes à Internet

Selon les besoins de votre organisation, vous souhaiterez peut-être activer l’accès Internet sortant sécurisé pour vos utilisateurs finaux. Si la liste des destinations autorisées est bien définie (par exemple, pour [accès Microsoft 365](/microsoft-365/enterprise/microsoft-365-ip-web-service)), vous pouvez utiliser des règles d’application et de réseau du Pare-feu Azure pour configurer l’accès requis. Le trafic des utilisateurs finaux est ainsi directement acheminé vers Internet, ce qui optimise les performances. Si vous devez autoriser la connectivité réseau pour Windows 365 ou Intune, consultez [Exigences réseau pour Windows 365](/windows-365/requirements-network#allow-network-connectivity) et [Points de terminaison réseau pour Intune](/mem/intune/fundamentals/intune-endpoints).

Si vous souhaitez filtrer le trafic Internet utilisateur sortant à l’aide d’une passerelle web sécurisée locale existante, vous pouvez configurer des navigateurs web ou d’autres applications qui s’exécutent sur le pool d’hôtes Azure Virtual Desktop avec une configuration de proxy explicite. Pour obtenir un exemple, consultez [Procédure d’utilisation des options de ligne de commande MicrosoftEdge pour configurer les paramètres de proxy](/deployedge/edge-learnmore-cmdline-options-proxy-settings). Ces paramètres de proxy n’influencent que l’accès Internet de l’utilisateur final, ce qui permet le trafic sortant de la plateforme Azure Virtual Desktop directement via le Pare-feu Azure.

## <a name="control-user-access-to-the-web"></a>Contrôler l’accès des utilisateurs au web

Les administrateurs peuvent autoriser ou refuser l’accès des utilisateurs à différentes catégories de sites web. Ajoutez une règle à votre collection d’applications à partir de votre adresse IP spécifique vers les catégories web que vous souhaitez autoriser ou refuser. Vérifiez toutes les [catégories web](web-categories.md).

## <a name="additional-considerations"></a>Considérations supplémentaires

Vous devrez peut-être configurer des règles de pare-feu supplémentaires, en fonction de vos exigences :

- Accès au serveur NTP

  Par défaut, les machines virtuelles exécutant Windows se connectent à `time.windows.com` via le port UDP 123 pour la synchronisation de l’heure. Créez une règle de réseau pour autoriser cet accès ou pour un serveur de temps que vous utilisez dans votre environnement.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur Azure Virtual Desktop : [Qu’est-ce qu’Azure Virtual Desktop ?](../virtual-desktop/overview.md)
