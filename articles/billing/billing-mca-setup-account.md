---
title: Configurer votre compte de facturation d’un contrat de client de Microsoft - Azure | Microsoft Docs
description: Découvrez comment configurer votre compte de facturation d’un contrat de client de Microsoft.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 90f832319fa2343003af58bd99eb64c0cbd94dd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371720"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Configurer votre compte de facturation d’un contrat de client de Microsoft

Si l’inscription de votre contrat entreprise a expiré ou sur le point d’expirer, vous pouvez signer un contrat de client Microsoft pour renouveler votre inscription. Le renouvellement comprend les étapes suivantes :

1. Accepter le nouveau contrat de client de Microsoft. Travailler avec votre représentant de champ de Microsoft pour comprendre les détails et acceptez le contrat de nouveau.
2. Configurer le nouveau compte de facturation est créé pour le nouveau contrat de client de Microsoft.

Cet article décrit les modifications apportées à votre facturation existante après l’installation et vous guide tout au long de l’installation de votre nouveau compte de facturation.

Pour configurer le compte de facturation, transition la facturation des abonnements Azure à partir de l’inscription de votre accord entreprise vers le nouveau compte. Le programme d’installation n’affecte pas les services Azure qui sont exécutent dans vos abonnements. Toutefois, il modifie la façon que vous prévoyez de gérer la facturation pour vos abonnements.

- Au lieu du [portail EA](https://ea.azure.com), vous allez gérer vos services Azure et la facturation, dans le [Azure portal](https://portal.azure.com).
- Vous obtiendrez une facture mensuelle et numérique pour vos frais. Vous pouvez afficher et analyser la facture dans Azure Cost Management + page de facturation.
- Au lieu de départements et compte dans votre inscription contrat entreprise, vous utiliserez la structure et les étendues depuis le nouveau compte de facturation pour gérer et organiser votre facturation.

Avant de commencer l’installation, nous vous recommandons de que procéder comme suit :

- **Comprendre votre nouveau compte de facturation**
  - Votre nouveau compte simplifie la facturation pour votre organisation. [Obtenir un aperçu rapide de votre nouveau compte de facturation](billing-mca-overview.md)
- **Vérifiez votre accès pour terminer l’installation**
  - Seuls les utilisateurs avec certaines autorisations d’administration peuvent terminer l’installation. Vérifiez si vous avez le [accès requis pour terminer l’installation](#access-required-to-complete-the-setup).
- **Comprendre les modifications apportées à votre hiérarchie de facturation**
  - Vous le nouveau compte de facturation êtes structurée différemment à l’inscription de votre contrat entreprise. [Comprendre les modifications apportées à votre hiérarchie de facturation dans le nouveau compte](#understand-changes-to-your-billing-hierarchy).
- **Comprendre les modifications apportées à l’accès de vos administrateurs de facturation**
  - Les administrateurs à partir de votre inscription contrat entreprise ont accès aux facturation étendues dans le nouveau compte. [Comprendre les modifications apportées à leur accès](#understand-changes-to-your-billing-administrators-access).
- **Fonctionnalités de contrat entreprise d’affichage qui sont remplacées par le nouveau compte**
  - Afficher les fonctionnalités de l’accord entreprise qui sont remplacées par des fonctionnalités dans le nouveau compte.
- **Affichage des réponses aux questions courantes**
  - Vue [des informations supplémentaires](#additional-information) pour en savoir plus sur le programme d’installation.

## <a name="access-required-to-complete-the-setup"></a>Accès requis pour terminer l’installation

Pour terminer l’installation, vous avez besoin de l’accès suivant :

- Propriétaire du profil de facturation qui a été créé lors de la signature de l’accord de client de Microsoft. Pour en savoir plus sur les profils de facturation, consultez [comprendre les profils de facturation](billing-mca-overview.md#understand-billing-profiles).

- Administrateur d’entreprise sur l’inscription est renouvelée.

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>Si vous n’êtes pas un administrateur d’entreprise sur l’inscription

Vous pouvez demander les administrateurs d’entreprise de l’inscription pour terminer la configuration de votre compte de facturation.

1. Connectez-vous au portail Azure en utilisant le lien dans l’e-mail qui vous a été envoyé lorsque vous signé le contrat de client de Microsoft.

2. Si quelqu'un d’autre dans votre organisation signé le contrat, ou vous n’avez pas reçu l’e-mail, connectez-vous à l’aide du lien suivant. Remplacez **enrollmentNumber** avec le numéro d’inscription de votre contrat entreprise qui a été renouvelé.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Sélectionnez les administrateurs d’entreprise que vous souhaitez envoyer la demande.

   ![Capture d’écran qui montre l’invitation les administrateurs d’entreprise](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. Sélectionnez **Send request**.

   Les administrateurs reçoit un e-mail contenant des instructions pour terminer l’installation.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>Si vous n’êtes pas un propriétaire du profil de facturation

L’utilisateur dans votre organisation, qui a signé le contrat de client de Microsoft est ajouté en tant que le propriétaire sur le profil de facturation. Demander à l’utilisateur pour vous ajouter en tant que propriétaire afin que vous puissiez exécuter le programme d’installation.  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>Comprendre les modifications apportées à votre hiérarchie de facturation

Votre nouveau compte de facturation simplifie la facturation pour votre organisation tout en vous fournissant améliorée de facturation et de fonctionnalités de gestion des coûts. Le diagramme suivant explique comment la facturation est organisée dans le nouveau compte de facturation.

![Image de la hiérarchie du transition post ea mca](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

1. Le compte de facturation vous permet de gérer la facturation pour votre contrat de client de Microsoft. Pour en savoir plus sur le compte de facturation, consultez [comprendre le compte de facturation](billing-mca-overview.md#understand-billing-account).
2. Le profil de facturation vous permet de gérer la facturation pour votre organisation, similaire à l’inscription de votre contrat entreprise. Les administrateurs d’entreprise deviennent propriétaires du profil de facturation. Pour en savoir plus sur les profils de facturation, consultez [comprendre les profils de facturation](billing-mca-overview.md#understand-billing-profiles).
3. Une section de facture permet d’organiser vos coûts en fonction de vos besoins, similaires aux départements dans votre inscription contrat entreprise. Service devient sections de facture et aux administrateurs de service deviennent propriétaires des sections de facture respectifs. Pour en savoir plus sur les sections de facture, consultez [comprendre les sections de facture](billing-mca-overview.md#understand-invoice-sections).
4. Les comptes qui ont été créés dans votre contrat entreprise ne sont pas pris en charge dans le nouveau compte de facturation. Abonnements du compte appartiennent à la section facture respectifs pour leur département. Les propriétaires de compte peuvent créer et gérer des abonnements pour leurs sections de facture.

## <a name="understand-changes-to-your-billing-administrators-access"></a>Comprendre les modifications apportées à l’accès de vos administrateurs de facturation

En fonction de leur accès, administrateurs de facturation sur votre inscription contrat entreprise ont accès aux facturation étendues sur le nouveau compte. Le tableau suivant explique le changement d’accès lors de l’installation :

| Rôle existant | Rôle de transition de publication |
| --- | --- |
| **Administrateur d’entreprise (en lecture uniquement = No)** | **-Propriétaire de profil de facturation** </br> Gérer toutes les opérations sur le profil de facturation </br> - **Propriétaire de section de facture sur toutes les sections de facture** </br> Gérer toutes les opérations sur les sections de facture |
| **Administrateur d’entreprise (en lecture uniquement = Yes)** | **-Lecteur de profil de facturation** </br> Vue en lecture seule de tous les éléments sur le compte de facturation</br>**-Lecteur de section facture sur toutes les factures section**</br> Vue en lecture seule de tous les éléments dans la section de la facture|
| **Administrateur de service (en lecture uniquement = No)** |**-Propriétaire de section facture sur la section de facture créé pour leur département respectif** </br>Gérer tout le contenu de la section de la facture|
| **Administrateur de service (en lecture uniquement = Yes)**|**-Lecteur de section facture sur la section de facture créée pour leur département respectif**</br> Vue en lecture seule de tous les éléments dans la section de la facture|
| **Propriétaire du compte** | **-Créateur de l’abonnement azure dans la section de facture créée pour leur département respectif** </br>  Créer des abonnements Azure pour leur section facture|

Un locataire Azure Active Directory est sélectionné pour le nouveau compte de facturation lors de la signature de l’accord de client de Microsoft. Si un client n’existe pas pour votre organisation, un nouveau locataire est créé. Le locataire représente votre organisation dans Azure Active Directory. Administrateurs de locataires général de votre organisation utilisent le client pour gérer l’accès des applications et des données de votre organisation.

Votre nouveau compte prend uniquement en charge les utilisateurs du locataire qui a été sélectionné lors de la signature de l’accord de client de Microsoft. Si les utilisateurs disposant des autorisations d’administration sur votre contrat entreprise font partie du client, ils obtiendront l’accès au nouveau compte de facturation lors de l’installation. S’ils ne sont pas partie du locataire, ils ne pourront accéder le nouveau compte de facturation, sauf si vous les inviter.

Si vous souhaitez inviter les utilisateurs, ils sont ajoutés au locataire en tant qu’utilisateurs invités et accédez au compte de facturation. Pour inviter les utilisateurs, l’accès invité doit être activé pour le locataire. Pour plus d’informations, consultez [contrôler l’accès des invités dans Azure Active Directory](https://docs.microsoft.com/en-us/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory). Si l’accès invité est désactivé, contactez les administrateurs généraux de votre client pour l’activer. <!-- Todo - How can they find their global administrator -->

## <a name="view-features-replaced-by-the-new-billing-account"></a>Afficher les fonctionnalités remplacées par le nouveau compte de facturation

Les fonctionnalités de l’accord entreprise suivantes sont remplacées par les nouvelles fonctionnalités dans le compte de facturation d’un contrat de client de Microsoft.

### <a name="enterprise-agreement-accounts"></a>Comptes contrat entreprise

Les comptes qui ont été créés dans votre inscription contrat entreprise ne sont pas pris en charge dans le nouveau compte de facturation. Abonnements du compte appartiennent à la section de facture créée pour leur service respectif. Les propriétaires de comptes deviennent des créateurs de l’abonnement Azure peuvent créer et gérer des abonnements pour leurs sections de facture.

### <a name="notification-contacts"></a>Contacts de notification

Contacts de notification sont envoyés à des communications par e-mail sur l’accord entreprise Azure. Ils ne sont pas pris en charge dans le nouveau compte de facturation. Des messages électroniques concernant les crédits Azure et les factures sont envoyés aux utilisateurs qui ont accès aux profils de facturation dans votre compte de facturation.

### <a name="spending-quotas"></a>Quotas de dépense

Quotas de dépenses qui ont été définies pour les départements dans votre inscription contrat entreprise sont remplacées par des budgets dans le nouveau compte de facturation. Un budget est créé pour chaque quota de dépense défini sur les services dans votre inscription. Pour plus d’informations sur le budget, consultez [créer et gérer des budgets Azure](../cost-management/manage-budgets.md).

### <a name="cost-centers"></a>Centres de coûts

Centre de coûts qui ont été définies sur les abonnements Azure dans votre inscription contrat entreprise sont reportées dans le nouveau compte de facturation. Toutefois, les centres de coût pour les départements et les comptes contrat entreprise ne sont pas pris en charge.

## <a name="additional-information"></a>Informations supplémentaires

Les sections suivantes fournissent des informations supplémentaires sur la configuration de votre compte de facturation.

### <a name="no-service-downtime"></a>Aucune interruption de service.

Les services Azure compris dans votre abonnement ne subissent aucune interruption. Nous transférons uniquement la relation de facturation pour vos abonnements Azure. Les ressources, groupes de ressources ou groupes d’administration existants ne sont pas affectés.

### <a name="user-access-to-azure-resources"></a>Accès utilisateur aux ressources Azure

Accès aux ressources Azure qui a été défini à l’aide d’Azure RBAC (contrôle d’accès en fonction du rôle) n’est pas affectée lors de la transition.

### <a name="azure-reservations"></a>Réservations Azure

Les réservations d’Azure dans votre inscription contrat entreprise est déplacée vers votre nouveau compte de facturation. Pendant la transition, les remises de réservation appliquées à vos abonnements ne subiront aucune modification.

### <a name="azure-marketplace-products"></a>Produits de la place de marché Azure

Les produits de la place de marché Azure dans votre contrat entreprise sont déplacés, ainsi que les abonnements. Il n’y avoir toute modification apportée à l’accès au service des produits de la place de marché pendant la transition.

### <a name="support-plan"></a>Plan de support

Avantages de la prise en charge ne sont pas transférés dans le cadre de la transition. Achetez un nouveau plan de support technique pour obtenir des avantages des abonnements Azure dans votre nouveau compte de facturation.

### <a name="past-charges-and-balance"></a>Au-delà de la facturation et le solde

Solde de frais et les crédits avant la transition sont consultables dans l’inscription de votre contrat entreprise via le portail Azure. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>Lorsque le programme d’installation doit être effectuée ?

Terminer la configuration de votre compte de facturation avant l’expiration de votre inscription contrat entreprise. Si votre inscription expire, services dans vos abonnements Azure seront toujours conserver de s’exécuter sans interruption. Toutefois, vous êtes facturé pour les services, les prix de détail.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Modifications apportées à l’inscription de contrat entreprise après l’installation

Abonnements Azure sont créés pour l’inscription contrat entreprise après que la transition peut être déplacée manuellement vers le nouveau compte de facturation. Pour plus d’informations, consultez [obtenir la propriété des abonnements Azure à partir d’autres utilisateurs de facturation](billing-mca-request-billing-ownership.md). Pour déplacer des réservations d’Azure qui peuvent être achetés après la transition, [contactez le Support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Vous pouvez également fournir aux utilisateurs l’accès au compte de facturation après la transition. Pour plus d’informations, consultez [gérer les rôles de facturation dans le portail Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>Rétablir la transition

La transition ne peut pas être annulée. Une fois que la facturation de vos abonnements Azure subit une transition vers le nouveau compte de facturation, vous ne pouvez pas le revenir à l’inscription de votre contrat entreprise.

### <a name="closing-your-browser-during-setup"></a>Fermer votre navigateur pendant l’installation

Avant de cliquer sur **démarrer transition**, vous pouvez fermer le navigateur. Vous pouvez revenir à l’installation en utilisant le lien que vous avez obtenu dans l’e-mail et commencez la transition. Si vous fermez le navigateur, après le démarrage de la transition, votre transition continuer à fonctionner. Revenir à la page d’état de transition pour surveiller l’état le plus récent de votre transition. Vous obtiendrez un message électronique lorsque la transition est terminée.

## <a name="complete-the-setup-in-the-azure-portal"></a>Terminer l’installation dans le portail Azure

Pour terminer l’installation, vous devez accéder à la fois le nouveau compte de facturation et l’inscription contrat entreprise. Pour plus d’informations, consultez [accès requis pour terminer la configuration de votre compte de facturation](#access-required-to-complete-the-setup).

1. Connectez-vous au portail Azure en utilisant le lien dans l’e-mail qui vous a été envoyé lorsque vous signé le contrat de client de Microsoft.

2. Si quelqu'un d’autre dans votre organisation signé le contrat, ou vous n’avez pas reçu l’e-mail, connectez-vous à l’aide du lien suivant. Remplacez **enrollmentNumber** avec le numéro d’inscription de votre contrat entreprise qui a été renouvelé.

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. Sélectionnez **démarrer transition** dans la dernière étape de l’installation. Une fois que vous sélectionnez la transition de début :

    ![Capture d’écran montrant l’Assistant Installation](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - Une hiérarchie de facturation correspondant à votre hiérarchie de contrat entreprise est créée dans le nouveau compte de facturation. Pour plus d’informations, consultez [comprendre les modifications apportées à votre hiérarchie de facturation](#understand-changes-to-your-billing-hierarchy).
    - Les administrateurs à partir de votre inscription contrat entreprise obtiennent un accès au nouveau compte de facturation afin qu’ils continuent à gérer la facturation pour votre organisation.
    - La facturation de vos abonnements Azure subit une transition vers le nouveau compte. **Il ne sera pas aucun impact sur vos services Azure durant cette transition. Allons poursuivre sans interruption**.
    - Si vous avez des réservations d’Azure, ils sont transférés vers votre nouveau compte de facturation avec les mêmes remise et le même terme. Votre remise de réservation continueront à être appliquées pendant la transition.

4. Vous pouvez surveiller l’état de la transition sur la **la Transition d’état** page.

   ![Capture d’écran qui affiche l’état de transition](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-the-billing-account-is-set-up-properly"></a>Valider la facturation compte est correctement configuré

 Valider les éléments suivants pour vous assurer de que votre nouveau compte de facturation est correctement configuré :

### <a name="azure-subscriptions"></a>Abonnements Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Effectuez une recherche sur **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Sélectionnez le profil de facturation. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation. Dans le compte de facturation, sélectionnez **profils de facturation** et ensuite le profil de facturation.

4. Sélectionnez **abonnements Azure** sur le côté gauche.

   ![Capture d’écran montrant la liste des abonnements](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

Les abonnements Azure qui sont transférées à partir de l’inscription de votre accord entreprise vers le nouveau compte de facturation sont affichés dans la page Abonnements Azure. Si vous pensez que n’importe quel abonnement est manquant, la transition la facturation de l’abonnement manuellement dans le portail Azure. Pour plus d’informations, consultez [obtenir la propriété des abonnements Azure à partir d’autres utilisateurs de facturation](billing-mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Réservations Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Effectuez une recherche sur **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Sélectionnez une section de la facture. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation ou un profil de facturation.  Dans le compte de facturation ou le profil de facturation, sélectionnez **sections de facture** et ensuite une section de la facture.

    ![Capture d’écran montrant la liste de transition de facture section post](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Sélectionnez **tous les produits** sur le côté gauche.

5. Effectuez une recherche sur **réservé**.

    ![Capture d’écran montrant la liste des abonnements post transition](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

Réservations d’Azure qui sont déplacées à partir de l’inscription de votre accord entreprise vers le nouveau compte de facturation sont affichées sur la page tous les produits. Répétez les étapes pour toutes les sections de facture vérifier que toutes les réservations d’Azure sont déplacées à partir de votre inscription contrat entreprise. Si vous pensez qu’une réservation de Azure est manquante, [contacter le support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour déplacer la réservation vers le nouveau compte de facturation.

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>Accès des administrateurs d’entreprise sur le profil de facturation

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Effectuez une recherche sur **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. Sélectionnez le profil de facturation créé pour votre inscription. Selon votre accès, vous devrez peut-être sélectionner un compte de facturation.  Dans le compte de facturation, sélectionnez **profils de facturation** et ensuite le profil de facturation.

4. Sélectionnez **contrôle d’accès (IAM)** sur le côté gauche.

   ![Capture d’écran qui illustre l’accès de l’entreprise administrateurs post transition](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

Les administrateurs d’entreprise sont répertoriés en tant que la facturation des propriétaires de profil lors de l’entreprise, les administrateurs disposant d’autorisations en lecture seule sont répertoriés en tant que lecteurs de profil de facturation. Si vous pensez que l’accès pour les administrateurs d’entreprise est manquant, vous pouvez leur donner de l’accès dans le portail Azure. Pour plus d’informations, consultez [gérer les rôles de facturation dans le portail Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>Accès des administrateurs d’entreprise, aux administrateurs de service et les propriétaires de comptes sur des sections de facture

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Effectuez une recherche sur **Gestion des coûts + facturation**.

   ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-mca-setup-account/billing-search-cost-management-billing.png).

3. Sélectionnez une section de la facture. Les sections de facture ont le même nom que leur service respectif dans les inscriptions contrat entreprise. Selon votre accès, vous devrez peut-être sélectionner un profil de facturation ou d’un compte de facturation. Dans le profil de facturation ou le compte de facturation, sélectionnez **sections de facture** , puis sélectionnez une section de la facture.

   ![Capture d’écran montrant la liste de transition de facture section post](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. Sélectionnez **contrôle d’accès (IAM)** sur le côté gauche.

    ![Capture d’écran qui illustre l’accès de service et le compte administrateurs d’accès post transition](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

Les administrateurs d’entreprise et aux administrateurs de service sont répertoriés comme propriétaires de section facture ou lecteurs de section facture tandis que les propriétaires de compte du service sont répertoriés en tant que créateurs d’abonnement Azure. Répétez l’étape pour toutes les sections de facture vérifier l’accès à tous les services de votre inscription contrat entreprise. Les propriétaires de compte qui n’étaient pas de partie de n’importe quel service obtenir l’autorisation sur une section de facture nommée **section de facture par défaut**. Si vous pensez que l’accès pour tous les administrateurs est manquant, vous pouvez leur donner de l’accès dans le portail Azure. Pour plus d’informations, consultez [gérer les rôles de facturation dans le portail Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez toujours besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes

- [Bien démarrer avec votre nouveau compte de facturation](billing-mca-overview.md)

- [Effectuer des tâches de contrat entreprise dans votre compte de facturation d’un contrat de client de Microsoft](billing-mca-enterprise-operations.md)

- [Gérer l’accès à votre compte de facturation](billing-understand-mca-roles.md)
