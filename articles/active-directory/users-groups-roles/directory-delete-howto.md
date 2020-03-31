---
title: Supprimer un annuaire Azure AD directory - Azure Active Directory | Microsoft Docs
description: Cet article explique comment préparer un annuaire Azure AD pour la suppression, y compris les répertoires en libre-service
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a60ed44ddf057ef983f8f76f23fd784bc3efd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961814"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Supprimer un annuaire dans Azure Active Directory

Lorsqu’un annuaire Azure AD est supprimé, toutes les ressources qui y sont contenues sont également supprimées. Vous devez préparer votre organisation en réduisant les ressources associées à un annuaire avant de le supprimer. Seul un administrateur général Azure Active Directory (Azure AD) peut supprimer un locataire Azure AD à partir du portail.

## <a name="prepare-the-directory"></a>Préparer le répertoire

Vous ne pouvez pas supprimer un annuaire dans Azure AD avant d’avoir effectué plusieurs vérifications. Ces vérifications limitent le risque d’un impact négatif de la suppression de l’annuaire Azure AD sur l’accès de l’utilisateur, par exemple, sur sa capacité à se connecter à Office 365 ou à accéder à des ressources dans Azure. En effet, si l’annuaire associé à un abonnement est supprimé involontairement, les utilisateurs ne peuvent pas accéder aux ressources Azure de cet abonnement. Les conditions à remplir sont les suivantes :

* L’annuaire ne doit contenir aucun utilisateur, à l’exception de l’administrateur général qui va le supprimer. Tous les autres utilisateurs doivent être supprimés avant de pouvoir supprimer le répertoire. Si les utilisateurs sont synchronisés en local, la synchronisation doit d’abord être désactivée et les utilisateurs doivent être supprimés dans l’annuaire de cloud en utilisant le portail Microsoft Azure ou des applets de commande Azure PowerShell.
* Aucune application ne doit se trouver dans l’annuaire. Elles doivent toutes être retirées avant de pouvoir supprimer l’annuaire.
* L’annuaire ne doit être lié à aucun fournisseur d’authentification multifacteur.
* L’annuaire ne doit être associé à aucun abonnement Microsoft Online Services, tel que Microsoft Azure, Office 365 ou Azure AD. Par exemple, si un annuaire par défaut a été créé pour vous dans Azure, vous ne pouvez pas le supprimer si votre abonnement Azure utilise toujours cet annuaire à des fins d’authentification. De même, vous ne pouvez pas supprimer un répertoire auquel un autre utilisateur a associé un abonnement.

## <a name="delete-the-directory"></a>Supprimer l’annuaire

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com) avec un compte d’administrateur général pour votre organisation.

2. Sélectionnez **Azure Active Directory**.

3. Basculez vers l’annuaire que vous voulez supprimer.
  
   ![Confirmez l’organisation avant de lancer la suppression](./media/directory-delete-howto/delete-directory-command.png)

4. Sélectionnez **Supprimer l’annuaire**.
  
   ![Sélectionnez la commande pour supprimer l’organisation](./media/directory-delete-howto/delete-directory-list.png)

5. Si votre annuaire échoue à une ou plusieurs vérifications, vous obtenez un lien vers plus d’informations sur la façon d’y remédier. Une fois toutes les vérifications effectuées, sélectionnez **Supprimer** pour terminer le processus.

## <a name="if-you-cant-delete-the-directory"></a>Si vous ne pouvez pas supprimer l’annuaire

Quand vous avez configuré votre annuaire Azure AD, vous avez peut-être aussi activé des abonnements avec licence pour votre organisation, comme Azure AD Premium P2, Office 365 Business Premium ou Enterprise Mobility + Security E5. Pour éviter la perte accidentelle de données, vous ne pouvez pas supprimer un annuaire tant que les abonnements qui lui sont associés ne sont complètement supprimés. Les abonnements doivent être dans un état **Approvisionnement annulé** pour autoriser la suppression de l’annuaire. Un abonnement **Expiré** ou **Annulé** passe à l’état **Désactivé**, et l’étape finale est l’état **Approvisionnement annulé**.

S’il s’agit de l’expiration d’un abonnement d’essai à Office 365 (à l’exception des programmes payants Partenaire/CSP, Accord Entreprise ou Licence en volume), consultez le tableau suivant. Pour plus d’informations sur la conservation des données et le cycle de vie des abonnements Office 365, consultez [Qu’arrive-t-il à mes données et à mon accès à la fin de mon abonnement Office 365 pour les entreprises ?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

État de l’abonnement | Données | Accès aux données
----- | ----- | -----
Actif (30 jours pour l’essai gratuit) | Données accessibles à tous | Les utilisateurs ont un accès normal aux fichiers ou applications Office 365<br>Les administrateurs ont un accès normal au centre d’administration et aux ressources Microsoft 365 
Expiré (30 jours) | Données accessibles à tous| Les utilisateurs ont un accès normal aux fichiers ou applications Office 365<br>Les administrateurs ont un accès normal au centre d’administration et aux ressources Microsoft 365
Désactivé (30 jours) | Données accessibles à l’administrateur uniquement | Les utilisateurs ne peuvent pas accéder aux fichiers ou applications Office 365<br>Les administrateurs peuvent accéder au centre d’administration Microsoft 365, mais ne peuvent pas attribuer de licences ou mettre à jour des utilisateurs
Déprovisionné (30 jours après l’état Désactivé) | Données supprimées (automatiquement supprimées si aucun autre service n’est en cours d’utilisation) | Les utilisateurs ne peuvent pas accéder aux fichiers ou applications Office 365<br>Les administrateurs peuvent accéder au centre d’administration Microsoft 365 pour acheter et gérer d’autres abonnements

## <a name="delete-a-subscription"></a>Supprimer un abonnement

Vous pouvez placer un abonnement dans un état **Approvisionnement annulé** à supprimer trois jours après à l’aide du centre d’administration Microsoft 365.

1. Connectez-vous au [Centre d’administration Microsoft 365](https://admin.microsoft.com) avec un compte d’administrateur général pour votre organisation. Si vous essayez de supprimer l’annuaire « Contoso » qui a le domaine initial par défaut contoso.onmicrosoft.com, connectez-vous avec un UPN de type admin@contoso.onmicrosoft.com.

2. Affichez un aperçu du nouveau Centre d’administration Microsoft 365 en vous assurant que la bascule **Découvrez le nouveau centre d’administration** est activée.

   ![Afficher un aperçu de la nouvelle expérience du centre d’administration M365](./media/directory-delete-howto/preview-toggle.png)

3. Une fois le nouveau centre d’administration activé, vous devez annuler un abonnement pour pouvoir le supprimer. Sélectionnez **Facturation** et sélectionnez **Produits et services**, puis sélectionnez **Annuler l’abonnement** pour l’abonnement que vous souhaitez annuler. Vous êtes redirigé vers une page de commentaires.

   ![Choisir l’abonnement à annuler](./media/directory-delete-howto/cancel-choose-subscription.png)

4. Remplissez le formulaire de commentaires et sélectionnez **Annuler l’abonnement** pour annuler l’abonnement.

   ![Commande Annuler dans la version préliminaire de l’abonnement](./media/directory-delete-howto/cancel-command.png)

5. Vous pouvez maintenant supprimer l’abonnement. Sélectionnez **Supprimer** pour l’abonnement que vous souhaitez supprimer. Si vous ne trouvez pas l’abonnement dans la page **Produits et services** , assurez-vous que **État de l'abonnement** est défini sur **Tous**.

   ![Supprimer un lien pour supprimer l’abonnement](./media/directory-delete-howto/delete-command.png)

6. Sélectionnez **Supprimer l’abonnement** pour supprimer l’abonnement et accepter les conditions générales. Toutes les données sont définitivement supprimées dans un délai de trois jours. Vous pouvez [réactiver l’abonnement](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide) pendant la période de trois jours si vous changez d’avis.
  
   ![Lisez attentivement les termes et conditions](./media/directory-delete-howto/delete-terms.png)

7. À présent, l’état de l’abonnement a changé et il est marqué pour suppression. L’abonnement passe à l’état **Déprovisionné** 72 heures plus tard.

8. Une fois que vous avez supprimé un abonnement dans votre annuaire et que le délai de 72 heures s’est écoulé, vous pouvez vous reconnecter au centre d’administration Azure AD pour vérifier qu’aucune action n’est nécessaire et qu’aucun abonnement ne bloque la suppression de votre annuaire. Vous pouvez alors supprimer votre annuaire Azure AD.
  
   ![écran de vérification d’abonnement pour la suppression](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>J’ai un abonnement d’évaluation qui bloque la suppression

L’inscription à certains de nos produits [est en libre-service](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide). C’est le cas pour Microsoft Power BI, Rights Management Services, Microsoft Power Apps ou Dynamics 365. Des utilisateurs individuels peuvent s’inscrire via Office 365, ce qui crée également un utilisateur invité pour l’authentification dans votre annuaire Azure AD. Ces produits en libre-service bloquent les suppressions d’annuaire tant qu’ils ne sont pas complètement supprimés de l’annuaire, afin d’éviter toute perte de données. Peu importe que l’utilisateur se soit inscrit de lui-même, ou qu’on lui ait attribué le produit : seul l’administrateur Azure AD pourra supprimer un tel abonnement.

Il existe deux types de produits disponibles à l’inscription en libre service, selon la façon dont ils sont attribués : 

* Attribution au niveau de l’organisation : Un administrateur Azure AD attribue le produit à toute l’organisation et un utilisateur peut utiliser le service grâce à cette attribution, même s’il ne possède pas la licence de ce produit.
* Attribution de niveau de l’utilisateur : Un utilisateur effectue une inscription en libre-service et s’attribue le produit, sans l’assistance d’un administrateur. Lorsque l’organisation est gérée par un administrateur (pour en savoir plus, veuillez consulter [Prendre le contrôle d’un annuaire non géré en tant qu’administrateur dans Azure Active Directory](domains-admin-takeover.md)), cet administrateur peut attribuer directement le produit à des utilisateurs, sans inscription en libre-service.  

Lorsque vous déclenchez la suppression d’un produit attribué à des utilisateurs par une inscription en libre-service, cette action supprime définitivement les données et tous les accès utilisateur du service. Tous les utilisateurs a qui ce produit a été attribué, que ce soit individuellement ou au niveau de l’organisation, ne peuvent plus s’y connecter, ni accéder à leurs données liées à ce produit. Si vous souhaitez éviter toute perte de données liées à un produit attribué par une inscription en libre-service, comme [des tableaux de bord Microsoft Power BI](https://docs.microsoft.com/power-bi/service-export-to-pbix) ou [une configuration de stratégie Rights Management Services](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), avant de déclencher la suppression, vérifiez que les données sont sauvegardées et enregistrées ailleurs.

Pour plus d’informations sur les services et produits disponibles avec une inscription en libre-service, consultez la page [Programmes libre-service disponibles](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

S’il s’agit de l’expiration d’un abonnement d’essai à Office 365 (à l’exception des programmes payants Partenaire/CSP, Accord Entreprise ou Licence en volume), consultez le tableau suivant. Pour plus d’informations sur la conservation des données et le cycle de vie des abonnements Office 365, consultez [Qu’arrive-t-il à mes données et à mon accès à la fin de mon abonnement Office 365 pour les entreprises ?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

État du produit | Données | Accès aux données
------------- | ---- | --------------
Actif (30 jours pour l’essai gratuit) | Données accessibles à tous | Les utilisateurs peuvent s’inscrire normalement en libre-service pour accéder aux produits, fichiers ou applications<br>Les administrateurs ont un accès normal au centre d’administration et aux ressources Microsoft 365
Deleted | Données supprimées | Les utilisateurs ne peuvent pas s’inscrire en libre-service pour accéder aux produits, fichiers ou applications<br>Les administrateurs peuvent accéder au centre d’administration Microsoft 365 pour acheter et gérer d’autres abonnements

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Comment puis-je supprimer une inscription à un produit en libre-service dans le portail Microsoft Azure ?

Vous pouvez définir un produit accessible via une inscription en libre-service, comme Microsoft Power BI ou Azure Rights Management Services, dans un état **Supprimer**. Il sera alors immédiatement supprimé du portail Microsoft Azure AD.

1. Connectez-vous au [centre d’administration Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) avec un compte d’administrateur général de votre organisation. Si vous essayez de supprimer l’annuaire « Contoso » qui a le domaine initial par défaut contoso.onmicrosoft.com, connectez-vous avec un UPN de type admin@contoso.onmicrosoft.com.

2. Sélectionnez **Licences**, puis **Produits à l’inscription en libre-service**. Vous pouvez afficher tous les produits disponibles à l’inscription en libre-service séparément des abonnements par postes. Choisissez le produit que vous souhaitez supprimer définitivement. Voici un exemple dans Microsoft Power BI :

    ![le nom d’utilisateur est mal orthographié ou introuvable](./media/directory-delete-howto/licenses-page.png)

3. Sélectionnez **Supprimer** pour supprimer le produit, puis acceptez les termes du contrat stipulant que les données sont supprimées immédiatement et définitivement. Cette action de suppression supprime tous les utilisateurs et supprime l’accès de l’organisation au produit. Cliquez sur Oui pour poursuivre la suppression.  

    ![le nom d’utilisateur est mal orthographié ou introuvable](./media/directory-delete-howto/delete-product.png)

4. Lorsque vous sélectionnez **Oui**, la suppression du produit accessible en libre-service sera lancée. Une notification s’affiche, pour vous indiquer que la suppression est en cours d’exécution.  

    ![le nom d’utilisateur est mal orthographié ou introuvable](./media/directory-delete-howto/progress-message.png)

5. À présent, l’état du produit dont l’inscription est disponible en libre-service est défini sur **Supprimé**. Normalement, lorsque vous actualisez la page **Produits à l’inscription en libre-service**, le produit n’y apparaît plus.  

    ![le nom d’utilisateur est mal orthographié ou introuvable](./media/directory-delete-howto/product-deleted.png)

6. Une fois que vous avez supprimé tous les produits, vous pouvez vous reconnecter au centre d’administration Azure AD pour vérifier qu’aucune action n’est nécessaire et qu’aucun produit ne bloque la suppression de votre annuaire. Vous pouvez alors supprimer votre annuaire Azure AD.

    ![le nom d’utilisateur est mal orthographié ou introuvable](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Étapes suivantes

[Documentation Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
