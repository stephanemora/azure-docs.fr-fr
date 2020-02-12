---
title: Azure VMware Solutions (AVS) - Superviser l’activité du cloud privé AVS
description: Décrit les informations disponibles sur les activités dans l’environnement Azure VMware Solution by AVS, y compris les alertes, les événements, les tâches et les audits.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5d6fd1b92db62ab7cc9edd47c601910b8148bb95
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019670"
---
# <a name="monitor-vmware-solutions-avs-activity"></a>Superviser l’activité VMware Solutions (AVS)

Les journaux d’activité AVS donnent un aperçu des opérations effectuées sur le portail AVS. La liste comprend les alertes, les événements, les tâches et les audits. Utilisez les journaux d’activité pour déterminer les opérations effectuées, quand et par qui. Les journaux d’activité n’incluent pas les opérations de lecture effectuées par un utilisateur.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Accédez au portail AVS

Accédez au [portail AVS](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Informations sur l'activité

Pour accéder aux pages Activité, sélectionnez **Activité** dans le menu latéral.

![Vue d’ensemble de la page Activité](media/activity-page-overview.png)

Pour afficher des détails sur une activité dans la page Activité, sélectionnez-la. Un panneau de détails s’ouvre à droite. Les actions dans le volet dépendent du type d’activité. Cliquez sur **X** pour fermer le panneau.

Cliquez sur un en-tête de colonne pour trier l’affichage. Vous pouvez filtrer les colonnes pour des valeurs spécifiques à afficher. Téléchargez un rapport d’activité en cliquant sur l’icône **Télécharger au format CSV**.

## <a name="alerts"></a>Alertes

Les alertes sont des notifications de toutes les activités significatives dans votre environnement AVS. Les alertes incluent les événements qui affectent l’accès à la facturation ou aux utilisateurs.

Pour confirmer la réception des alertes et les supprimer de la liste, sélectionnez-en une ou plusieurs dans la liste, puis cliquez sur **Confirmer la réception**.

Les colonnes d’informations suivantes sont disponibles pour les alertes. Cliquez sur **Modifier les colonnes** et sélectionnez les colonnes que vous souhaitez afficher.

| Colonne | Description |
------------ | ------------- |
| Type d’alerte | Catégorie de l’alerte.|
| Temps | Heure à laquelle l’alerte s’est produite. |
| severity | Signification de l’alerte.|
| Nom de la ressource | Nom affecté à la ressource, tel que le nom du cloud privé AVS. |
| Type de ressource | Catégorie de la ressource : Cloud privé AVS, rack de cloud. |
| ID de ressource | Identificateur de la ressource. |
| Description | Description de ce qui a déclenché l’alerte. |
| Reconnu | Indique si la réception de l’alerte a été confirmée. |

## <a name="events"></a>Événements

Les événements montrent l’activité de l’utilisateur et du système sur le portail AVS. La page Événements affichent l’activité associée à une ressource spécifique et la gravité de l’impact.

Les colonnes d’informations suivantes sont disponibles pour les alertes. Cliquez sur **Modifier les colonnes** et sélectionnez les colonnes que vous souhaitez afficher.

| Colonne | Description |
------------ | ------------- |
| Temps | Date et heure auxquelles l’événement s’est produit. |
| Type d'événement | Code numérique identifiant l’événement. |
| severity | Gravité de l'événement.|
| Nom de la ressource | Nom affecté à la ressource, tel que le nom du cloud privé AVS. |
| Type de ressource | Catégorie de la ressource : Cloud privé AVS, rack de cloud. |
| Description | Description de ce qui a déclenché l’alerte. |

## <a name="tasks"></a>Tâches

Les tâches sont des activités de cloud privé AVS censées durer 30 secondes ou plus. (Les activités censées prendre moins de 30 secondes sont signalées uniquement en tant qu’événements.) Ouvrez les pages Tâches pour suivre la progression des tâches pour votre cloud privé AVS.

Les colonnes d’informations suivantes sont disponibles pour les alertes. Cliquez sur **Modifier les colonnes** et sélectionnez les colonnes que vous souhaitez afficher.

| Colonne | Description |
------------ | ------------- |
| ID de la tâche | Identificateur unique de la tâche. |
| Opération | Action exécutée par la tâche. |
| Utilisateur | Utilisateur affecté pour terminer la tâche. |
| Nom de la ressource | Nom attribué à la ressource. |
| Type de ressource | Catégorie de la ressource : Cloud privé AVS, rack de cloud. |
| ID de ressource | Identificateur de la ressource. |
| Démarrer | Heure de début de la tâche. |
| End | Heure de fin de la tâche. |
| Statut | État actuel de la tâche. |
| Temps écoulé | Durée d’exécution de la tâche (si elle est terminée) ou en cours (si l’exécution n’est pas terminée). |
| Description | Description de la tâche. |

## <a name="audit"></a>Audit

Les journaux d’audit assurent le suivi de l’activité des utilisateurs. Vous pouvez utiliser les journaux d’audit pour surveiller l’activité de tous les utilisateurs.

Les colonnes d’informations suivantes sont disponibles pour les alertes. Cliquez sur **Modifier les colonnes** et sélectionnez les colonnes que vous souhaitez afficher.

| Colonne | Description |
------------ | ------------- |
| Temps | Heure de l’entrée d’audit. |
| Opération | Action exécutée par la tâche. |
| Utilisateur | Utilisateur à qui la tâche est affectée. |
| Nom de la ressource | Nom attribué à la ressource. |
| Type de ressource | Catégorie de la ressource : Cloud privé AVS, rack de cloud. |
| ID de ressource | Identificateur de la ressource. |
| Résultats | Résultat de l’activité, par exemple **Réussie**. |
| Durée | Durée d’exécution de la tâche. |
| Description | Description de l’action. |

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser des machines virtuelles VMware sur Azure](quickstart-create-vmware-virtual-machine.md)
* En savoir plus sur les [clouds privés AVS](cloudsimple-private-cloud.md)
