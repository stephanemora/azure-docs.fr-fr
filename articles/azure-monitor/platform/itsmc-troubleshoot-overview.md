---
title: Résolution des problèmes liés au connecteur ITSM
description: Résolution des problèmes liés au connecteur de gestion des services informatiques
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: e43c5fb36c5395e12fd0b9c2c67b787a1137f5d0
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761981"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Résolution des problèmes liés au connecteur ITSM

Cet article décrit des problèmes courants dans le connecteur ITSM et la manière de les résoudre.

Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes avant que les utilisateurs de votre système ne les remarquent. Pour plus d’informations sur les alertes, consultez Vue d’ensemble des alertes dans Microsoft Azure.
Le client peut choisir la manière dont il souhaite être informé de l’alerte, que ce soit par e-mail, SMS, webhook, ou d’automatiser une solution. Une autre option de notification consiste à utiliser ITSM.
ITSM vous donne la possibilité d’envoyer les alertes à un système de tickets externe tel que ServiceNow.

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualiser et analyser les données d’incident et de demande de modification

En fonction de la configuration choisie lors de l’établissement d’une connexion, ITSMC peut synchroniser jusqu’à 120 jours de données d’incidents et de demande de changement. Le schéma d’enregistrement de journal pour ces données est fourni dans la [section d’informations supplémentaires](./itsmc-synced-data.md) de cet article.

Vous pouvez visualiser les données d’incident et de demande de changement à l’aide du tableau de bord ITSMC :

![Capture d’écran montrant le tableau de bord ITSMC.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Le tableau de bord fournit également des informations sur l’état du connecteur. Vous pouvez les utiliser comme point de départ pour analyser les problèmes liés aux connexions.

Pour obtenir plus d’informations sur l’examen du tableau de bord, consultez [Recherche d’erreurs à l’aide du tableau de bord](./itsmc-dashboard.md).

### <a name="service-map"></a>Carte de service

Vous pouvez également visualiser les incidents synchronisés avec les ordinateurs concernés dans Service Map.

La solution Carte de service détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Elle vous permet d’afficher les serveurs comme vous vous les représentez, c’est-à-dire comme des systèmes interconnectés qui fournissent des services critiques. Service Map affiche les connexions entre les serveurs, les processus et les ports sur n’importe quelle architecture connectée par TCP. Aucune configuration autre que l’installation d’un agent n’est exigée. Pour plus d’informations, consultez [Utilisation de Service Map](../insights/service-map.md).

Si vous utilisez Service Map, vous pouvez afficher les éléments de service d’assistance créés dans les solutions ITSM, comme indiqué ici :

![Capture d’écran montrant l’écran Log Analytics.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="common-symptoms---how-should-it-be-resolved"></a>Symptômes courants : comment les résoudre ?

La liste ci-dessous contient des symptômes courants et montre comment les résoudre :

* **Symptôme** : Si une connexion au système ITSM échoue et que le message **Erreur lors de l’enregistrement de la connexion** s’affiche.

    **Cause**: la cause peut être l’une des options suivantes :
    * Informations d’identification incorrectes
     * Privilèges insuffisants
     * L’application web doit être déployée correctement

    **Résolution** :
    * Pour les connexions ServiceNow, Cherwell et Provance :
        * Vérifiez que vous avez correctement entré le nom d’utilisateur, le mot de passe, l’ID client et le secret client pour chacune des connexions.  
        * Pour ServiceNow : Veillez à disposer de privilèges suffisants dans le produit ITSM correspondant afin d’établir la connexion comme [indiqué](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).
  * Pour les connexions Service Manager :  
      * Vérifiez que l’application web est correctement déployée et que la connexion hybride est créée. Pour vérifier que la connexion est établie avec l’ordinateur Service Manager local, accédez à l’URL de l’application web, comme décrit dans la documentation concernant l’établissement d’une [connexion hybride](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  
* **Symptôme** : des éléments de travail en double sont créés

    **Cause**: la cause peut être l’une des deux options suivantes :
    * Plus d’une action ITSM est définie pour l’alerte.
    * L'alerte est résolue.

    **Résolution** : deux solutions sont possibles :
    * Assurez-vous de disposer d’un seul groupe d’actions ITSM par alerte.
    * Le Connecteur ITSM ne prend pas en charge la mise à jour de l’état des éléments de travail correspondants quand une alerte est résolue. Un nouvel élément de travail résolu est créé.
* **Symptôme** : des éléments de travail ne sont pas créés

    **Cause** : il peut y avoir deux raisons à l’apparition de ce symptôme :
    * Modification du code côté ServiceNow
    * Configuration erronée des autorisations
    * Les limites de débit de ServiceNow sont trop élevées/basses
    * Le jeton d'actualisation a expiré
    * Le connecteur ITSM a été supprimé

    **Résolution** : vous pouvez consulter le [tableau de bord](itsmc-dashboard.md) et examiner les erreurs dans la section relative à l’état du connecteur. Examinez les [erreurs courantes](itsmc-dashboard-errors.md) et découvrez comment les résoudre.

* **Symptôme** : impossible de créer l’action ITSM pour le groupe d’actions

    **Cause** : le connecteur ITSM nouvellement créée n’a pas encore terminé la synchronisation initiale.

    **Résolution**: vous pouvez examiner les [erreurs d’interface utilisateur courantes](itsmc-dashboard-errors.md#ui-common-errors) et découvrir comment les résoudre.