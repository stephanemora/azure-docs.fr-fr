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
ms.date: 12/26/2018
ms.author: magoedte
ms.openlocfilehash: c329e1fa80c6647bb78b11917ecd012461e62ea4
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790501"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Problèmes connus avec Azure Monitor pour machines virtuelles (version préliminaire)

Cet article décrit les problèmes connus avec Azure Monitor pour machines virtuelles, une solution dans Azure qui combine l’analyse de l’intégrité et des performances du système d’exploitation de machine virtuelle Azure. 

Voici les problèmes connus de la fonctionnalité Health (Intégrité) :

- La fonctionnalité Health (Intégrité) est activée pour toutes les machines virtuelles qui sont connectées à l’espace de travail Log Analytics. C’est également le cas quand une action débute et se termine dans une machine virtuelle unique.
- Pour les machines virtuelles Linux, le titre de la page répertoriant les critères d’intégrité pour une vue de machine virtuelle porte le nom de domaine entier de celle-ci au lieu du nom défini par l’utilisateur.  
- Lorsque vous désactivez la supervision d’une machine virtuelle à l’aide d’une méthode prise en charge et que vous essayez de la redéployer, le déploiement doit se faire dans le même espace de travail. Si vous utilisez un nouvel espace de travail et que essayez d’afficher l’état d’intégrité pour cette machine virtuelle, il se peut que le comportement soit anormal.
- Si une machine virtuelle Azure est retirée ou supprimée, elle reste affichée dans la liste de machines virtuelles pendant un certain temps. Par ailleurs, le fait de cliquer sur l’état d’une machine virtuelle supprimée a pour effet d’ouvrir la vue **Diagnostics d’intégrité**, qui entre ensuite dans une boucle de chargement. Le fait de sélectionner le nom d’une machine virtuelle supprimée a pour effet d’ouvrir un panneau avec un message indiquant que la machine virtuelle a été supprimée.
- Il n’est pas possible de modifier la période et la fréquence des critères d’intégrité dans cette version. 
- Les critères d’intégrité ne peuvent pas être désactivés. 
- Après le déploiement, l’affichage des données dans le volet **Azure Monitor** > **Machines virtuelles** > **Intégrité** ou dans le volet **Ressource de machine virtuelle** > **Insights** peut prendre du temps.
- L’interface des diagnostics d’intégrité se met à jour plus rapidement que toutes les autres vues. Lorsque vous passez d’une vue à l’autre, il se peut que les informations connaissent un certain retard. 
- Le récapitulatif des alertes inclus dans Azure Monitor pour les machines virtuelles affiche uniquement les alertes résultant de problèmes d’intégrité détectés avec des machines virtuelles Azure supervisées.
- L’arrêt des machines virtuelles entraîne la mise à jour de certains critères d’intégrité qui passent à l’état *critical (critique)* ou *healthy (sain)*. L’état de machine virtuelle net indique *critical (critique)*.
- La gravité d’une alerte d’intégrité n’est pas modifiable, elle ne peut qu’être activée ou désactivée. De plus, certains niveaux de gravité sont mis à jour en fonction de l’état du critère d’intégrité.   
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

- La prise en compte des modifications de configuration, comme la mise à jour d’un seuil, peut prendre jusqu'à 30 minutes, même si la mise à jour du portail ou de l’API de supervision de charge de travail est immédiate. 
- Les critères d’intégrité au niveau des processeurs individuels et des processeurs logiques ne sont pas disponibles dans Windows. Seule l’utilisation totale de l’UC est disponible pour les machines virtuelles Windows. 
- Les règles d’alerte définies pour chaque critère d’intégrité ne sont pas affichées dans le portail Azure. Elles ne peuvent être activées ou désactivées qu’à partir de l’[API de supervision de charge de travail](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). 
- Il n’est pas possible d’affecter un [groupe d’actions Azure Monitor](../../azure-monitor/platform/action-groups.md) pour des alertes d’intégrité dans le portail Azure. Vous pouvez utiliser l’API de paramètre de notification uniquement pour configurer le déclenchement d’un groupe d’actions chaque fois qu’une alerte d’intégrité est déclenchée. Actuellement, il est possible d’affecter des groupes d’action pour une machine virtuelle, si bien que toutes les *alertes d’intégrité* déclenchées sur la machine virtuelle déclenchent les mêmes groupes d’actions. Contrairement aux alertes Azure classiques, il ne peut pas exister un groupe d’action distinct pour chaque règle d’alerte d’intégrité. De plus, seuls les groupes d’actions configurés pour adresser des notifications par e-mail ou SMS sont pris en charge quand des alertes d’intégrité sont déclenchées. 

## <a name="next-steps"></a>Étapes suivantes
Pour découvrir les exigences et les méthodes relatives à l’activation de la supervision de vos machines virtuelles, consultez [Déployer Azure Monitor pour machines virtuelles](vminsights-onboard.md).
