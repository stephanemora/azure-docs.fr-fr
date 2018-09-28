---
title: Problèmes connus avec Azure Monitor pour machines virtuelles | Microsoft Docs
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
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: c03adc239ea7025fe154db315daa17b26f8237f1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980195"
---
# <a name="known-issues-with-azure-monitor-for-vms"></a>Problèmes connus avec Azure Monitor pour machines virtuelles

Les problèmes suivants concernent la fonctionnalité Health (Intégrité) d’Azure Monitor pour machines virtuelles :

- Il n’est pas possible de modifier la période et la fréquence des critères d’intégrité dans cette version. 
- Les critères d’intégrité ne peuvent pas être désactivés. 
- Après l’intégration, un certain temps peut s’écouler avant que les données ne soient affichées dans Azure Monitor -> Virtual Machines -> Health (Azure Monitor -> Machines virtuelles -> Intégrité) ou dans le panneau des ressources de la machine virtuelle -> Insights
- Les mises à jour de Health Diagnostics (Diagnostics d’intégrité) sont plus rapides que celles des autres affichages, ce qui peut entraîner des retards d’information en passant d’une fenêtre à l’autre  
- Les résumés d’alertes dans la partie Health (Intégrité) d’Azure Monitor pour machines virtuelles ne concernent que les alertes liées à des problèmes d’intégrité détectés sur les machines virtuelles Azure supervisées.
- La fenêtre **Liste des alertes** de l’unique machine virtuelle et d’Azure Monitor affiche les alertes dont l’état du moniteur a été défini « activé » dans les 30 derniers jours.  Elles ne sont pas configurables. Cependant, une fois que la page d’affichage de la **Liste d’alertes**est lancée, vous pouvez modifier la valeur de filtrage de l’état du moniteur et de l’intervalle de temps en cliquant sur le résumé.
- Sur la page **Liste des alertes**, nous vous conseillons de ne pas modifier les filtres **Type de ressource**, **Ressource** et **Service de supervision** car ils ont été spécifiquement configurés pour la solution (cette vue de liste présente quelques champs supplémentaires par rapport à Azure Monitor -> Fenêtre de la liste des alertes).    
- Dans les machines virtuelles Linux, la fenêtre **Diagnostic d’intégrité** (Health Diagnostics) contient le nom de domaine complet de la machine virtuelle au lieu du nom de la machine virtuelle défini par l’utilisateur.
- La fermeture des machines virtuelles actualise certains de ses critères d’intégrité en les faisant passer à un état critique et d’autres à un état intègre avec un état net de la machine virtuelle à un état critique.
- La gravité d’une alerte d’intégrité n’est pas modifiable, elle ne peut qu’être activée ou désactivée.  De plus, certains niveaux de gravité sont mis à jour en fonction de l’état du critère d’intégrité.
- La modification des paramètres d’une instance de critère d’intégrité entraîne la modification du même paramètre pour toutes les instances de critère d’intégrité de même type dans la machine virtuelle. Par exemple, si le seuil de l’instance de critère d’intégrité de l’espace disque libre correspondant au disque logique C : est modifié, alors il s’applique à tous les autres disques logiques idco trouvés et supervisés de la même machine virtuelle.   
- Les seuils de certains critères d’intégrité de Windows ne sont pas modifiables, comme DNS Client Service Health, car leur état d’intégrité est déjà verrouillé à l’état **en cours d’exécution**, **disponible** du service ou de l’entité suivant le contexte.  Au lieu de cela, la valeur est représentée par le chiffre 4 et sera convertie en la chaîne d’affichage réelle dans une version ultérieure.  
- Les seuils de certains critères d’intégrité de Linux ne sont pas modifiables, comme Logical Disk Health, car ils sont déjà définis de façon à se déclencher dans un état non intègre.  Ils indiquent si quelque chose est en ligne, hors ligne, activé ou désactivé. De plus, ils sont représentés et indiquent la même chose en affichant la valeur 1 ou 0.
- Lorsque l’on actualise le filtre Groupe de ressources dans un groupe de ressources en utilisant la fenêtre Azure Monitor -> Machines virtuelles -> Intégrité -> Toutes les listes avec l’abonnement et le groupe de ressources présélectionnés, la fenêtre des listes affiche **aucun résultat**.  Retournez à l’onglet Azure Monitor -> Machines virtuelles -> Intégrité, sélectionnez l’abonnement et le groupe de ressources souhaités, puis accédez à la fenêtre des listes.
