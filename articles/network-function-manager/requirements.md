---
title: Conditions préalables et exigences pour le Gestionnaire de fonctions réseau Azure
description: En savoir plus sur les exigences et les conditions préalables pour le Gestionnaire de fonctions réseau Azure.
author: prmitt
ms.service: network-function-manager
ms.topic: article
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8843c92943071a96c6e0e9340823a85d01b1c218
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097677"
---
# <a name="network-function-manager-prerequisites-and-requirements"></a>Conditions préalables et exigences du Gestionnaire de fonctions réseau Azure

Cet article vous aide à comprendre les conditions préalables et les exigences requises pour configurer et utiliser le gestionnaire de fonctions réseau Azure.

## <a name="azure-stack-edge-pro-with-gpu-installed-and-activated"></a><a name="edge-pro"></a>Azure Stack Pro Edge avec GPU installé et activé

Assurez-vous que les conditions préalables suivantes sont respectées :

* Le service Gestionnaire de fonctions réseau Azure est activé sur l’appareil Azure Stack Edge Pro.
* Avant de déployer des fonctions réseau, vérifiez que Azure Stack Edge Pro est installé et activé.
* La ressource Azure Stack Edge doit être déployée dans une région prise en charge par des ressources de Gestionnaire de fonctions réseau. Pour plus d’informations, consultez [Disponibilité des régions](overview.md#regions).
* Veillez à suivre toutes les étapes des [Tutoriels[ et ](../databox-online/azure-stack-edge-gpu-deploy-checklist.md)Guides de démarrage rapide](../databox-online/azure-stack-edge-gpu-quickstart.md) Azure Stack Edge Pro.
* Vérifiez que l'**État** de l’appareil, situé dans la section Propriétés de la ressource Azure Stack Edge dans le Portail de gestion Azure, est **En ligne**.

   :::image type="content" source="./media/overview/properties.png" alt-text="Capture d’écran des propriétés." lightbox="./media/overview/properties.png":::

## <a name="partner-prerequisites"></a><a name="partner-prereq"></a>Conditions préalables pour les partenaires

Les clients peuvent choisir parmi un ou plusieurs [partenaires](partners.md) Gestionnaire de fonctions réseau pour déployer leur fonction réseau sur un appareil Azure Stack Edge. Pour plus d’informations sur les partenaires du gestionnaire de fonctions réseau, consultez l’article [partenaires](partners.md) .

Chaque partenaire a des exigences de mise en réseau pour le déploiement de sa fonction réseau sur un appareil Azure Stack Edge. Reportez-vous à la documentation du produit à partir des partenaires de fonctions réseau pour effectuer les tâches de configuration suivantes :

* [Configurer le réseau sur des ports différents](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
* [Activer le réseau de calcul sur votre appareil Azure Stack Edge](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network).

## <a name="azure-account"></a><a name="account"></a>Compte Azure

Pour la préversion, les partenaires du Gestionnaire de fonctions réseau Azure doivent activer le même ID d’abonnement Azure pour déployer leur fonction réseau à partir de la Place de marché Azure. Assurez-vous que votre ID d’abonnement Azure est intégré au partenaire.

Le service Gestionnaire de fonctions réseau Azure se compose des ressources **Appareil** du Gestionnaire de fonctions réseau et **Fonction réseau** du Gestionnaire de fonctions réseau. Les ressources Appareil et Fonction réseau se trouvent dans les abonnements Azure. L’ID de l’abonnement utilisé pour activer l’appareil Azure Stack Edge Pro et les ressources du Gestionnaire de fonctions réseau doivent être les mêmes.

## <a name="port-requirements-and-firewall-rules"></a><a name="port-firewall"></a>Exigences relatives aux ports et règles de pare-feu

Les services NFM (Gestionnaire de fonctions réseau) exécutés sur Azure Stack Edge nécessitent une connectivité sortante vers le service cloud NFM pour le trafic de gestion afin de déployer des fonctions réseau. NFM est entièrement intégré au service Azure Stack Edge. Analysez les exigences relatives aux ports réseau et aux règles de pare-feu pour l’appareil [Azure Stack Edge](../databox-online/azure-stack-edge-gpu-system-requirements.md#networking-port-requirements).  

Les partenaires de fonction réseau auront des exigences différentes pour les règles de configuration de port et de pare-feu pour gérer le trafic vers le Portail de gestion des partenaires. Vérifiez auprès de votre partenaire de fonctions réseau des exigences spécifiques.

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : créer une ressource d’appareil de gestionnaire de fonction réseau](create-device.md).
