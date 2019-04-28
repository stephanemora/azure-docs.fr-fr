---
title: Comment configurer des alertes sur les événements de journal de diagnostic de passerelle VPN Azure
description: Étapes pour configurer des alertes sur les événements de journal de diagnostic de passerelle VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 39a6d2e6201dd0635149159cb3727fd3c40f710a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769735"
---
# <a name="setting-up-alerts-on-vpn-gateway-diagnostic-log-events"></a>Définir des alertes sur les événements de journal de diagnostic de passerelle VPN

Cet article vous permet de configurer des alertes basées sur les événements de journal de diagnostic de passerelle VPN.


## <a name="setup"></a>Configurer des alertes Azure Monitor basées sur les événements de journal de diagnostic à l’aide du portail

Les étapes de l’exemple ci-dessous crée une alerte pour un événement de déconnexion de tunnel VPN site à site



1. Recherchez « Log Analytique » sous tous les services et choisir « Espaces de travail Analytique de journal » ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "créer")

2. Cliquez sur « Créer » dans la page journal Analytique.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "sélectionnez")

3. Vérifiez l’espace de travail « Créer nouveau » et renseignez les détails.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "sélectionnez")

4. Rechercher votre passerelle VPN sous la section « surveiller » > panneau « Paramètres de Diagnostics » ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "sélectionnez")

5. Pour activer les diagnostics, double-cliquez sur la passerelle, puis cliquez sur « Activer Diagnostics » ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "sélectionnez")

6. Renseignez les détails et vérifiez que l’option « Envoyer à Log Analytique » et « TunnelDiagnosticLog » sont vérifiées. Choisissez l’espace de travail d’Analytique de journal qui a été créé à l’étape 3.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "sélectionnez")

7. Accédez à la vue d’ensemble de la ressource de la passerelle réseau virtuel et sélectionnez « Alertes » dans l’onglet analyse, puis créer une règle d’alerte ou modifier une règle d’alerte existante.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "sélectionnez")

8. Sélectionnez l’espace de travail Analytique de journal et de la ressource.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "sélectionnez")

9. Sélectionnez « recherche de journal personnalisée » en tant que la logique de signal sous « Ajouter une condition » ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "sélectionnez")

10. Entrez la requête suivante dans la zone de texte « Requête de recherche » en remplaçant les valeurs dans <> comme il convient.

    AzureDiagnostics | où catégorie == « TunnelDiagnosticLog » et ResourceId == toupper («<RESOURCEID OF GATEWAY>») et TimeGenerated > ago(5m) et remoteIP_s == «<REMOTE IP OF TUNNEL>» et status_s == « Déconnecté »

    Définissez la valeur de seuil sur 0 et cliquez sur « Terminé »

    ![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "sélectionnez")

11. Dans la page « Créer une règle », cliquez sur « Créer » dans la section groupes d’actions. Renseignez les détails et cliquez sur OK

![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "sélectionnez")

12. Dans la page « Créer une règle », renseignez les détails pour « Personnaliser Action » et assurez-vous que le nom de groupe d’actions approprié s’affiche dans la section « Nom du groupe d’actions ». Cliquez sur « Créer une règle d’alerte » pour créer la règle.
![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "sélectionnez")

## <a name="next-steps"></a>Étapes suivantes

Pour configurer des alertes sur les mesures de tunnel, consultez [comment configurer des alertes sur les mesures de la passerelle VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
