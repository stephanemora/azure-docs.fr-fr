---
title: Approvisionnement à la demande de la synchronisation cloud Azure AD Connect
description: Cet article explique comment utiliser la fonctionnalité de synchronisation cloud d’Azure AD Connect pour tester les modifications de configuration.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048b78c7d59b3358dbffe2e3e6eedf41decabb8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554273"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Approvisionnement à la demande de la synchronisation cloud Azure AD Connect

Vous pouvez utiliser la fonctionnalité de synchronisation cloud d’Azure Active Directory (Azure AD) Connect pour tester les modifications de configuration en appliquant ces modifications à un seul utilisateur. Cet approvisionnement à la demande vous permet de vous assurer que les modifications apportées à la configuration ont bien été appliquées et sont correctement synchronisées avec Azure AD.  

> [!IMPORTANT] 
> Lorsque vous utilisez la configuration à la demande, les filtres d’étendue ne sont pas appliqués à l’utilisateur que vous avez sélectionné. Vous pouvez utiliser l’approvisionnement à la demande sur des utilisateurs extérieurs aux unités d’organisation que vous avez spécifiées.

## <a name="validate-a-user"></a>Valider un utilisateur
Pour utiliser l’approvisionnement à la demande, procédez comme suit :

1.  Dans le portail Azure, sélectionnez **Azure Active Directory**.
2.  Sélectionnez ensuite **Azure AD Connect**.
3.  Sélectionnez **Gérer la synchronisation cloud**.

    ![Capture d’écran montrant le lien permettant de gérer la synchronisation cloud.](media/how-to-install/install-6.png)
4. Sous **Configuration**, sélectionnez votre configuration.
5. Sous **Valider**, sélectionnez le bouton **Provisionner un utilisateur**. 

   ![Capture d’écran montrant le bouton d’approvisionnement d’un utilisateur.](media/how-to-on-demand-provision/on-demand-2.png)

6. Dans l’écran **Approvisionner à la demande**, entrez le nom unique d’un utilisateur, puis sélectionnez le bouton **Approvisionner**.  
 
   ![Capture d’écran montrant un nom d’utilisateur et un bouton d’approvisionnement.](media/how-to-on-demand-provision/on-demand-3.png)
7. Une fois l’approvisionnement terminé, un écran de confirmation s’affiche avec quatre coches vertes. Les éventuelles erreurs s’affichent à gauche.

   ![Capture d’écran montrant un approvisionnement réussi.](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>Obtenir les détails relatifs à l’approvisionnement
Vous pouvez maintenant examiner les informations relatives à utilisateur et déterminer si les modifications que vous avez apportées à la configuration ont été appliquées. Le reste de cet article décrit les sections individuelles qui s’affichent dans les détails d’un utilisateur synchronisé avec succès.

### <a name="import-user"></a>Importer l'utilisateur
La section **Importer un utilisateur** fournit des informations sur l’utilisateur importé à partir d’Active Directory. Voici à quoi ressemble l’utilisateur avant l’approvisionnement dans Azure AD. Sélectionnez le lien **Afficher les détails** pour afficher ces informations.

![Capture d’écran du bouton permettant d’afficher les détails relatifs à un utilisateur importé.](media/how-to-on-demand-provision/on-demand-5.png)

À l’aide de ces informations, vous pouvez voir les différents attributs (et leurs valeurs) importés. Si vous avez créé un mappage d’attribut personnalisé, vous pouvez voir la valeur ici.

![Capture d’écran montrant les détails relatifs à l’utilisateur.](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>Déterminer si l'utilisateur se trouve dans l'étendue
La section **Déterminer si l’utilisateur se trouve dans l’étendue** fournit des informations indiquant si l’utilisateur importé dans Azure AD se trouve dans l’étendue. Sélectionnez le lien **Afficher les détails** pour afficher ces informations.

![Capture d’écran du bouton permettant d’afficher les détails relatifs à l’étendue de l’utilisateur.](media/how-to-on-demand-provision/on-demand-7.png)

À l’aide de ces informations, vous pouvez voir si l’utilisateur se trouve dans l’étendue.

![Capture d’écran montrant les détails relatifs à l’étendue de l’utilisateur.](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>Faire correspondre l'utilisateur entre le système source et le système cible
La section **Faire correspondre l'utilisateur entre le système source et le système cible** fournit des informations indiquant si l’utilisateur existe déjà dans Azure AD et si une jointure est préférable à l’approvisionnement d’un nouvel utilisateur. Sélectionnez le lien **Afficher les détails** pour afficher ces informations.

![Capture d’écran du bouton permettant d’afficher les détails relatifs à un utilisateur mis en correspondance.](media/how-to-on-demand-provision/on-demand-8.png)

À l’aide de ces informations, vous pouvez voir si une correspondance a été trouvée ou si un nouvel utilisateur va être créé.

![Capture d’écran montrant les informations relatives à l’utilisateur.](media/how-to-on-demand-provision/on-demand-11.png)

Les détails de correspondance affichent un message avec l’une des trois opérations suivantes :
- **Créer** : un utilisateur est créé dans Azure AD
- **Mettre à jour** : un utilisateur est mis à jour selon une modification apportée à la configuration
- **Supprimer** : un utilisateur est supprimé d’Azure AD

Selon le type d’opération effectuée, le message varie.

### <a name="perform-action"></a>Action à effectuer
La section **Action à effectuer** fournit des informations sur l’utilisateur approvisionné ou exporté dans Azure AD après l’application de la configuration. Voici à quoi ressemble l’utilisateur après l’approvisionnement dans Azure AD. Sélectionnez le lien **Afficher les détails** pour afficher ces informations.

![Capture d’écran du bouton permettant d’afficher les détails relatifs à une action effectuée.](media/how-to-on-demand-provision/on-demand-9.png)

À l’aide de ces informations, vous pouvez voir les valeurs des attributs après l’application de la configuration. Sont-ils similaires à ce qui a été importé ou sont-ils différents ? La configuration a-t-elle été correctement appliquée ?  

Ce processus vous permet de suivre la transformation des attributs à mesure qu’elle progresse dans le cloud et dans votre locataire Azure AD.

![Capture d’écran montrant les détails relatifs aux attributs suivis.](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>Étapes suivantes 

- [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](what-is-cloud-sync.md)
- [Installer la synchronisation cloud Azure AD Connect](how-to-install.md)
 