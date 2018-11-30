---
title: Problèmes connus avec Azure Monitor pour machines virtuelles (préversion) | Microsoft Docs
description: Azure Monitor pour machines virtuelles est une solution d’Azure qui supervise à la fois l’intégrité et les performances du système d’exploitation de la machine virtuelle Azure. En outre, elle identifie automatiquement les composants des applications et leurs dépendances avec les autres ressources, et effectue le mappage de leurs communications respectives. Cet article porte sur des problèmes connus.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: 99f84e9784c448091c0257218855c3bf32c2f8f4
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51714064"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Problèmes connus avec Azure Monitor pour machines virtuelles (préversion)

Les problèmes suivants concernent la fonctionnalité Health (Intégrité) d’Azure Monitor pour machines virtuelles :

- La fonctionnalité Intégrité est activée pour toutes les machines virtuelles connectées à l’espace de travail Log Analytics, même si elle est initiée et finalisée à partir d’une seule et même machine virtuelle.
- Si vous effectuez une nouvelle tentative d’intégration après avoir désactivé la surveillance d’une machine virtuelle à l’aide des méthodes prises en charge, l’opération doit être réalisée sur le même espace de travail.  Si un nouvel espace de travail est utilisé lors de l’affichage de l’état d’intégrité de cette machine virtuelle, son comportement peut sembler anormal.
- Si une machine virtuelle Azure n’existe plus parce qu’elle a été supprimée, elle figure dans la liste des machines virtuelles pendant trois à sept jours. Par ailleurs, le fait de cliquer sur l’état d’une machine virtuelle supprimée a pour effet de lancer la vue **Diagnostics d’intégrité**, qui entre ensuite dans une boucle de chargement. Le fait de sélectionner le nom d’une machine virtuelle supprimée a pour effet de lancer un panneau avec un message indiquant que la machine virtuelle a été supprimée.
- Il n’est pas possible de modifier la période et la fréquence des critères d’intégrité dans cette version. 
- Les critères d’intégrité ne peuvent pas être désactivés. 
- Après l’intégration, un certain temps peut s’écouler avant que les données ne soient affichées dans Azure Monitor -> Virtual Machines -> Health (Azure Monitor -> Machines virtuelles -> Intégrité) ou dans le panneau des ressources de la machine virtuelle -> Insights
- Les mises à jour de Health Diagnostics (Diagnostics d’intégrité) sont plus rapides que celles des autres affichages, ce qui peut entraîner des retards d’information en passant d’une fenêtre à l’autre  
- Les résumés d’alertes inclus avec Azure Monitor pour machines virtuelles ne concernent que les alertes liées à des problèmes d’intégrité détectés sur les machines virtuelles Azure supervisées.
- La fenêtre **Liste des alertes** de l’unique machine virtuelle et d’Azure Monitor affiche les alertes dont l’état du moniteur a été défini « activé » dans les 30 derniers jours.  Elles ne sont pas configurables. Cependant, une fois que la page d’affichage de la **Liste d’alertes**est lancée, vous pouvez modifier la valeur de filtrage de l’état du moniteur et de l’intervalle de temps en cliquant sur le résumé.
- Sur la page **Liste des alertes**, nous vous conseillons de ne pas modifier les filtres **Type de ressource**, **Ressource** et **Service de supervision** car ils ont été spécifiquement configurés pour la solution (cette vue de liste présente quelques champs supplémentaires par rapport à Azure Monitor -> Fenêtre de la liste des alertes).    
- Dans les machines virtuelles Linux, la fenêtre **Diagnostic d’intégrité** (Health Diagnostics) contient le nom de domaine complet de la machine virtuelle au lieu du nom de la machine virtuelle défini par l’utilisateur.
- La fermeture des machines virtuelles actualise certains de ses critères d’intégrité en les faisant passer à un état critique et d’autres à un état intègre avec un état net de la machine virtuelle à un état critique.
- La gravité d’une alerte d’intégrité n’est pas modifiable, elle ne peut qu’être activée ou désactivée.  De plus, certains niveaux de gravité sont mis à jour en fonction de l’état du critère d’intégrité.
- La modification des paramètres d’une instance de critère d’intégrité entraîne la modification du même paramètre pour toutes les instances de critère d’intégrité de même type dans la machine virtuelle. Par exemple, si le seuil de l’instance de critère d’intégrité de l’espace disque libre correspondant au disque logique C : est modifié, alors il s’applique à tous les autres disques logiques idco trouvés et supervisés de la même machine virtuelle.   
- Les seuils des critères d’intégrité ci-dessous ciblant une machine virtuelle Windows ne peuvent pas être modifiés, car leurs états d’intégrité sont déjà définis sur **en cours d’exécution** ou **disponible**. Dans le cas d’une requête émise à partir de l’[API de supervision de charge de travail](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager), l’état d’intégrité présente la valeur **LessThan** ou **GreaterThan** pour *comparisonOperator* avec un seuil (*threshold*) de **4** pour le service ou l’entité si :
   - Le service DNS Client Service Health n’est pas en cours d’exécution 
   - Le service DHCP Client Service Health n’est pas en cours d’exécution 
   - Le service RPC Service Health n’est pas en cours d’exécution 
   - Le service Windows Firewall Service Health n’est pas en cours d’exécution
   - Le service Windows Event Log Service Health n’est pas en cours d’exécution 
   - Le service Server Service Health n’est pas en cours d’exécution 
   - Le service Windows Remote Management Service Health n’est pas en cours d’exécution 
   - Le disque logique est indisponible du fait d’une erreur ou de l’altération du système de fichiers

- Les seuils des critères d’intégrité Linux suivants ne peuvent pas être modifiés, car leur état d’intégrité est déjà défini sur **true**.  Pour l’état d’intégrité, *comparisonOperator* indique **LessThan** avec un seuil (*threshold*) de **1** dans le cas d’une requête émise à partir de l’API de supervision de charge de travail pour l’entité en fonction de son contexte :
   - État du disque logique – Le disque logique n’est pas en ligne / disponible
   - État du disque – Le disque n’est pas en ligne / disponible
   - État de la carte réseau - La carte réseau est désactivée  

- Le critère d’intégrité **Utilisation d’UC totale** dans Windows indique un seuil **différent de 4** sur le portail, de même lorsqu’il fait l’objet d’une requête de l’API de supervision de charge de travail et que le taux d’utilisation de l’UC est supérieur à 95 % et que la longueur de la file d’attente système est supérieure à 15. Ce critère d’intégrité ne peut pas être modifié dans cette version.  
- La prise en compte des modifications de configuration, comme la mise à jour d’un seuil, peut prendre jusqu'à 30 minutes, même si la mise à jour du portail ou de l’API de supervision de charge de travail est immédiate.  
- Les critères d’intégrité au niveau du processeur individuel et du processeur logique ne sont pas disponibles dans Windows ; seul le critère **Utilisation d’UC totale** est disponible pour les machines virtuelles Windows.  
- Les règles d’alerte définies pour chaque critère d’intégrité ne sont pas exposées sur le portail Azure. Elles ne peuvent être configurées qu’à partir de l’[API de supervision de charge de travail](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) pour activer ou désactiver une règle d’alerte d’intégrité.  
- Il n’est pas possible d’affecter un [groupe d’actions Azure Monitor](../../monitoring-and-diagnostics/monitoring-action-groups.md) pour des alertes d’intégrité à partir du portail Azure. Vous pouvez utiliser l’API de paramètre de notification uniquement pour configurer le déclenchement d’un groupe d’actions chaque fois qu’une alerte d’intégrité est déclenchée. Actuellement, il est possible d’affecter des groupes d’action pour une machine virtuelle, si bien que toutes les *alertes d’intégrité* déclenchées sur la machine virtuelle déclenchent les mêmes groupes d’actions. Contrairement aux alertes Azure classiques, il ne peut pas exister un groupe d’action distinct pour chaque règle d’alerte d’intégrité. De plus, seuls les groupes d’actions configurés pour adresser des notifications par e-mail ou SMS sont pris en charge quand des alertes d’intégrité sont déclenchées. 

## <a name="next-steps"></a>Étapes suivantes
Pour découvrir les exigences et les méthodes relatives à l’activation de la supervision de vos machines virtuelles, consultez l’article [Intégrer Azure Monitor pour machines virtuelles](vminsights-onboard.md).