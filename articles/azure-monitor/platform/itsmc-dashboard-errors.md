---
title: Erreurs courantes
description: Ce document contient des informations sur des erreurs courantes apparaissant dans le tableau de bord.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: c3e6408a6a3473380edbe2966eba41970bd73562
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762764"
---
# <a name="errors-in-the-connector-status-section"></a>Erreurs dans la section État du connecteur

La section sous forme de liste État du connecteur dans le tableau de bord contient des erreurs qui peuvent vous aider à résoudre des problèmes dans votre connecteur ITSM.

## <a name="status-common-errors"></a>Corriger des erreurs courantes

Cette section contient les erreurs courantes qui apparaissent dans la section de l’état du connecteur, et indique comment les résoudre :

* **Erreur** : « Réponse inattendue de ServiceNow avec le code d’état de réussite. Response: { "import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "result": [ { "transform_map": "OMS Incident", "table": "incident", "status": "error", "error_message": "{enregistrement cible introuvable|Table non valide|Table intermédiaire non valide" }"

    **Cause** : Une telle erreur est retournée à partir de ServiceNow quand :
  * Un script personnalisé déployé dans une instance ServiceNow a pour effet que des incidents sont ignorés.
  * Le code « OMS Integrator App » lui-même a été modifié côté ServiceNow, par exemple le script onBefore.

  **Résolution** : Désactivez les scripts personnalisés ou les modifications de code.

* **Erreur**: "{"erreur":{"message":"Échec de l’opération","détail":"Échec de la mise à jour de l’exception ACL en raison des contraintes de sécurité "}"

    **Cause** : Configuration erronée des autorisations ServiceNow

    **Résolution** : Vérifiez que tous les rôles ont été correctement attribués comme [spécifié](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).

* **Erreur** : « Une erreur s’est produite lors de l’envoi de la demande. »

    **Cause** : « Instance ServiceNow indisponible »

    **Résolution** : Vérifiez que votre instance dans ServiceNow n’a pas été supprimée ou n’est pas indisponible.

* **Erreur** : « ServiceDeskHttpBadRequestException: StatusCode=429 »

    **Cause** : Les limites de débit de ServiceNow sont trop élevées/basses.

    **Résolution** : Augmentez ou annulez les limites de débit dans l’instance ServiceNow comme expliqué [ici](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html).

* **Erreur** : « AccessToken et RefreshToken non valides. L’utilisateur doit s’authentifier à nouveau. »

    **Cause** : Le jeton d'actualisation a expiré.

    **Résolution** : Synchronisez le connecteur ITSM pour générer un nouveau jeton d’actualisation comme expliqué [ici](./itsmc-resync-servicenow.md).

* **Erreur** : « Impossible de créer/mettre à jour l’élément de travail pour l’alerte {alertName}. Le connecteur ITSM {connectionIdentifier} n’existe pas ou a été supprimé. »

    **Cause** : Le connecteur ITSM a été supprimé.

    **Résolution** : La connecteur ITSM a été supprimé, mais des groupes d’actions ITSM définis y sont toujours associés. Vous avez 2 options pour résoudre ce problème :
  * Rechercher, puis désactiver ou supprimer ces groupes d’actions
  * [Reconfigurez le groupe d’actions](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) pour utiliser un connecteur ITSM existant.
  * [Créez un connecteur ITSM](./itsmc-definition.md#create-an-itsm-connection) et [reconfigurez le groupe d’actions pour l’utiliser](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="ui-common-errors"></a>Erreurs courantes de l’interface utilisateur

* **Erreur**: « Un problème est survenu. Impossible d’accéder aux détails de la connexion. » Cette erreur s’affiche quand le client définit un groupe d’actions ITSM.

    **Cause** : Le connecteur ITSM nouvellement créée n’a pas encore terminé la synchronisation initiale.

    **Résolution** : Quand un nouveau connecteur ITSM est créé, le connecteur ITSM démarre la synchronisation d’informations à partir du système ITSM, telles que des modèles d’élément de travail et des éléments de travail. Synchronisez le connecteur ITSM pour générer un nouveau jeton d’actualisation comme expliqué [ici](./itsmc-resync-servicenow.md).
