---
title: Supprimer un annuaire Azure AD - Azure Active Directory | Microsoft Docs
description: Explique comment préparer un annuaire Azure AD pour la suppression, y compris les répertoires de libre-service
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
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ac6b4530414850c52605bac8cb701aa2b877d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60473126"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>Supprimer un annuaire dans Azure Active Directory

Lorsqu’un annuaire Azure AD est supprimé, toutes les ressources qui sont contenus dans le répertoire sont également supprimées. Préparation de votre organisation en réduisant ses ressources associées avant de le supprimer. Seul un administrateur général Azure Active Directory (Azure AD) peut supprimer un annuaire Azure AD à partir du portail.

## <a name="prepare-the-directory"></a>Préparer le répertoire

Vous ne pouvez pas supprimer un répertoire dans Azure AD tant qu’il passe plusieurs vérifications. Ces vérifications de réduisent le risque que la suppression d’un annuaire Azure AD négativement a un impact sur l’accès utilisateur, comme la possibilité de se connecter à Office 365 ou accès aux ressources dans Azure. Par exemple, si le répertoire associé à un abonnement est supprimé accidentellement, les utilisateurs ne peuvent pas accéder les ressources Azure pour cet abonnement. Les conditions à remplir sont les suivantes :

* Il ne peut y avoir aucun utilisateur dans le répertoire à l’exception d’un administrateur global qui consiste à supprimer le répertoire. Tous les autres utilisateurs doivent être supprimés avant de pouvoir supprimer le répertoire. Si les utilisateurs sont synchronisés en local, puis synchronisation doit tout d’abord être désactivée et les utilisateurs doivent être supprimés dans le répertoire cloud à l’aide du portail Azure ou les applets de commande Azure PowerShell.
* Aucune application ne doit se trouver dans l’annuaire. Toutes les applications doivent être supprimées avant de pouvoir supprimer le répertoire.
* Il ne peut y avoir aucun fournisseur d’authentification multifacteur lié à l’annuaire.
* L’annuaire ne doit être associé à aucun abonnement Microsoft Online Services, tel que Microsoft Azure, Office 365 ou Azure AD. Par exemple, si un annuaire par défaut a été créé pour vous dans Azure, vous ne pouvez pas le supprimer si votre abonnement Azure utilise toujours cet annuaire à des fins d’authentification. De même, vous ne pouvez pas supprimer un répertoire auquel un autre utilisateur a associé un abonnement.

## <a name="delete-the-directory"></a>Supprimer le répertoire

1. Se connecter à la [centre d’administration Azure AD](https://aad.portal.azure.com) avec un compte qui est l’administrateur Global pour votre organisation.

2. Sélectionnez **Azure Active Directory**.

3. Basculer vers le répertoire que vous souhaitez supprimer.
  
   ![Confirmer l’organisation avant de supprimer](./media/directory-delete-howto/delete-directory-command.png)

4. Sélectionnez **Supprimer l’annuaire**.
  
   ![Sélectionnez la commande pour supprimer l’organisation](./media/directory-delete-howto/delete-directory-list.png)

5. Si votre répertoire ne passe pas d’une ou plusieurs vérifications, vous bénéficiez avec un lien vers plus d’informations sur la façon de passer. Une fois toutes les vérifications effectuées, sélectionnez **Supprimer** pour terminer le processus.

## <a name="if-you-cant-delete-the-directory"></a>Si vous ne pouvez pas supprimer le répertoire

Lorsque vous avez configuré votre annuaire Azure AD, vous pourrez avoir également activé sous licence des abonnements pour votre organisation comme Azure AD Premium P2, Office 365 Business Premium ou Enterprise Mobility + Security E5. Pour éviter la perte accidentelle de données, vous ne pouvez pas supprimer un répertoire jusqu'à ce que les abonnements ne sont complètement supprimés. Les abonnements doivent être dans un **Désapprovisionné** état pour autoriser la suppression du répertoire. Un **expiré** ou **Canceled** abonnement est déplacé vers le **désactivé** état et l’étape finale est la **Désapprovisionné** état.

S’il s’agit de l’expiration d’un abonnement d’essai à Office 365 (à l’exception des programmes payants Partenaire/CSP, Accord Entreprise ou Licence en volume), consultez le tableau suivant. Pour plus d’informations sur la conservation des données et le cycle de vie des abonnements Office 365, consultez [Qu’arrive-t-il à mes données et à mon accès à la fin de mon abonnement Office 365 pour les entreprises ?](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3). 

État de l’abonnement | Données | Accès aux données
----- | ----- | -----
Actif (30 jours pour l’essai gratuit) | Données accessibles à tous | Les utilisateurs ont un accès normal aux fichiers ou applications Office 365<br>Les administrateurs ont accès normal au centre d’administration Microsoft 365 et des ressources 
Expiré (30 jours) | Données accessibles à tous| Les utilisateurs ont un accès normal aux fichiers ou applications Office 365<br>Les administrateurs ont accès normal au centre d’administration Microsoft 365 et des ressources
Désactivé (30 jours) | Données accessibles à l’administrateur uniquement | Les utilisateurs ne peuvent pas accéder aux fichiers ou applications Office 365<br>Administrateurs peuvent accéder au centre d’administration Microsoft 365 mais ne peut pas attribuer des licences ou mettre à jour les utilisateurs
Déprovisionné (30 jours après l’état Désactivé) | Données supprimées (automatiquement supprimées si aucun autre service n’est en cours d’utilisation) | Les utilisateurs ne peuvent pas accéder aux fichiers ou applications Office 365<br>Les administrateurs peuvent accéder le centre d’administration Microsoft 365 pour acheter et gérer les autres abonnements

## <a name="delete-a-subscription"></a>Supprimer un abonnement

Vous pouvez placer un abonnement à l’état d’approvisionnement annulé à supprimer dans les trois jours à l’aide du centre d’administration Microsoft 365.

1. Se connecter à la [centre d’administration Microsoft 365](https://admin.microsoft.com) avec un compte qui est un administrateur global dans votre organisation. Si vous essayez de supprimer le répertoire « Contoso » qui est le contoso.onmicrosoft.com de domaine initial par défaut, connectez-vous avec un nom UPN, tel que admin@contoso.onmicrosoft.com.

2. Sélectionnez **facturation** et sélectionnez **abonnements**, puis choisissez l’abonnement que vous souhaitez annuler. Après avoir cliqué sur **Annuler**, actualisez la page.
  
   ![Supprimer un lien pour supprimer l’abonnement](./media/directory-delete-howto/delete-command.png)
  
3. Sélectionnez **Supprimer** pour supprimer l’abonnement et accepter les conditions générales. Toutes les données sont définitivement supprimées dans un délai de trois jours. Vous pouvez réactiver l’abonnement pendant la période de trois jours si vous changez d’avis.
  
   ![Lisez attentivement les termes et conditions](./media/directory-delete-howto/delete-terms.png)

4. À présent, l’état de l’abonnement a changé et il est marqué pour suppression. L’abonnement passe à l’état **Déprovisionné** 72 heures plus tard.

5. Une fois que vous avez supprimé un abonnement dans votre répertoire et 72 heures se sont écoulées, vous pouvez vous connecter arrière dans le centre d’administration Azure AD à nouveau et il doit être aucune action requise et aucun abonnement bloque la suppression de votre répertoire. Vous devez pouvoir supprimer correctement votre annuaire Azure AD.
  
   ![écran de vérification d’abonnement pour la suppression](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>J’ai un abonnement d’évaluation qui bloque la suppression

Il existe [produits de l’inscription libre-service](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide) comme Microsoft Power BI, Rights Management Services, Microsoft Power Apps ou Dynamics 365, des utilisateurs individuels peuvent s’inscrire via Office 365, ce qui crée également un utilisateur invité pour l’authentification dans votre annuaire Azure AD. Ces produits libre-service bloquent les suppressions de répertoire jusqu'à ce qu’ils sont complètement supprimés à partir du répertoire, afin d’éviter la perte de données. Ils peuvent être supprimés uniquement par l’administrateur Azure AD si l’utilisateur inscrit individuellement ou a été affecté le produit.

Il existe deux types de produits d’inscription libre service dans la façon dont ils sont attribués : 

* Affectation de niveaux de l’organisation : Un administrateur Azure AD affecte le produit à toute l’organisation et un utilisateur peut être activement à l’aide du service à cette affectation de niveaux de l’organisation même si elles ne sont pas licence individuelle.
* Attribution de niveau utilisateur : Un utilisateur individuel au cours de l’inscription libre-service affecte essentiellement le produit à eux-mêmes sans un administrateur. Une fois que l’organisation est gérée par un administrateur (consultez [prise en charge de l’administrateur d’un répertoire non géré](domains-admin-takeover.md), puis l’administrateur peut affecter directement le produit pour les utilisateurs sans inscription libre-service.  

Lorsque vous commencez la suppression du produit inscription libre-service, l’action définitivement supprime les données et tous les accès utilisateur au service. Tout utilisateur qui a été attribuée l’offre individuellement ou à niveau de l’organisation est ensuite bloqué à partir de la connexion ou de l’accès à toutes les données existantes. Si vous souhaitez éviter la perte de données avec le produit inscription libre-service comme [des tableaux de bord Microsoft Power BI](https://docs.microsoft.com/power-bi/service-export-to-pbix) ou [configuration de stratégie de Rights Management Services](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy), assurez-vous que les données sont sauvegardées et enregistrées ailleurs.

Pour plus d’informations sur les services et produits d’inscription libre-service actuellement disponibles, consultez [des programmes disponibles libre-service](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs).

S’il s’agit de l’expiration d’un abonnement d’essai à Office 365 (à l’exception des programmes payants Partenaire/CSP, Accord Entreprise ou Licence en volume), consultez le tableau suivant. Pour plus d’informations sur Office 365 données rétention et l’abonnement du cycle de vie, consultez [que se passe-t-il pour mes données et l’accès quand mon Office 365 pour l’abonnement de l’entreprise se termine ?](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide).

État du produit | Données | Accès aux données
------------- | ---- | --------------
Actif (30 jours pour l’essai gratuit) | Données accessibles à tous | Les utilisateurs ont un accès normal au produit d’inscription libre-service, des fichiers ou des applications<br>Les administrateurs ont accès normal au centre d’administration Microsoft 365 et des ressources
Deleted | Données supprimées | Les utilisateurs ne peuvent pas accéder aux libre-service un abonnement à un produit, des fichiers ou des applications<br>Les administrateurs peuvent accéder le centre d’administration Microsoft 365 pour acheter et gérer les autres abonnements

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>Comment puis-je supprimer un produit d’inscription libre-service dans le portail Azure ?

Vous pouvez placer un produit inscription libre-service telles que Microsoft Power BI ou Azure Rights Management Services dans un **supprimer** état doit être immédiatement supprimé dans le portail Azure AD.

1. Se connecter à la [centre d’administration Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) avec un compte qui est un administrateur général de l’organisation. Si vous essayez de supprimer le répertoire « Contoso » qui est le contoso.onmicrosoft.com de domaine par défaut initial, connectez-vous avec un UPN comme admin@contoso.onmicrosoft.com.

2. Sélectionnez **licences**, puis sélectionnez **produits de l’inscription libre-service**. Vous pouvez voir tous les libre-service inscription produits séparément à partir d’abonnements en fonction du siège. Choisissez le produit que vous souhaitez supprimer définitivement. Voici un exemple dans Microsoft Power BI :

    ![le nom d’utilisateur est mal orthographiée ou introuvable](./media/directory-delete-howto/licenses-page.png)

3. Sélectionnez **supprimer** pour supprimer le produit et acceptez les termes du contrat, les données sont supprimées immédiatement et irrévocable. Cette action de suppression supprime tous les utilisateurs et supprimer l’accès d’organisation pour le produit. Cliquez sur Oui pour poursuivre la suppression.  

    ![le nom d’utilisateur est mal orthographiée ou introuvable](./media/directory-delete-howto/delete-product.png)

4. Lorsque vous sélectionnez **Oui**, la suppression du produit libre-service sera lancée. Il existe une notification qui vous indiquera de la suppression en cours d’exécution.  

    ![le nom d’utilisateur est mal orthographiée ou introuvable](./media/directory-delete-howto/progress-message.png)

5. Maintenant l’état de libre-service un abonnement à un produit a changé à **Deleted**. Lorsque vous actualisez la page, le produit doit être supprimé de la **produits de l’inscription libre-service** page.  

    ![le nom d’utilisateur est mal orthographiée ou introuvable](./media/directory-delete-howto/product-deleted.png)

6. Une fois que vous avez supprimé tous les produits, vous pouvez vous connecter à nouveau dans le centre d’administration Azure AD et il doit y avoir aucune action requise et aucun produit bloque la suppression de votre répertoire. Vous devez pouvoir supprimer correctement votre annuaire Azure AD.

    ![le nom d’utilisateur est mal orthographiée ou introuvable](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>Étapes suivantes

[Documentation Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
