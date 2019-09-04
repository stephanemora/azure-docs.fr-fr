---
title: Approvisionnement des journaux dans le portail Azure Active Directory (version préliminaire) | Microsoft Docs
description: Présentation des rapports d’activité d’approvisionnement dans le portail Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/29/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74b2d80d21822758991ad8b3bc1d3bb44e2257f4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129513"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Approvisionnement des rapports dans le portail Azure Active Directory (version préliminaire)

L’architecture de création de rapports dans Azure Active Directory (Azure AD) comprend les composants suivants :

- **Activité** 
    - **Connexions** – Il s’agit d’informations sur l’utilisation des applications managées et les activités de connexion des utilisateurs.
    - **Journaux d’audit** - [Journaux d’audit](concept-audit-logs.md) – Fournit des informations sur les activités du système liées aux utilisateurs et à la gestion des groupes, les applications gérées et les activités de répertoire.
    - **Approvisionnement des journaux** : fournit une activité système sur les utilisateurs, les groupes et les rôles qui sont approvisionnés par le service d’approvisionnement Azure AD. 

- **Sécurité** 
    - **Connexions risquées** : une [connexion risquée](concept-risky-sign-ins.md) est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur.
    - **Utilisateurs avec indicateur de risque** : un [utilisateur à risque](concept-user-at-risk.md) correspond à un indicateur de compte d’utilisateur susceptible d’être compromis.

Cette rubrique présente une vue d’ensemble du rapport d’approvisionnement.

## <a name="prerequisites"></a>Prérequis

### <a name="who-can-access-the-data"></a>Qui peut accéder aux données ?
* Utilisateurs des rôles Administrateur de la sécurité, Lecteur de sécurité, Lecteur de rapports, Administrateur d’application et Administrateur d’application cloud
* Les administrateurs généraux


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>De quelle licence Azure AD avez-vous besoin pour accéder aux activités d’approvisionnement ?

Votre client doit avoir une licence Azure AD Premium associée pour afficher tous les rapports d’activités d’approvisionnement. Consultez [Bien démarrer avec Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) pour mettre à niveau votre édition d’Azure Active Directory. 

## <a name="provisioning-logs"></a>Journaux d’approvisionnement

Les journaux d’approvisionnement fournissent des réponses aux questions suivantes :

* Quels groupes ont été créés avec succès dans ServiceNow ?
* Comment les rôles ont été importés à partir d’Amazon Web Services ?
* Quels utilisateurs n’ont pas été créés avec succès dans DropBox ?

Vous pouvez accéder aux journaux d’approvisionnement en sélectionnant **Provisionner des journaux** dans la section **Surveillance** du panneau **Azure Active Directory** dans le [Portail Azure](https://portal.azure.com). L’affichage de certains enregistrements d’approvisionnement dans le portail peut prendre jusqu’à deux heures.

![Approvisionnement des journaux](./media/concept-provisioning-logs/access-provisioning-logs.png "Approvisionnement des journaux")


Un journal d’approvisionnement comporte un affichage de liste par défaut qui indique :

- L’identité
- L’action
- Le système source
- Le système cible
- Le statut
- La date


![Colonnes par défaut](./media/concept-provisioning-logs/default-columns.png "Colonnes par défaut")

Vous pouvez personnaliser le mode Liste en cliquant sur **Colonnes** dans la barre d’outils.

![Sélecteur de colonnes](./media/concept-provisioning-logs/column-chooser.png "Sélecteur de colonnes")

Cela vous permet d’afficher des champs supplémentaires, ou de supprimer des champs qui sont déjà affichés.

![Colonnes disponibles](./media/concept-provisioning-logs/available-columns.png "Colonnes disponibles")

Sélectionnez un élément dans la vue sous forme de liste pour obtenir des informations plus détaillées.

![Informations détaillées](./media/concept-provisioning-logs/detailed-information.png "Informations détaillées")


## <a name="filter-provisioning-activities"></a>Filtrer les activités d’approvisionnement

Pour limiter les données transmises à un niveau qui vous convient, vous pouvez filtrer les données d’approvisionnement à l’aide des champs par défaut suivants. Notez que les valeurs des filtres sont renseignées dynamiquement en fonction de votre locataire. Si, par exemple, vous n’avez pas d’événements de création dans votre locataire, il n’y aura pas d’option de filtre à créer.

- Identité
- Action
- Système source
- Système cible
- Statut
- Date


![Filtre](./media/concept-provisioning-logs/filter.png "Filtre")

Le filtre **Identité** vous permet de spécifier le nom ou l’identité qui vous intéresse. Cette identité peut être un utilisateur, un groupe, un rôle ou un autre objet. Vous pouvez effectuer une recherche par nom ou ID de l’objet. L’ID varie selon le scénario. Par exemple, lors de l’approvisionnement d’un objet d’Azure AD vers SalesForce, l’ID source est l’ID d’objet de l’utilisateur dans Azure AD, tandis qu’IDCible est l’ID de l’utilisateur dans Salesforce. Lors de l’approvisionnement de Workday vers Active Directory, l’ID source est l’ID employé du travailleur Workday. Notez qu’il se peut que le nom de l’utilisateur ne soit pas toujours présent dans la colonne d’identité. Il y aura toujours un ID. 

Le filtre **Système source** vous permet de spécifier l’emplacement à partir duquel l’identité est approvisionnée. Par exemple, lors de l’approvisionnement d’un objet d’Azure AD vers ServiceNow, le système source est Azure AD. 

Le filtre **Système cible** vous permet de spécifier l’emplacement où l’identité est approvisionnée. Par exemple, lors de l’approvisionnement d’un objet d’Azure AD vers ServiceNow, le système cible est ServiceNow. 

Le filtre **État** vous permet de sélectionner les états suivants :

- Tous
- Succès
- Échec
- Ignoré

Le filtre **Action** vous permet de filtrer les éléments suivants :

- Créer 
- Mettre à jour
- Supprimer
- Désactiver
- Autres

Le filtre **Date** vous permet de définir un intervalle de temps pour les données renvoyées.  
Les valeurs possibles sont les suivantes :

- 1 mois
- 7 jours
- 30 jours
- 24 heures
- Intervalle de temps personnalisé

Lorsque vous sélectionnez un délai d’exécution personnalisé, vous pouvez configurer une date de début et une date de fin.


En plus des champs par défaut, lorsque vous sélectionnez cette option, vous pouvez également inclure les champs suivants dans votre filtre :

- **ID de tâche** : un ID de tâche unique est associé à chaque application pour laquelle vous avez activé l’approvisionnement.   

- **ID de cycle** : identifie de façon unique le cycle d’approvisionnement. Vous pouvez partager cet ID avec le support pour rechercher le cycle dans lequel cet événement s’est produit.

- **Changer l’ID :** identificateur unique de l’événement d’approvisionnement. Vous pouvez partager cet ID avec le support pour rechercher l’événement d’approvisionnement.   



  

## <a name="provisioning-details"></a>Détails de l’approvisionnement 

Lorsque vous sélectionnez un élément dans la vue de la liste d’approvisionnement, vous pouvez obtenir plus de détails sur cet élément.
Les détails sont regroupés en fonction des catégories suivantes :

- Étapes

- Résolution des problèmes et recommandations

- Propriétés modifiées

- Résumé


![Onglets de ](./media/concept-provisioning-logs/provisioning-tabs.png "filtre")



### <a name="steps"></a>Étapes

L’onglet **Étapes** présente les étapes nécessaires pour approvisionner un objet. L’approvisionnement d’un objet peut être constitué de quatre étapes : 

- Importer un objet
- Déterminer si l’objet est dans la portée
- Faire correspondre l’objet entre la source et la cible
- Approvisionner l’objet (effectuer une action : il peut s’agir d’une opération de création, de mise à jour, de suppression ou de désactivation)



![Filtre](./media/concept-provisioning-logs/steps.png "Filtre")


### <a name="troubleshoot-and-recommendations"></a>Résolution des problèmes et recommandations


L’onglet de **résolution des problèmes et recommandations** fournit le code d’erreur et la raison. Les informations sur l’erreur sont uniquement disponibles en cas de défaillance. 


### <a name="modified-properties"></a>Propriétés modifiées

Les **propriétés modifiées** affichent l’ancienne valeur et la nouvelle valeur. Dans les cas où il n’y a pas d’ancienne valeur, la colonne de l’ancienne valeur est vide. 


### <a name="summary"></a>Résumé

L’onglet **résumé** fournit une vue d’ensemble des événements et des identificateurs de l’objet dans les systèmes source et cible. 

## <a name="what-you-should-know"></a>Ce que vous devez savoir

- Le Portail Azure stocke les données d’approvisionnement signalées pendant 30 jours si vous disposez d’une édition Premium et pendant 7 jours si vous disposez d’une édition gratuite.

- Vous pouvez utiliser l’attribut Changer l’ID comme identificateur unique. C’est, par exemple, utile lors de l’interaction avec le support technique.

- Il n’existe actuellement aucune option pour télécharger les données d’approvisionnement.

- Il n’existe actuellement aucun support pour Log Analytics.

- Lorsque vous accédez aux journaux d’approvisionnement à partir du contexte d’une application, cela ne filtre pas automatiquement les événements sur l’application spécifique comme le font les journaux d’audit.

## <a name="next-steps"></a>Étapes suivantes

* [Vérifier l’état de l’approvisionnement d’utilisateurs](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Problèmes lors de la configuration de l’approvisionnement des utilisateurs pour une application relevant de la galerie Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


