---
title: Gérer les incidents de sécurité dans Azure Security Center | Microsoft Docs
description: Ce document est conçu pour vous aider à utiliser d’Azure Security Center pour gérer les incidents de sécurité.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: memildin
ms.openlocfilehash: b2ab769fc2595af9fd1ea41f1ef492944cb5e7fa
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665766"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Gérer les incidents de sécurité dans Azure Security Center

Procéder au triage et à l’examen des alertes de sécurité peut prendre beaucoup de temps, même pour le plus compétent des analystes de sécurité, et bien souvent il est difficile de savoir par où commencer. En utilisant les [fonctions d’analyse](security-center-detection-capabilities.md) pour connecter les informations entre différentes [alertes de sécurité](security-center-managing-and-responding-alerts.md), le Centre de sécurité peut vous fournir une vue unique d’une campagne d’attaque et de l’ensemble des alertes associées. Vous pouvez donc rapidement savoir quelles actions l’attaquant a effectuées et quelles ressources ont été affectées.

Cette rubrique explique les incidents dans Security Center, et comment utiliser ce dernier pour corriger les alertes associées.

## <a name="what-is-a-security-incident"></a>Qu’est-ce qu’un incident de sécurité ?

Dans le Centre de sécurité, un incident de sécurité est un regroupement de toutes les alertes d’une ressource correspondant à des modèles de [chaîne de destruction](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) . Les incidents apparaissent dans la liste [Alertes de sécurité](security-center-managing-and-responding-alerts.md). Cliquez sur un incident pour afficher les alertes associées, ce qui vous permet d’en savoir plus sur chaque occurrence.

## <a name="managing-security-incidents"></a>Gestion des incidents de sécurité

1. Dans le tableau de bord de Security Center, cliquez sur la mosaïque **Alertes de sécurité**. Les incidents et les alertes sont répertoriés. Notez que la description de l’incident de sécurité présente une autre icône que celle utilisée pour les autres alertes.

    ![Afficher les incidents de sécurité](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Pour afficher les détails, cliquez sur un incident. Le panneau **Incident de sécurité détecté** affiche des détails supplémentaires. La section **Informations générales** peut vous donner un aperçu de ce qui a déclenché l’alerte de sécurité. Elle affiche des informations telles que la ressource cible, l’adresse IP source (le cas échéant), elle indique si l’alerte est toujours active et affiche des recommandations sur la façon de résoudre le problème.  

    ![Répondre aux incidents de sécurité dans Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Pour obtenir plus d’informations sur chaque alerte, cliquez dessus. La correction suggérée par Security Center dépend de l’alerte de sécurité.

   > [!NOTE]
   > Une même alerte peut s’afficher dans le cadre d’un incident, mais également apparaître sous la forme d’une alerte autonome.

    ![Détails de l’alerte](./media/security-center-incident/security-center-incident-alert.png)

1. Suivez les étapes de correction indiquées pour chaque alerte.

Pour plus d’informations sur les alertes, consultez [Gestion et résolution des alertes de sécurité](security-center-managing-and-responding-alerts.md).

Les rubriques suivantes vous guident tout au long des différentes alertes en fonction des types de ressources :

* [Alertes liées aux serveurs et machines virtuelles IaaS](security-center-alerts-iaas.md)
* [Alertes liées au calcul natif](security-center-alerts-compute.md)
* [Alertes liées aux services de données](security-center-alerts-data-services.md)

Les rubriques suivantes expliquent comment Security Center utilise les différentes données de télémétrie qu’il collecte à partir de l’intégration à l’infrastructure Azure afin d’appliquer des couches de protection supplémentaires pour les ressources déployées sur Azure :

* [Alertes liées à la couche de services](security-center-alerts-service-layer.md)
* [Détection des menaces pour Azure WAF et Azure DDoS Protection](security-center-alerts-integration.md)

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez vu comment utiliser la fonctionnalité de gestion des incidents de sécurité du Centre de sécurité. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Alertes de sécurité dans Azure Security Center](security-center-alerts-overview.md).
* [Gérer les alertes de sécurité](security-center-managing-and-responding-alerts.md)
* [Guide des opérations et de planification du Centre de sécurité Azure](security-center-planning-and-operations-guide.md)
* [FAQ de Azure Security Center](security-center-faq.md): forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/): trouvez des billets de blog sur la sécurité et la conformité Azure.
