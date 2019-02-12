---
title: Supprimer un annuaire de locataire - Azure Active Directory | Microsoft Docs
description: Explique comment préparer un annuaire de locataire Azure AD pour la suppression
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 2fa8b6573952220b214294cfd0e89e978f6d00b5
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509352"
---
# <a name="delete-an-azure-active-directory-tenant"></a>Supprimer un locataire Azure Active Directory

Quand un locataire est supprimé, toutes les ressources qu’il contient sont également supprimées. Vous devez préparer le locataire en réduisant ses ressources associées avant de le supprimer. Seul un administrateur général Azure Active Directory (Azure AD) peut supprimer un locataire Azure AD à partir du portail.

## <a name="prepare-the-tenant-for-deletion"></a>Préparer le locataire pour la suppression

Vous ne pouvez pas supprimer un locataire dans Azure AD avant d’avoir effectué plusieurs vérifications. Ces vérifications limitent le risque d’un impact négatif de la suppression du locataire sur l’accès de l’utilisateur, par exemple, sur sa capacité à se connecter à Office 365 ou à accéder à des ressources dans Azure. En effet, si le locataire associé à un abonnement est supprimé involontairement, les utilisateurs ne peuvent pas accéder aux ressources Azure de cet abonnement. La section suivante décrit les conditions à vérifier :

* Le locataire ne doit contenir aucun utilisateur, à l’exception de l’administrateur général qui va supprimer le locataire. Tous les autres utilisateurs doivent être supprimés pour pouvoir supprimer le locataire. Si les utilisateurs sont synchronisés localement, la synchronisation doit être désactivée et les utilisateurs doivent être supprimés dans le locataire cloud à l’aide du portail Azure ou des applets de commande Azure PowerShell. 
* Aucune application ne doit se trouver dans le locataire. Elles doivent toutes être supprimées pour pouvoir supprimer le locataire.
* Le locataire ne doit être lié à aucun fournisseur d’authentification multifacteur.
* Le locataire ne doit être associé à aucun abonnement Microsoft Online Services, comme Microsoft Azure, Office 365 ou Azure AD Premium. Par exemple, si un locataire par défaut a été créé pour vous dans Azure, vous ne pouvez pas le supprimer si votre abonnement Azure utilise toujours ce locataire pour l’authentification. De même, vous ne pouvez pas supprimer un locataire auquel un autre utilisateur a associé un abonnement. 

## <a name="delete-an-azure-ad-tenant"></a>Supprimer un locataire Azure AD

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com) avec un compte d’administrateur général pour le locataire.

2. Sélectionnez **Azure Active Directory**.

3. Basculez sur le locataire à supprimer.
  
  ![bouton supprimer l’annuaire](./media/directory-delete-howto/delete-directory-command.png)

4. Sélectionnez **Supprimer l’annuaire**.
  
  ![bouton supprimer l’annuaire](./media/directory-delete-howto/delete-directory-list.png)

5. Si votre locataire échoue à une ou plusieurs vérifications, vous obtenez un lien vers plus d’informations sur la façon d’y remédier. Une fois toutes les vérifications effectuées, sélectionnez **Supprimer** pour terminer le processus.

## <a name="i-have-an-expired-subscription-but-i-cant-delete-the-tenant"></a>Mon abonnement a expiré, mais je ne peux pas supprimer le locataire

Quand vous avez configuré votre locataire Azure Active Directory, vous avez peut-être aussi activé des abonnements avec licence pour votre organisation, comme Azure Active Directory Premium P2, Office 365 Business Premium ou Enterprise Mobility + Security E5. Ces abonnements bloquent la suppression d’annuaire tant qu’ils ne sont pas complètement supprimés, pour éviter la perte accidentelle de données. Les abonnements doivent être dans un état **Déprovisionné** pour autoriser la suppression du locataire. Un abonnement **Expiré** ou **Annulé** passe à l’état **Désactivé**, et l’étape finale est l’état **Déprovisionné**. 

S’il s’agit de l’expiration d’un abonnement d’essai à Office 365 (à l’exception des programmes payants Partenaire/CSP, Accord Entreprise ou Licence en volume), consultez le tableau suivant. Pour plus d’informations sur la conservation des données et le cycle de vie des abonnements Office 365, consultez [Qu’arrive-t-il à mes données et à mon accès à la fin de mon abonnement Office 365 pour les entreprises ?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

État de l’abonnement | Données | Accès aux données
----- | ----- | -----
Actif (30 jours pour l’essai gratuit)  | Données accessibles à tous    | <li>Les utilisateurs ont un accès normal aux fichiers ou applications Office 365<li>Les administrateurs ont un accès normal au centre d’administration et aux ressources Office 365 
Expiré (30 jours)   | Données accessibles à tous    | <li>Les utilisateurs ont un accès normal aux fichiers ou applications Office 365<li>Les administrateurs ont un accès normal au centre d’administration et aux ressources Office 365
Désactivé (30 jours) | Données accessibles à l’administrateur uniquement  | <li>Les utilisateurs ne peuvent pas accéder aux fichiers ou applications Office 365<li>Les administrateurs peuvent accéder au centre d’administration Office 365, mais ne peuvent pas attribuer de licences ou mettre à jour des utilisateurs
Déprovisionné (30 jours après l’état Désactivé) | Données supprimées (automatiquement supprimées si aucun autre service n’est en cours d’utilisation) | <li>Les utilisateurs ne peuvent pas accéder aux fichiers ou applications Office 365<li>Les administrateurs peuvent accéder au centre d’administration Office 365 pour acheter et gérer d’autres abonnements

Vous pouvez placer un abonnement dans un état **Déprovisionné** à supprimer 3 jours après à l’aide du centre d’administration du Microsoft Store pour Entreprises. Cette fonctionnalité est bientôt disponible pour le centre d’administration Office 365.

1. Connectez-vous au [centre d’administration du Microsoft Store pour Entreprises](https://businessstore.microsoft.com/manage/) avec un compte d’administrateur général du locataire. Si vous essayez de supprimer le locataire « Contoso » qui a le domaine initial par défaut contoso.onmicrosoft.com, connectez-vous avec un UPN de type admin@contoso.onmicrosoft.com.

2. Accédez à l’onglet **Gérer** et sélectionnez **Produits et services**, puis choisissez l’abonnement à annuler et sélectionnez **Supprimer**.
  
  ![Supprimer un lien pour supprimer l’abonnement](./media/directory-delete-howto/delete-command.png)
  
3. Sélectionnez **Supprimer l’abonnement** pour supprimer l’abonnement et accepter les conditions générales. Toutes les données sont définitivement supprimées dans un délai de trois jours. Vous pouvez réactiver l’abonnement pendant cette période de trois jours si vous changez d’avis.
  
  ![conditions générales](./media/directory-delete-howto/delete-terms.png)

4. À présent, l’état de l’abonnement a changé et il est marqué pour suppression. L’abonnement passe à l’état **Déprovisionné** 72 heures plus tard.

5. Une fois que vous avez supprimé un abonnement dans votre locataire et que le délai de 72 heures s’est écoulé, vous pouvez vous reconnecter au centre d’administration Azure AD pour vérifier qu’aucune action n’est nécessaire et qu’aucun abonnement ne bloque la suppression de votre locataire. Vous pouvez alors supprimer votre locataire Azure AD.
  
  ![écran de vérification d’abonnement pour la suppression](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="next-steps"></a>Étapes suivantes
[Documentation Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
