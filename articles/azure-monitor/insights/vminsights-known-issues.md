---
title: Problèmes connus avec Azure Monitor pour machines virtuelles (version préliminaire) | Microsoft Docs
description: Cet article décrit les problèmes connus avec Azure Monitor pour machines virtuelles, une solution dans Azure qui combine l’analyse de l’intégrité et des performances du système d’exploitation de machine virtuelle Azure. Cette solution détecte automatiquement les composants d’application et les dépendances avec d’autres ressources, et mappe la communication entre ces différents éléments.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: b2a60ddee58a7a9c37582085132ee8a1df767cb4
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190354"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Problèmes connus avec Azure Monitor pour machines virtuelles (version préliminaire)

Cet article décrit les problèmes connus avec Azure Monitor pour machines virtuelles, une solution dans Azure qui combine l’analyse de l’intégrité et des performances du système d’exploitation de machine virtuelle Azure. 

Voici les problèmes connus de la fonctionnalité Health (Intégrité) :

- La fonctionnalité Health (Intégrité) est activée pour toutes les machines virtuelles qui sont connectées à l’espace de travail Log Analytics. C’est également le cas quand une action débute et se termine dans une machine virtuelle unique.
- Lorsque vous désactivez la supervision d’une machine virtuelle à l’aide d’une méthode prise en charge et que vous essayez de la redéployer, le déploiement doit se faire dans le même espace de travail. Si vous utilisez un nouvel espace de travail et que vous essayez d’afficher l’état d’intégrité pour cette machine virtuelle, le comportement indiqué peut être anormal.
- Quand une machine virtuelle Azure est supprimée, elle reste affichée dans la vue de liste de machines virtuelles pendant trois à sept jours. Par ailleurs, le fait de cliquer sur l’état d’une machine virtuelle supprimée a pour effet d’ouvrir la vue **Diagnostics d’intégrité**, qui entre ensuite dans une boucle de chargement. Le fait de sélectionner le nom d’une machine virtuelle supprimée a pour effet d’ouvrir un panneau avec un message indiquant que la machine virtuelle a été supprimée.
- Il n’est pas possible de modifier la période et la fréquence des critères d’intégrité dans cette version. 
- Les critères d’intégrité ne peuvent pas être désactivés. 
- Après le déploiement, l’affichage des données dans le volet **Azure Monitor** > **Machines virtuelles** > **Intégrité** ou dans le volet **Ressource de machine virtuelle** > **Insights** peut prendre du temps.
- L’interface des diagnostics d’intégrité se met à jour plus rapidement que toutes les autres vues. Lorsque vous passez d’une vue à l’autre, il se peut que les informations connaissent un certain retard. 
- Le résumé des alertes, fourni avec Azure Monitor pour machines virtuelles, affiche uniquement les alertes liées à des problèmes d’intégrité qui ont été détectés avec les machines virtuelles supervisées.
- Le volet **Liste des alertes** de l’unique machine virtuelle et d’Azure Monitor affiche les alertes dont l’état du moniteur a été défini sur *activé* dans les 30 derniers jours. Les alertes ne sont pas configurables. Cependant, une fois que le volet **Liste d’alertes** s’ouvre, vous pouvez cliquer sur le résumé pour modifier la valeur de filtrage de l’état du moniteur et de l’intervalle de temps.
- Dans le volet **Liste des alertes**, nous vous recommandons de ne pas modifier les filtres **Type de ressource**, **Ressource** et **Service de moniteur**. Ils été configurés spécifiquement pour la solution. Cette vue de liste affiche plus de champs que celle de **Azure Monitor** > **Alertes**.   
- Dans les machines virtuelles Linux, la vue **Health Diagnostics (Diagnostics d’intégrité)** contient le nom de domaine complet de la machine virtuelle au lieu du nom de la machine virtuelle défini par l’utilisateur.
- L’arrêt des machines virtuelles entraîne la mise à jour de certains critères d’intégrité qui passent à l’état *critical (critique)* ou *healthy (sain)*. L’état de machine virtuelle net indique *critical (critique)*.
- La gravité des alertes d’intégrité ne peut pas être modifiée. Elle peut uniquement être activée ou désactivée. De plus, certains niveaux de gravité sont mis à jour en fonction de l’état du critère d’intégrité.
- Si vous modifiez un paramètre d’une instance de critère d’intégrité, toutes les instances de critère d’intégrité du même type sur la machine virtuelle sont modifiées. Par exemple, si le seuil de l’instance de critère d’intégrité de l’espace disque libre correspondant au disque logique C : est modifié, alors il s’applique à tous les autres disques logiques idco trouvés et supervisés de la même machine virtuelle.  
- Les seuils des critères d’intégrité ciblant une machine virtuelle Windows ne peuvent pas être modifiés, car leurs états d’intégrité sont déjà définis sur *en cours d’exécution* ou *disponible*. Dans le cas d’une requête émise à partir de l’[API de supervision de charge de travail](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager), l’état d’intégrité présente la valeur **LessThan** ou **GreaterThan** pour *comparisonOperator* avec un seuil (*threshold*) de **4** pour le service ou l’entité si :
   - Le service DNS Client Service Health n’est pas en cours d’exécution. 
   - Le service DHCP Client Service Health n’est pas en cours d’exécution. 
   - Le service RPC Service Health n’est pas en cours d’exécution. 
   - Le service Windows Firewall Service Health n’est pas en cours d’exécution.
   - Le service Windows Event Log Service Health n’est pas en cours d’exécution. 
   - Le service Server Service Health n’est pas en cours d’exécution. 
   - Le service Windows Remote Management Service Health n’est pas en cours d’exécution. 
   - Le disque logique est indisponible du fait d’une erreur ou de l’altération du système de fichiers.

- Les seuils des critères d’intégrité Linux suivants ne peuvent pas être modifiés, car leur état d’intégrité est déjà défini sur *true*. Pour l’état d’intégrité, *comparisonOperator* indique **LessThan** avec un seuil (*threshold*) de **1** dans le cas d’une requête émise à partir de l’API de supervision de charge de travail pour l’entité en fonction de son contexte :
   - État du disque logique – Le disque logique n’est pas en ligne/disponible
   - État du disque – Le disque n’est pas en ligne/disponible
   - État de la carte réseau - La carte réseau est désactivée  

- Le critère d’intégrité *Utilisation totale de l’UC* dans Windows affiche un seuil **non égal à 4** dans le portail et l’API de supervision de charge de travail. Le seuil est atteint lorsque le critère *Utilisation totale de l’UC* est supérieure à 95 %, et la longueur de la file d’attente du système est supérieure à 15. Ce critère d’intégrité ne peut pas être modifié dans cette version. 
- La prise en compte des modifications de configuration, comme la mise à jour d’un seuil, peut prendre jusqu'à 30 minutes, même si la mise à jour du portail ou de l’API de supervision de charge de travail est immédiate. 
- Les critères d’intégrité au niveau des processeurs individuels et des processeurs logiques ne sont pas disponibles dans Windows. Seule l’utilisation totale de l’UC est disponible pour les machines virtuelles Windows. 
- Les règles d’alerte définies pour chaque critère d’intégrité ne sont pas affichées dans le portail Azure. Elles ne peuvent être activées ou désactivées qu’à partir de l’[API de supervision de charge de travail](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager). 
- Il n’est pas possible d’affecter un [groupe d’actions Azure Monitor](../../azure-monitor/platform/action-groups.md) pour des alertes d’intégrité dans le portail Azure. Vous pouvez utiliser l’API de paramètre de notification uniquement pour configurer le déclenchement d’un groupe d’actions chaque fois qu’une alerte d’intégrité est déclenchée. Actuellement, il est possible d’affecter des groupes d’action pour une machine virtuelle, si bien que toutes les *alertes d’intégrité* déclenchées sur la machine virtuelle déclenchent les mêmes groupes d’actions. Contrairement aux alertes Azure classiques, il ne peut pas exister un groupe d’action distinct pour chaque règle d’alerte d’intégrité. De plus, seuls les groupes d’actions configurés pour adresser des notifications par e-mail ou SMS sont pris en charge quand des alertes d’intégrité sont déclenchées. 

## <a name="next-steps"></a>Étapes suivantes
Pour découvrir les exigences et les méthodes relatives à l’activation de la supervision de vos machines virtuelles, consultez [Déployer Azure Monitor pour machines virtuelles](vminsights-onboard.md).
