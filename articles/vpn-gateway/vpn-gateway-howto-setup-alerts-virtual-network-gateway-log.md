---
title: Configurer des alertes sur les événements du journal de diagnostic à partir de la passerelle VPN Azure
description: Étapes pour configurer des alertes sur les événements de journal de diagnostic de passerelle VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 3880c847c54136dfd3ba1ecfe0178565091e229f
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510216"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Configurer des alertes sur les événements du journal de diagnostic à partir de la passerelle VPN

Cet article vous permet de configurer des alertes basées sur les événements de journal de diagnostic à partir de la passerelle VPN Azure.

## <a name="setup"></a>Configurer des alertes

L’exemple suivant montre comment créera une alerte pour un événement de déconnexion qui implique un tunnel VPN de site à site :


1. Dans le portail Azure, recherchez **Analytique de journal** sous **tous les services** et sélectionnez **espaces de travail Analytique de journal**.

   ![Sélections permettant d’accéder aux espaces de travail Analytique de journal](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "créer")

2. Sélectionnez **créer** sur le **Analytique de journal** page.

   ![Page d’Analytique de journal avec le bouton Créer](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "sélectionnez")

3. Sélectionnez **créer un nouveau** et renseignez les détails.

   ![Création d’un espace de travail Analytique de journal](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "sélectionnez")

4. Rechercher votre passerelle VPN sur le **moniteur** > **les paramètres de diagnostic** panneau.

   ![Sélections pour la recherche de la passerelle VPN dans les paramètres de Diagnostic](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "sélectionnez")

5. Pour activer les diagnostics, double-cliquez sur la passerelle, puis sélectionnez **activer les diagnostics**.

   ![Sélections pour l’activation des diagnostics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "sélectionnez")

6. Renseignez les détails, puis vérifiez que **envoyer à Log Analytique** et **TunnelDiagnosticLog** sont sélectionnés. Choisissez l’espace de travail d’Analytique de journal que vous avez créé à l’étape 3.

   ![Sélectionné des cases à cocher](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "sélectionnez")

7. Accédez à la vue d’ensemble de la ressource de passerelle de réseau virtuel et sélectionnez **alertes** à partir de la **surveillance** onglet. Créez une règle d’alerte ou modifier une règle d’alerte existante.

   ![Sélections pour la création d’une règle d’alerte](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "sélectionnez")

   ![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "sélectionnez")
8. Sélectionnez l’espace de travail Analytique de journal et de la ressource.

   ![Sélections pour l’espace de travail et des ressources](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "sélectionnez")

9. Sélectionnez **recherche de journal personnalisée** en tant que la logique de signal sous **ajouter une condition**.

   ![Sélections pour une recherche de journal personnalisé](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "sélectionnez")

10. Entrez la requête suivante dans la zone de texte **Requête de recherche**. Remplacez les valeurs dans <> comme il convient.

     `AzureDiagnostics |
     where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
     remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"`

    Définissez la valeur de seuil sur 0 et sélectionnez **fait**.

    ![Entrez une requête et en sélectionnant un seuil](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "sélectionnez")

11. Sur le **créer une règle** page, sélectionnez **créer un nouveau** sous le **groupes d’actions** section. Renseignez les détails et sélectionnez **OK**.

    ![Détails pour un groupe d’actions](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "sélectionnez")

12. Sur le **créer une règle** page, renseignez les détails pour **personnaliser les Actions** et assurez-vous que le nom correct apparaît dans le **nom groupe d’actions** section. Sélectionnez **créer une règle d’alerte** pour créer la règle.

    ![Sélections pour la création d’une règle](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "sélectionnez")

## <a name="next-steps"></a>Étapes suivantes

Pour configurer des alertes sur les mesures de tunnel, consultez [configurer des alertes sur les métriques de passerelle VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
