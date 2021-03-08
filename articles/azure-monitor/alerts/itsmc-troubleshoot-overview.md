---
title: Résoudre les problèmes dans ITSMC
description: Découvrez comment résoudre les problèmes courants dans Connecteur de gestion des services informatiques.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a01005231b5d775f79555ec10dedeb3f30b3426a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737203"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>Résoudre les problèmes liés à Connecteur de gestion des services informatiques

Cet article décrit des problèmes courants dans Connecteur de gestion des services informatiques (ITSMC) et la manière de les résoudre.

Azure Monitor vous avertit de façon proactive par des alertes lorsque des conditions significatives sont détectées dans vos données de surveillance. Ces alertes vous aident à identifier et à résoudre les problèmes avant que les utilisateurs de votre système ne les remarquent.

Vous pouvez choisir la façon dont vous souhaitez recevoir les alertes. Vous pouvez choisir par e-mail, par SMS ou par webhook, voire automatiser une solution. 

Une autre solution consiste à être informé par l’intermédiaire d’ITSMC. ITSMC vous donne la possibilité d’envoyer des alertes à un système de tickets externe tel que ServiceNow.

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>Utiliser le tableau de bord pour analyser les données d’incident et de demande de modification

En fonction de la configuration choisie lors de l’établissement d’une connexion, ITSMC peut synchroniser jusqu’à 120 jours de données d’incidents et de demande de changement. Pour obtenir le schéma d’enregistrement du journal pour ces données, consultez l’article [Données synchronisées à partir de votre produit ITSM](./itsmc-synced-data.md).

Vous pouvez visualiser les données d’incident et de demande de changement à l’aide du tableau de bord ITSMC :

![Capture d’écran montrant le tableau de bord ITSMC.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Le tableau de bord fournit également des informations sur l’état du connecteur. Vous pouvez utiliser ces informations comme point de départ pour analyser les problèmes liés aux connexions. Pour plus d’informations, consultez [Recherche d’erreurs à l’aide du tableau de bord](./itsmc-dashboard.md).

## <a name="use-service-map-to-visualize-incidents"></a>Utiliser Service Map pour visualiser les incidents

Vous pouvez également visualiser les incidents synchronisés avec les ordinateurs concernés dans Service Map.

La solution Carte de service détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Elle vous permet d’afficher les serveurs comme vous vous les représentez, c’est-à-dire comme des systèmes interconnectés qui fournissent des services critiques. 

Service Map affiche les connexions entre les serveurs, les processus et les ports sur n’importe quelle architecture connectée par TCP. Aucune configuration autre que l’installation d’un agent n’est exigée. Pour plus d’informations, consultez [Utilisation de Service Map](../vm/service-map.md).

Si vous utilisez Service Map, vous pouvez afficher les éléments de service d’assistance créés dans les solutions de gestion des services informatiques (ITSM), comme illustré dans cet exemple :

![Capture d’écran montrant l’écran Log Analytics.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>Résoudre les problèmes

Les sections suivantes identifient les symptômes courants, les causes possibles et les résolutions. 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>Une connexion au système ITSM échoue et vous recevez un message « Erreur lors de l’enregistrement de la connexion »

**Cause** : La cause peut être l’une des options suivantes :

* Les informations d’identification sont incorrectes.
* Les privilèges sont insuffisants.
* Pour les connexions Service Manager  : l’application web n’a pas été déployée correctement.

**Résolution** :

* Pour les connexions ServiceNow, Cherwell et Provance :
  * Vérifiez que vous avez correctement entré le nom d’utilisateur, le mot de passe, l’ID client et la clé secrète client pour chacune des connexions.  
  * Pour ServiceNow, veillez à disposer de [privilèges suffisants](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role) dans le produit ITSM correspondant.

* Pour les connexions Service Manager :  
  * Vérifiez que l’application web est correctement déployée et que la connexion hybride est créée. Pour vérifier que la connexion est établie avec l’ordinateur Service Manager local, accédez à l’URL de l’application web, comme décrit dans la [documentation concernant l’établissement d’une connexion hybride](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

### <a name="duplicate-work-items-are-created"></a>des éléments de travail en double sont créés

**Cause** : La cause peut être l’une de ces deux options :

* Plus d’une action ITSM est définie pour l’alerte.
* L’alerte est résolue.

**Résolution** : deux solutions sont possibles :

* Assurez-vous de disposer d’un seul groupe d’actions ITSM par alerte.
* ITSMC ne prend pas en charge les mises à jour de l’état des éléments de travail correspondants quand une alerte est résolue. Créez un élément de travail résolu.

### <a name="work-items-are-not-created"></a>des éléments de travail ne sont pas créés

**Cause** : Il peut y avoir plusieurs raisons à l’apparition de ce symptôme :

* Le code a été modifié du côté ServiceNow.
* Les autorisations sont mal configurées.
* Les limites de débit de ServiceNow sont trop élevées ou trop basses.
* Un jeton d’actualisation a expiré.
* ITSMC a été supprimé.

**Résolution** : Vérifiez le [tableau de bord](itsmc-dashboard.md) et passez en revue les erreurs dans la section relative à l’état du connecteur. Passez ensuite en revue les [erreurs courantes et leurs résolutions](itsmc-dashboard-errors.md).

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>Vous ne pouvez pas créer d’action ITSM pour un groupe d’actions

**Cause** : Une instance d’ITSMC nouvellement créée n’a pas encore terminé la synchronisation initiale.

**Résolution** : Passez en revue les [erreurs courantes et leurs résolutions](itsmc-dashboard-errors.md).

### <a name="sync-connection"></a>Synchroniser la connexion 

**Cause** : Il peut y avoir plusieurs raisons à l’apparition de ce symptôme :

* Les modèles ne sont pas affichés dans le cadre de la définition de l’action.
* Aucun incident/événement n’est créé dans ServiceNow.

**Résolution**: [Synchronisez le connecteur](itsmc-resync-servicenow.md).
