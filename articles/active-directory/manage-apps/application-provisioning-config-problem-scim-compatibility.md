---
title: Problèmes connus liés à la conformité du protocole SCIM 2.0 - Azure AD
description: Comment résoudre les problèmes courants de compatibilité des protocoles rencontrés lors de l’ajout d’une application ne figurant pas dans la galerie qui prend en charge SCIM 2.0 dans Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0cae5458a9b9456d26363802ee9b06aaa842e72
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063592"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Problèmes et solutions connus relatifs à la conformité au protocole SCIM 2.0 du service de provisionnement des utilisateurs Azure AD

Azure Active Directory (Azure AD) peut affecter automatiquement des utilisateurs et des groupes à une application ou un système avec en façade un service Web avec l’interface définie dans la [Spécification du protocole SCIM (System for Cross-Domain Identity Management) 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

La prise en charge Azure AD du protocole SCIM 2.0 est décrite dans l’article [Utilisation du protocole SCIM (System for Cross-Domain Identity Management) pour configurer automatiquement des utilisateurs et groupes d’Azure Active Directory dans des applications](../app-provisioning/use-scim-to-provision-users-and-groups.md), qui dresse la liste des parties spécifiques du protocole qu’il implémente afin d’approvisionner automatiquement les utilisateurs et groupes Azure AD aux applications qui prennent en charge le protocole SCIM 2.0.

Cet article décrit les problèmes actuels et passés concernant le respect du service d’approvisionnement d’utilisateurs Azure AD au protocole SCIM 2.0, et comment les résoudre.

> [!IMPORTANT]
> La dernière mise à jour du client SCIM du service d’approvisionnement utilisateur Azure AD a été effectuée le 18 décembre 2018. Cette mise à jour traite des problèmes de compatibilité connus énumérés dans le tableau ci-dessous. Consultez le forum aux questions ci-dessous pour plus d’informations sur cette mise à jour.

## <a name="scim-20-compliance-issues-and-status"></a>Problèmes de conformité et état SCIM 2.0

| **Problèmes de conformité à SCIM 2.0** |  **Résolution ?** | **Date du correctif**  |  
|---|---|---|
| Azure AD nécessite que « /scim » soit à la racine de l’URL du point de terminaison SCIM de l’application  | Oui  |  18 décembre 2018 | 
| Les attributs d’extension utilisent la notation point « . » avant les noms d’attribut, et non pas la notation deux-points « : » |  Oui  | 18 décembre 2018  | 
|  Les demandes de correctif pour les attributs multivaleurs contiennent une syntaxe de filtre de chemin d’accès non valide | Oui  |  18 décembre 2018  | 
|  Les demandes de création de groupe contiennent une URI de schéma non valide | Oui  |  18 décembre 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Les corrections de services décrites ont-elles été automatiquement appliquées à mon application SCIM préexistante ?

Non. Comme cela aurait constitué un changement radical pour les applications SCIM codées pour utiliser l’ancien comportement, les modifications n’ont pas été automatiquement appliquées aux applications existantes.

Les changements sont appliqués à toutes les nouvelles [applications SCIM en dehors de la galerie](configure-single-sign-on-non-gallery-applications.md) configurées dans le portail Azure, après la date du correctif.

Pour plus d’informations sur la migration d’un travail d’approvisionnement utilisateur préexistant en vue d’inclure les dernières correctifs, reportez-vous à la section suivante.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Puis-je migrer un travail d’approvisionnement utilisateur existant basé sur SCIM pour y inclure les dernières correctifs de service ?

Oui. Si vous utilisez déjà cette instance d’application pour l’authentification unique et que vous avez besoin de migrer le travail d’approvisionnement existant pour inclure les dernières corrections, suivez la procédure ci-dessous. Cette procédure décrit comment utiliser l’API Microsoft Graph et l’explorateur d’API Microsoft Graph pour supprimer votre ancien travail d’approvisionnement de votre application SCIM existante et en créer une nouvelle qui présente le nouveau comportement.

> [!NOTE]
> Si votre application est encore en cours de développement et n’a pas encore été déployée pour l’authentification unique ou l’approvisionnement utilisateur, la solution la plus simple est de supprimer l’entrée de l’application dans la section **Azure Active Directory > Applications d’entreprise** du portail Azure, et de simplement ajouter une nouvelle entrée pour l’application en utilisant l’option **Créer une application > Ne figurant pas dans la galerie**. Il s’agit d’une alternative à l’exécution de la procédure ci-dessous.
 
1. Connectez-vous au portail Azure à l’adresse https://portal.azure.com.
2. Dans la section **Azure Active Directory > Applications d’entreprise** du portail Azure, localisez et sélectionnez votre application SCIM existante.
3. Dans la section **Propriétés** de votre application SCIM existante, copiez l’**ID objet**.
4. Dans une nouvelle fenêtre de navigateur web, accédez à https://developer.microsoft.com/graph/graph-explorer et connectez-vous en tant qu’administrateur pour le locataire Azure AD où votre application est ajoutée.
5. Dans l’Explorateur graphique, exécutez la commande suivante pour rechercher l’ID de votre travail d’approvisionnement. Remplacez « [object-id] » par l’ID du principal de service (ID d’objet) copié à partir de la troisième étape.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Obtenir les travaux](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Obtenir les travaux") 


6. Dans les résultats, copiez la chaîne « ID » complète qui commence par « customappsso » ou « scim ».
7. Exécutez la commande ci-dessous pour récupérer la configuration du mappage des attributs, afin de pouvoir effectuer une sauvegarde. Utilisez le même [object-id] qu’auparavant et remplacez [job-id] par l’ID du travail approvisionnement copié à partir de la dernière étape.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Obtenir le schéma](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Obtenir le schéma") 

8. Copiez la sortie JSON de la dernière étape et enregistrez-la dans un fichier texte. Ce code JSON de quelques milliers de lignes contient tous les mappages d’attributs personnalisés que vous avez ajoutés à votre ancienne application.
9. Exécutez la commande ci-dessous pour supprimer le travail d’approvisionnement :
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Exécutez la commande ci-dessous pour créer un nouveau travail d’approvisionnement avec les derniers correctifs de service.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Dans les résultats de la dernière étape, copiez la chaîne « ID » complète qui commence par « scim ». Si vous le souhaitez, réappliquez vos anciens mappages d’attributs en exécutant la commande ci-dessous, en remplaçant [new-job-id] par le nouvel ID de travail que vous venez de copier, et en entrant la sortie JSON de l’étape n° 7 en tant que corps de la requête.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Revenez à la première fenêtre du navigateur Web et sélectionnez l’onglet **Approvisionnement** de votre application.
13. Vérifiez votre configuration, puis démarrez la tâche d’approvisionnement. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Puis-je ajouter une nouvelle application non galerie possédant l’ancien comportement d’approvisionnement des utilisateurs ?

Oui. Si vous avez codé une application sur l’ancien comportement qui existait avant les correctifs et que vous avez besoin de déployer une nouvelle instance, suivez la procédure ci-dessous. Cette procédure décrit comment utiliser l’API Microsoft Graph et l’explorateur d’API Microsoft Graph pour créer un travail d’approvisionnement qui présente l’ancien comportement.
 
1. Connectez-vous au portail Azure à l’adresse https://portal.azure.com.
2. Dans la section **Azure Active Directory > Applications d’entreprise > Créer une application** du portail Azure, créez une application **ne figurant pas dans la galerie**.
3. Dans la section **Propriétés** de votre nouvelle application personnalisée, copiez l’**ID objet**.
4. Dans une nouvelle fenêtre de navigateur web, accédez à https://developer.microsoft.com/graph/graph-explorer et connectez-vous en tant qu’administrateur pour le locataire Azure AD où votre application est ajoutée.
5. Dans l’Explorateur graphique, exécutez la commande suivante pour initialiser la configuration d’approvisionnement de votre application.
   Remplacez « [object-id] » par l’ID du principal de service (ID d’objet) copié à partir de la troisième étape.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Revenez à la première fenêtre du navigateur Web et sélectionnez l’onglet **Approvisionnement** de votre application.
7. Complétez la configuration d’approvisionnement utilisateur comme vous le feriez normalement.


## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur l’approvisionnement et l’annulation de l’approvisionnement pour les applications SaaS](../app-provisioning/user-provisioning.md)

