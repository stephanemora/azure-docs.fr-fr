---
title: Administration du portail Azure pour les contrats Enterprise directs
description: Cet article présente les tâches courantes à la charge d’un administrateur d’entreprise direct dans le portail Azure.
author: bandersmsft
ms.author: banders
ms.date: 10/11/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: sapnakeshari
ms.openlocfilehash: d958ea620e16dbf7ab4e9181d05508246b832907
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007380"
---
# <a name="azure-portal-administration-for-direct-enterprise-agreements"></a>Administration du portail Azure pour les contrats Enterprise directs

Cet article présente les tâches courantes à la charge d’un administrateur de contrat Entreprise (EA) direct dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes). Un contrat Entreprise direct est signé entre Microsoft et un client de contrat Entreprise.

À l’inverse, un contrat Entreprise indirect est un contrat dans lequel un client signe un accord avec un partenaire Microsoft. Les EA (Contrats Entreprise) indirects sont gérés à l’aide du [portail Azure Enterprise](https://ea.azure.com/). Pour plus d’informations sur la gestion des EA indirects, consultez [Administration du portail Azure EA](ea-portal-administration.md).

## <a name="manage-your-enrollment"></a>Gérer votre inscription

Pour gérer votre service, l’administrateur d’entreprise initial ouvre le  [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) et se connecte en utilisant l’adresse e-mail indiquée dans son e-mail d’invitation.

Si vous êtes l’administrateur d’entreprise, accédez au portail Azure et connectez-vous avec votre adresse e-mail professionnelle, scolaire ou de compte Microsoft.

Si vous disposez de plusieurs comptes de facturation, sélectionnez un compte de facturation dans le menu de l’étendue de facturation. Vous pouvez afficher les propriétés et la stratégie de votre compte de facturation dans le menu de gauche.

## <a name="select-a-billing-scope"></a>Sélectionner une étendue de facturation

Les contrats Enterprise et les clients qui accèdent aux contrats peuvent avoir plusieurs inscriptions. Un utilisateur peut avoir accès à plusieurs étendues d’inscription (étendues de compte de facturation). Toutes les informations et activités dans le portail Azure surviennent dans le contexte d’une étendue de compte de facturation. Il est important que l’administrateur d’entreprise sélectionne d’abord une étendue de facturation avant d’effectuer des tâches d’administration.

### <a name="to-select-a-billing-scope"></a>Pour sélectionner une étendue de facturation

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Recherchez **Cost Management + Billing**, puis sélectionnez cette option.  
    :::image type="content" source="./media/direct-ea-administration/search-cost-management.png" alt-text="Capture d’écran montrant une recherche sur Gestion des coûts + facturation." lightbox="./media/direct-ea-administration/search-cost-management.png" :::
1. Sélectionnez **Étendues de facturation** dans le menu de navigation, puis sélectionnez le compte de facturation que vous souhaitez utiliser.  
    :::image type="content" source="./media/direct-ea-administration/select-billing-scope.png" alt-text="Capture d’écran montrant un compte de facturation sélectionné." lightbox="./media/direct-ea-administration/select-billing-scope.png" :::

## <a name="view-enrollment-details"></a>Afficher les détails de l’inscription

Un administrateur Azure Enterprise (EA) peut afficher et gérer les propriétés et la stratégie d’inscription pour s’assurer que les paramètres d’inscription sont configurés correctement.

### <a name="to-view-enrollment-properties"></a>Pour afficher les propriétés de l’inscription

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez **Étendues de facturation**, puis sélectionnez une étendue de compte de facturation.
1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Propriétés**.
1. Affichez les informations du compte de facturation.  
    :::image type="content" source="./media/direct-ea-administration/billing-account-properties.png" alt-text="Capture d’écran montrant les propriétés du compte de facturation." lightbox="./media/direct-ea-administration/billing-account-properties.png" :::

### <a name="view-and-manage-enrollment-policies"></a>Afficher et gérer les stratégies d’inscription

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez **Étendues de facturation**, puis sélectionnez une étendue de compte de facturation.
1. Dans le menu de navigation, sélectionnez **Stratégies**.
1. Activez ou désactivez des stratégies en sélectionnant **Activé** ou **Désactivé**.  
    :::image type="content" source="./media/direct-ea-administration/enrollment-policies.png" alt-text="Capture d’écran montrant des stratégies d’inscription EA." lightbox="./media/direct-ea-administration/enrollment-policies.png" :::

Pour plus d’informations sur les paramètres de stratégie des frais de l’administrateur de service (DA) et du propriétaire du compte (AO), consultez [Tarification des différents rôles d’utilisateur](understand-ea-roles.md#see-pricing-for-different-user-roles).

## <a name="add-another-enterprise-administrator"></a>Ajouter un autre administrateur d’entreprise

Seuls les administrateurs EA existants peuvent créer d’autres administrateurs d’entreprise. Utilisez l’une des options suivantes selon votre situation.

### <a name="if-youre-already-an-enterprise-administrator"></a>Si vous êtes déjà administrateur d’entreprise

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez **Étendues de facturation**, puis sélectionnez une étendue de compte de facturation.
1. Dans le menu de gauche, sélectionnez **Contrôle d’accès (IAM)** .
1. Dans le menu supérieur, sélectionnez **+ Ajouter**, puis sélectionnez **Administrateur d’entreprise**.  
    :::image type="content" source="./media/direct-ea-administration/add-enterprise-admin-navigate.png" alt-text="Capture d’écran montrant la navigation vers l’administrateur d’entreprise." lightbox="./media/direct-ea-administration/add-enterprise-admin-navigate.png" :::
1. Complétez le formulaire d’ajout d’une attribution de rôle, puis sélectionnez **Ajouter**.

Assurez-vous que vous avez l’adresse e-mail de l’utilisateur et la méthode d’authentification par défaut, par exemple l’authentification avec un compte professionnel, scolaire ou Microsoft.

Un administrateur EA peut gérer l’accès pour les administrateurs d’entreprise existants en sélectionnant le symbole des points de suspension ( **…** ) à droite de chaque utilisateur. Ils peuvent **modifier** et **supprimer** des utilisateurs existants.

### <a name="if-youre-not-an-enterprise-administrator"></a>Si vous n’êtes pas administrateur d’entreprise

Si vous n’êtes pas administrateur EA, contactez votre administrateur EA pour lui demander de vous ajouter à une inscription. L’administrateur EA utilise les étapes précédentes pour vous ajouter comme administrateur d’entreprise. Une fois que vous avez été ajouté à une inscription, vous recevez un e-mail d’activation.

### <a name="if-your-enterprise-administrator-cant-help-you"></a>Si votre administrateur d’entreprise ne peut pas vous aider

Si votre administrateur d'entreprise ne peut pas vous aider, créez une  [demande de support Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Fournissez les informations suivantes :

- Numéro d’inscription
- Adresse e-mail à ajouter et type d’authentification (avec un compte professionnel, scolaire ou Microsoft)
- Approbation reçue par e-mail d’un administrateur d’entreprise existant

Si l’administrateur d’entreprise existant n’est pas disponible, contactez votre partenaire ou votre conseiller logiciel pour lui demander de modifier les détails de contact dans l’outil du Centre de gestion des licences en volume (VLSC).

## <a name="create-an-azure-enterprise-department"></a>Créer un service Azure Enterprise

Les administrateurs EA et les administrateurs de service utilisent les services pour organiser les services Entreprise Azure, et créer des rapports sur l’utilisation par service et par centre de coûts. L’administrateur d’entreprise peut :

- ajouter ou supprimer des services ;
- associer un compte à un service ;
- créer des administrateurs de service ;
- autoriser les administrateurs de service à consulter les tarifs et les coûts.

Un administrateur de service peut ajouter de nouveaux comptes à ses services. Ils peuvent également supprimer des comptes de ses services, mais pas de l’inscription.

### <a name="to-create-a-department"></a>Pour créer un service

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez **Étendues de facturation**, puis sélectionnez une étendue de compte de facturation.
1. Dans le menu de navigation gauche, sélectionnez **Services**.
1. Sélectionnez **Ajouter**.  
    :::image type="content" source="./media/direct-ea-administration/add-department.png" alt-text="Capture d’écran montrant la navigation pour ajouter un service." lightbox="./media/direct-ea-administration/add-department.png" :::
1. Sous Ajouter un nouveau service à partir de, entrez vos informations. Le nom du service est le seul champ obligatoire. Le nom du service doit contenir au moins trois caractères.
1. Quand vous avez terminé, sélectionnez **Enregistrer**.

## <a name="add-a-department-administrator"></a>Ajouter un administrateur de service

Après avoir créé un service, l’administrateur EA peut ajouter des administrateurs de service et associer chacun d’eux à un service. Les administrateurs de service peuvent effectuer les actions suivantes pour leurs services :

- Créer d’autres administrateurs de service
- Consulter et modifier les propriétés d’un service, telles que le nom ou le centre de coûts
- Ajouter des comptes
- Supprimer des comptes
- Télécharger les détails d'utilisation
- Afficher l’utilisation et les frais mensuels <sup>1</sup>

 <sup>1</sup> Un administrateur EA doit accorder les autorisations.

### <a name="to-add-a-department-administrator"></a>Pour ajouter un administrateur de service

En tant qu’administrateur d’entreprise :

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez **Étendues de facturation**, puis sélectionnez une étendue de compte de facturation.
1. Dans le menu de navigation, sélectionnez **Services**.
1. Sélectionnez le service dans lequel vous voulez ajouter un administrateur.
1. Dans la vue du service, sélectionnez **Access Control (IAM)** .
1. Sélectionnez **+ Ajouter**, puis **Administrateur de service**.
1. Entrez l’adresse e-mail et les autres informations requises.
1. Pour obtenir l’accès en lecture seule, définissez l’option **Lecture seule** sur **Oui**, puis sélectionnez **Ajouter**.  
    :::image type="content" source="./media/direct-ea-administration/add-department-admin.png" alt-text="Capture d’écran montrant la navigation vers l’administrateur du service." lightbox="./media/direct-ea-administration/add-department-admin.png" :::

### <a name="to-set-read-only-access"></a>Pour accorder l’accès en lecture seule

Les administrateurs EA peuvent accorder l’accès en lecture seule aux administrateurs de service. Lorsque vous créez un administrateur de service, définissez l’option de lecture seule sur **Oui**.

Pour changer l’accès d’un administrateur de service existant :

1. Sélectionnez le symbole de points de suspension ( **…** ) sur le côté droit d’une ligne et sélectionnez-le.
1. Définissez l’option lecture seule ouverte sur   **Oui**, puis sélectionnez  **Appliquer**.

Les administrateurs d’entreprise obtiennent automatiquement les autorisations d’administrateur de service.

## <a name="add-an-account-and-account-owner"></a>Ajouter un compte et un propriétaire de compte

La structure des comptes et des abonnements affecte la façon dont ces éléments sont administrés, ainsi que leur mode d’affichage dans vos factures et rapports. Les divisions métier, les équipes fonctionnelles et les emplacements géographiques sont des exemples de structures organisationnelles courantes.

Après l’ajout d’un nouveau compte à l’inscription, le propriétaire du compte reçoit un e-mail de propriété de compte confirmant la propriété.

### <a name="to-add-an-account-and-account-owner"></a>Pour ajouter un compte et un propriétaire de compte

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez **Étendues de facturation**, puis sélectionnez une étendue de compte de facturation.
1. Dans le menu de gauche, sélectionnez **Comptes**.
1. Sélectionnez **Ajouter**.
1. Dans la page Ajouter un compte, entrez un nom convivial pour identifier le compte utilisé pour la création de rapports.
1. Tapez l’adresse **e-mail du propriétaire du compte** à associer au nouveau compte.
1. Sélectionnez un service ou laissez-le comme non affecté.
1. Quand vous avez terminé, sélectionnez **Ajouter**.

### <a name="to-confirm-account-ownership"></a>Pour confirmer la propriété du compte

Lorsque le propriétaire du compte reçoit un e-mail de propriété de compte, il doit confirmer sa propriété.

1. Connectez-vous au compte de messagerie associé au compte professionnel, scolaire ou Microsoft défini en tant que propriétaire du compte.
1. Ouvrez l’e-mail de notification intitulé _« Invitation à activer votre compte sur le service Microsoft Azure »_ .
1. Sélectionnez le lien **Activer le compte** dans l’invitation.
1. Connectez-vous au portail Azure.
1. Sur la page Activer le compte, sélectionnez **Oui, je souhaite continuer** pour confirmer la propriété du compte.  
    :::image type="content" source="./media/direct-ea-administration/activate-account.png" alt-text="Capture d’écran montrant la page Activer le compte." lightbox="./media/direct-ea-administration/activate-account.png" :::

Une fois la propriété du compte confirmée, vous pouvez créer des abonnements et acheter des ressources avec les abonnements.

## <a name="change-azure-subscription-or-account-ownership"></a>Changer l’abonnement Azure ou la propriété du compte

Les administrateurs EA peuvent utiliser le portail Azure pour transférer la propriété du compte des abonnements sélectionnés ou de tous les abonnements dans une inscription. Quand vous effectuez un transfert d’abonnement ou de propriété de compte, Microsoft met à jour le propriétaire du compte.

Avant de commencer le transfert de propriété, familiarisez-vous avec les stratégies de contrôle d’accès en fonction du rôle (RBAC) Azure suivantes :

- Lorsque vous transférez un abonnement ou une propriété de compte entre deux ID d’organisation au sein du même locataire, les éléments suivants sont conservés :
    - Stratégies Azure RBAC
    - Administrateur de service existant
    - Rôles de coadministrateur
- Les transferts d’abonnement ou de propriété de compte inter-locataires entraînent la perte de vos stratégies Azure RBAC et de vos attributions de rôles.
- Les stratégies et les rôles d’administrateur ne sont pas transférés entre les annuaires. Les administrateurs de service sont mis à jour pour refléter le propriétaire du compte de destination.
- Pour éviter la perte des stratégies Azure RBAC et des attributions de rôles lors du transfert de l’abonnement entre les locataires, veillez à ce que la case **Déplacer les abonnements vers le locataire Azure AD du destinataire** reste décochée. Cette sélection conserve les services, les rôles Azure et les stratégies sur le locataire Azure AD actuel, et transfère uniquement la propriété de facturation pour le compte.

Avant de changer le propriétaire d’un compte :

1. Affichez l’onglet **Account** (Compte) et identifiez le compte source. Le compte source doit être actif.
1. Identifiez le compte de destination et assurez-vous qu’il est actif.

Pour transférer la propriété du compte pour tous les abonnements :

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez **Étendues de facturation**, puis sélectionnez une étendue de compte de facturation.
1. Dans le menu de gauche, sélectionnez **Comptes**.
1. Sélectionnez un **compte**, puis à droite de la ligne, sélectionnez le symbole de points de suspension l'ellipse ( **…** ).
1. Sélectionnez **Transférer des abonnements**.  
    :::image type="content" source="./media/direct-ea-administration/transfer-subscriptions-01.png" alt-text="Capture d’écran montrant où transférer les abonnements." lightbox="./media/direct-ea-administration/transfer-subscriptions-01.png" :::
1. Sur la page Transférer des abonnements, sélectionnez le compte de destination vers lequel vous souhaitez effectuer le transfert, puis sélectionnez **Suivant**.
1. Si vous souhaitez transférer la propriété du compte entre des locataires Azure AD, sélectionnez la confirmation **Yes, I would also like to move the subscriptions to the new account's Azure AD tenant** (Oui, je souhaite également déplacer les abonnements vers le locataire Azure AD du nouveau compte).
1. Confirmez le transfert et sélectionnez  **Envoyer**.  
    :::image type="content" source="./media/direct-ea-administration/transfer-account-confirmation.png" alt-text="Capture d’écran montrant la confirmation de transfert de l’abonnement." lightbox="./media/direct-ea-administration/transfer-account-confirmation.png" :::

Pour transférer la propriété du compte pour un seul abonnement :

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez **Étendues de facturation**, puis sélectionnez une étendue de compte de facturation.
1. Dans le menu de gauche, sélectionnez **Abonnements Azure**.
1. Sur la page Abonnements Azure, à droite d’un abonnement, sélectionnez le symbole représentant des points de suspension ( **…** ).
1. Sélectionnez **Transférer un abonnement**.
1. Sur la page Transférer des abonnements, sélectionnez le compte de destination vers lequel vous souhaitez transfert l’abonnement, puis sélectionnez   **Suivant**.
1. Si vous souhaitez transférer la propriété de l’abonnement entre des locataires Azure AD, sélectionnez l’option **Yes, I would like to also move the subscriptions to the to the new account's Azure AD tenant** (Oui, je souhaite également déplacer les abonnements vers le locataire Azure AD du nouveau compte).
1. Confirmez le transfert, puis sélectionnez **Envoyer**.

## <a name="associate-an-account-to-a-department"></a>Associer un compte à un service

Les administrateurs EA peuvent associer des comptes existants à des services dans l’inscription.

### <a name="to-associate-an-account-to-a-department"></a>Pour associer un compte à un service

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez **Étendues de facturation**, puis sélectionnez une étendue de compte de facturation.
1. Dans le menu de gauche, sélectionnez **Comptes**.
1. Sélectionnez le symbole de points de suspension ( **…** ) sur le côté droit d’un compte, puis choisissez **Modifier**.
1. Sur la page Modifier le compte, sélectionnez un **service** dans la liste.
1. Sélectionnez  **Enregistrer**.

## <a name="associate-an-account-with-a-pay-as-you-go-subscription"></a>Associer un compte à un abonnement avec paiement à l’utilisation

Si vous disposez déjà d’un compte scolaire, professionnel ou Microsoft pour un abonnement Azure avec paiement à l’utilisation, vous pouvez l’associer à votre inscription Contrat Entreprise.

Pour associer un compte, vous devez disposer du rôle d’administrateur EA ou d’administrateur de service.

### <a name="to-associate-an-existing-account"></a>Pour associer un compte existant

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez **Étendues de facturation**, puis sélectionnez une étendue de compte de facturation.
1. Dans le menu de gauche, sélectionnez **Comptes**.
1. Sélectionnez **Ajouter**.
1. Sur la page Ajouter un compte, entrez le nom que vous souhaitez utiliser pour identifier le compte à des fins de création de rapports.
1. Sélectionnez un compte **Microsoft** ou un **compte professionnel ou scolaire** associé au compte Azure existant.
1. Entrez l’adresse e-mail du propriétaire du compte, puis confirmez-la.
1. Sélectionnez **Ajouter**.

L’affichage du compte dans le portail Azure peut prendre jusqu’à huit heures.

### <a name="to-confirm-account-ownership"></a>Pour confirmer la propriété du compte

1. Connectez-vous au compte de messagerie associé au compte professionnel, scolaire ou Microsoft que vous avez associé à la procédure précédente.
1. Ouvrez l’e-mail de notification intitulé _« Invitation à activer votre compte sur le service Microsoft Azure »_ .
1. Sélectionnez le lien **Activer le compte** dans l’invitation.
1. Connectez-vous au portail Azure.
1. Sur la page Activer le compte, sélectionnez **Oui, je souhaite continuer** pour confirmer la propriété du compte.  
    :::image type="content" source="./media/direct-ea-administration/activate-account.png" alt-text="Capture d’écran montrant la page Activer le compte." lightbox="./media/direct-ea-administration/activate-account.png" :::

## <a name="enable-azure-marketplace-purchases"></a>Activer les achats sur la Place de marché Azure

Même si la plupart des _abonnements_ avec paiement à l’utilisation peuvent être associés à un Contrat Entreprise Azure, ce n’est pas le cas des _services_ achetés précédemment sur la Place de marché Azure. Pour obtenir une vue unique de la totalité des abonnements et frais, nous vous recommandons d’activer les achats sur la Place de marché Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez **Étendues de facturation**, puis sélectionnez une étendue de compte de facturation.
1. Dans le menu de gauche, sélectionnez **Stratégies**.
1. Sous **Place de marché Azure**, définissez la stratégie sur **Activé**.  
   :::image type="content" source="./media/direct-ea-administration/azure-marketplace.png" alt-text="Capture d’écran montrant le paramètre de stratégie de la Place de marché Azure." lightbox="./media/direct-ea-administration/azure-marketplace.png" :::

Le propriétaire du compte peut alors acheter des services de la Place de marché Azure qui faisaient précédemment partie de l’abonnement avec paiement à l’utilisation.

Le paramètre s’applique à tous les propriétaires de comptes dans l’inscription. Cela leur permet de faire des achats sur la Place de marché Azure.

Une fois les abonnements activés dans votre inscription Azure EA, annulez les services de la Place de marché Azure créés à l’aide de l’abonnement avec paiement à l’utilisation. Cette étape est essentielle dans le cas où votre solution avec paiement à l’utilisation expire.

## <a name="msdn-subscription-transfer"></a>Transfert d’abonnement MSDN

Lorsque vous transférez un abonnement MSDN vers une inscription EA, l’abonnement est automatiquement converti en abonnement Dev/Test MSDN. Après la conversion, l’abonnement perd tout crédit financier existant. Nous vous recommandons donc d’utiliser tout votre crédit avant de le transférer vers votre Contrat Entreprise.

## <a name="azure-in-open-subscription-transfer"></a>Transfert d’abonnement Azure dans Open

Lorsque vous transférez un abonnement Azure dans Open vers un Contrat Entreprise, vous perdez tous les crédits Azure dans Open inutilisés. Nous vous recommandons d’utiliser tout votre crédit pour l’abonnement Azure dans Open avant de le transférer vers votre Contrat Entreprise.

## <a name="subscription-transfers-with-support-plans"></a>Transferts d’abonnement avec plans de support

Si votre Contrat Entreprise n’inclut aucun plan de support et que vous tentez de transférer un abonnement Microsoft Online Support Agreement (MOSA ) avec un plan de support, l’abonnement ne sera pas automatiquement transféré. Vous devrez racheter un plan de support pour votre inscription de Contrat Entreprise au cours de la période de grâce, qui se termine à la fin du mois suivant.

## <a name="manage-department-and-account-spending-with-budgets"></a>Gérer les dépenses de service et de compte avec des budgets

Les clients EA peuvent définir des budgets pour chaque service ou compte dans le cadre d’une inscription. Les budgets dans Cost Management vous aident à planifier et à suivre la comptabilité de l’organisation. Ils vous permettent d’informer les autres utilisateurs de leurs dépenses pour gérer les coûts de manière proactive, ainsi que pour superviser la progression des dépenses. Vous pouvez configurer des alertes en fonction du coût réel ou prévu pour vous assurer que vos dépenses respectent la limite de dépenses de votre organisation. En cas de dépassement des seuils budgétaires que vous avez créés, seules des notifications sont déclenchées. Aucune de vos ressources n’est affectée et votre consommation n’est pas arrêtée. Vous pouvez utiliser des budgets pour comparer et suivre les dépenses lors de l’analyse des coûts. Pour plus d’informations sur la création de budgets, consultez [Tutoriel : Créer et gérer des budgets Azure](../costs/tutorial-acm-create-budgets.md).

## <a name="enterprise-agreement-user-roles"></a>Rôles d’utilisateur Contrat Entreprise

Le portail Azure vous permet de gérer vos coûts et votre utilisation d’Azure EA. Il existe trois principaux rôles EA :

- Administrateur Contrat Entreprise (EA)
- Administrateur de service
- Propriétaire du compte

Chaque rôle a un niveau d’accès et une autorité différents. Pour plus d’informations sur les rôles d’utilisateur, consultez  [Rôles d’utilisateur Entreprise](understand-ea-roles.md#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Ajouter un compte Azure EA

Un compte Azure EA est une unité d’organisation dans le portail Azure. Dans le portail Azure, il s’agit du terme _compte_. Il est utilisé pour gérer des abonnements, ainsi que pour créer des rapports. Pour accéder aux services Azure et les utiliser, vous devez créer un compte ou vous en faire créer-un. Pour plus d’informations sur les comptes, consultez [Ajouter un compte](#add-an-account-and-account-owner).

## <a name="enable-the-enterprise-devtest-offer"></a>Activer l’offre Enterprise Dev/Test

En tant qu’administrateur EA, vous pouvez autoriser les propriétaires de comptes de votre organisation à créer des abonnements basés sur l’offre EA Dev/Test. Pour ce faire, sélectionnez l’option **Dev/Test** dans les propriétés du compte. Après avoir sélectionné l’option Dev/Test, informez le propriétaire du compte afin qu’il puisse créer les abonnements Dev/Test EA nécessaires pour ses équipes d’abonnés Dev/Test. L’offre permet aux abonnés Visual Studio actifs d’exécuter des charges de travail de développement et de test sur Azure à des tarifs Dev/Test spéciaux. Elle fournit un accès à la galerie complète d’images Dev/Test, notamment Windows 8.1 et Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Pour configurer l’offre Enterprise Dev/Test

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez **Étendues de facturation**, puis sélectionnez une étendue de compte de facturation.
1. Dans le menu de gauche, sélectionnez **Comptes**.
1. Sélectionnez le compte pour lequel vous souhaitez activer l’accès Dev/Test.
1. Sur la page du compte d'inscription, sélectionnez **Modifier**.
1. Sur la page Modifier le compte, sélectionnez **Dev/Test**, puis **Enregistrer**.

Lorsqu’un utilisateur est ajouté en tant que propriétaire du compte, tous les abonnements Azure associés à l’utilisateur qui sont basés sur l’offre avec paiement à l'utilisation Dev/Test ou les offres de crédit mensuel pour les abonnés Visual Studio sont convertis en l’offre EA Dev/Test. Les abonnements basés sur d’autres types d’offres, tels que le paiement à l'utilisation, associés au propriétaire du compte, sont convertis en offres Microsoft Azure Enterprise.

L’offre Dev/Test ne s’applique pas aux clients Azure Government pour le moment.

## <a name="create-a-subscription"></a>Création d’un abonnement

Les propriétaires de compte peuvent consulter et gérer les abonnements. Vous pouvez utiliser des abonnements pour donner aux équipes de votre organisation l’accès à des projets et environnements de développement. Par exemple : 

- Test
- Production
- Développement
- Staging

En créant des abonnements différents pour chacun des environnements d’application, vous sécurisez davantage chaque environnement.

- Vous pouvez également attribuer un compte d’administrateur de service distinct par abonnement.
- Vous pouvez associer plusieurs services aux abonnements.
- Le propriétaire du compte crée des abonnements et attribue un compte d’administrateur de service à chaque abonnement dans son compte.

## <a name="add-a-subscription"></a>Ajouter un abonnement

Les propriétaires de comptes créent des abonnements au sein de leur compte d’inscription. La première fois que vous ajoutez un abonnement à votre compte, vous êtes invité à accepter le contrat d’abonnement à Microsoft Online (MOSA) et un plan tarifaire. Bien qu’ils ne s’appliquent pas aux clients ayant souscrit un Contrat Entreprise, le MOSA et le plan tarifaire sont requis pour créer votre abonnement. L’avenant à votre inscription Contrat Entreprise Microsoft Azure se substitue aux éléments précédemment cités, et votre lien contractuel ne change pas. Lorsque vous y êtes invité, sélectionnez l’option qui indique que vous acceptez les conditions.

_Microsoft Azure Enterprise_  est le nom par défaut lorsqu’un abonnement est créé. Vous pouvez modifier le nom de l’abonnement pour le différencier des autres abonnements dans votre inscription et vous assurer qu’il est reconnaissable dans les rapports au niveau de l’entreprise.

### <a name="to-add-a-subscription"></a>Pour ajouter un abonnement

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez **Étendues de facturation**, puis choisissez une étendue de compte de facturation pour l’inscription.
1. Dans le menu de gauche, sélectionnez **Comptes**.
1. Dans la liste des comptes d’inscription, sélectionnez celui pour lequel vous souhaitez créer un abonnement.
1. Sur la page du compte d'inscription, sélectionnez **+ Nouvel abonnement**.  
    :::image type="content" source="./media/direct-ea-administration/new-subscription.png" alt-text="Capture d’écran montrant l’option Nouvel abonnement." lightbox="./media/direct-ea-administration/new-subscription.png" :::
1. Sur la page Créer un abonnement, entrez un nom d’abonnement, puis sélectionnez **Créer**.

Le nom de l’abonnement apparaît sur les rapports. C’est le nom du projet associé à l’abonnement dans le portail de développement.

Cela peut prendre jusqu’à 24 heures pour que les nouveaux abonnements soient visibles dans la liste des abonnements. Une fois que vous avez créé un abonnement, vous pouvez :

- Modifier les détails de l’abonnement
- Gérer les services de l’abonnement

Vous pouvez également créer des abonnements en accédant à la page Abonnements Azure et en sélectionnant **+ Ajouter**.

## <a name="cancel-a-subscription"></a>Annuler un abonnement

Seuls les propriétaires de comptes peuvent annuler leurs propres abonnements.

Pour supprimer un abonnement dans lequel vous êtes le propriétaire du compte :

1. Connectez-vous au portail Azure avec les informations d’identification associées à votre compte.
1. Dans le menu de navigation, sélectionnez **Abonnements**.
1. Sélectionnez un abonnement.
1. Sur la page Détails de l’abonnement, dans le coin supérieur gauche de la page, **annulez l’abonnement**.
1. Entrez le nom de l’abonnement, choisissez un motif d’annulation, puis sélectionnez **Annuler**.

Pour plus d’informations, consultez  [Que se passe-t-il après l’annulation de mon abonnement ?](cancel-azure-subscription.md#what-happens-after-subscription-cancellation).

## <a name="delete-an-enrollment-account"></a>Supprimer un compte d’inscription

Vous pouvez supprimer un compte d’inscription uniquement lorsqu’il n’y a aucun abonnement actif.

1. Connectez-vous au [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes).
1. Accédez à **Cost Management + facturation**.
1. Dans le menu de gauche, sélectionnez **Étendues de facturation**, puis sélectionnez une étendue de compte de facturation.
1. Dans le menu de gauche, sélectionnez **Comptes**.
1. Dans la liste des comptes, recherchez le compte que vous souhaitez supprimer.
1. Recherchez la ligne de compte à supprimer, sélectionnez le symbole de points de suspension ( **...** ), puis **Supprimer**.
1. Sur la page Supprimer le compte, sélectionnez la confirmation **Yes, I want to delete this account** (Oui, je veux supprimer ce compte), puis choisissez **Supprimer**.

## <a name="azure-sponsorship-offer"></a>Offre Azure Sponsorship

L’offre Azure Sponsorship est un compte Microsoft Azure sponsorisé limité. Elle est disponible sur invitation par e-mail pour certains clients sélectionnés par Microsoft. Si vous bénéficiez de l’offre Microsoft Azure Sponsorship, vous recevrez une invitation par e-mail pour votre ID de compte.

Pour plus d’informations, créez une  [demande de support pour l’activation de Sponsorship](https://aka.ms/azrsponsorship).

## <a name="convert-to-work-or-school-account-authentication"></a>Convertir en authentification de compte professionnel ou scolaire

Les utilisateurs d’Azure Enterprise peuvent convertir un compte Microsoft (MSA ou Live ID) en un compte professionnel ou scolaire. Un compte professionnel ou scolaire utilise le type d’authentification Azure Active Directory.

### <a name="to-begin"></a>Pour commencer

1. Ajoutez le compte professionnel ou scolaire au portail Azure dans le ou les rôles nécessaires.
1. Si vous recevez des erreurs, le compte n’est peut-être pas valide dans Azure Active Directory. Azure utilise le nom d’utilisateur principal (UPN), qui n’est pas toujours identique à l’adresse e-mail.
1. Authentifiez-vous sur le portail Azure à l’aide du compte professionnel ou scolaire.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Pour convertir des abonnements de comptes Microsoft en comptes professionnels ou scolaires

1. Connectez-vous au portail Azure à l’aide du compte Microsoft qui possède les abonnements.
1. Utilisez un transfert de propriété de compte pour passer au nouveau compte.
1. Le compte Microsoft doit être gratuit à partir de tous les abonnements actifs et peut être supprimé.
1. Les comptes supprimés restent visibles dans le portail Azure avec l’état inactif pour des raisons de facturation historiques. Vous pouvez les supprimer de la vue en sélectionnant l’option permettant d’**afficher les comptes actifs uniquement**.

## <a name="azure-ea-term-glossary"></a>Glossaire des termes Azure EA

**Compte**<br>
Une unité d’organisation. Elle est utilisée pour gérer des abonnements et pour créer des rapports.

**Propriétaire du compte**<br>
la personne qui gère les abonnements et les administrateurs de services sur Azure. Elles peuvent afficher les données d’utilisation sur ce compte et ses abonnements associés.

**Abonnement de modification**<br>
abonnement d’un an ou équivalent dans le cadre de la modification de l’inscription.

**Acompte**<br>
acompte pour un montant annuel en devises concernant les services Azure, à un tarif réduit.

**Administrateur de département (ou service)**<br>
personne identifiée qui gère les services, crée des comptes et des propriétaires de comptes, affiche les détails d’utilisation des services qu’elle gère et peut afficher les coûts quand des autorisations lui sont accordées.

**Numéro d’inscription**<br>
identificateur unique fourni par Microsoft pour identifier l’inscription spécifique associée à un Accord Entreprise.

**Administrateur d’entreprise**<br>
personne qui gère les services, les propriétaires de service, les comptes et les propriétaires de comptes sur Azure. Elles peuvent gérer les administrateurs d’entreprise, et afficher les données d’utilisation, les quantités facturées et les frais non facturés pour tous les comptes et abonnements associés à l’inscription d’entreprise.

**Contrat Entreprise**<br>
Un contrat de licence Microsoft pour les clients avec des achats centralisés qui souhaitent normaliser leur organisation entière sur la technologie Microsoft et maintenir une infrastructure informatique sur des logiciels Microsoft.

**Inscription à un contrat Entreprise**<br>
inscription au programme Accord Entreprise fournissant des produits Microsoft en volume à tarif réduit.

**Compte Microsoft**<br>
service web qui permet aux sites participants d’authentifier un utilisateur avec un ensemble unique d’informations d’identification.

**Modification d’inscription Microsoft Azure Enterprise (modification de l’inscription)**<br>
modification signée par une entreprise, qui lui permet d’accéder à Azure dans le cadre de l’inscription de l’entreprise.

**Quantité de ressources consommées**<br>
quantité d’un service Azure utilisée durant un mois.

**Administrateur de services fédérés**<br>
La personne qui accède aux abonnements et aux projets de développement et les gère.

**Abonnement**<br>
Représente un abonnement au portail Azure EA et est un conteneur de services Microsoft Azure gérés par le même administrateur de services.

**Compte professionnel ou scolaire**<br>
pour les organisations qui ont configuré Azure Active Directory avec une fédération dans le cloud, et dont tous les comptes se trouvent sur un seul locataire.

## <a name="enrollment-status"></a>État de l'inscription

**Nouveau**<br>
Cet état est affecté à une inscription dans les 24 heures qui suivent sa création et est mis à jour en état d’attente dans un délais de 24 heures.

**En attente**<br>
L’administrateur de l’inscription doit se connecter au portail Azure. Une fois la connexion établie, l’inscription passe à l’état Active.

**Actif**<br>
L’inscription est active et les comptes et les abonnements peuvent être créés dans le portail Azure. L’inscription reste active jusqu’à la date de fin de l’Accord Entreprise.

**Durée prolongée indéterminée**<br>
une durée prolongée indéterminée prend place quand la date de fin d’Accord Entreprise est atteinte. Il permet aux clients Azure EA qui ont opté pour le terme étendu de continuer à utiliser des services Azure indéfiniment à la fin de leur Accord Entreprise.

Avant que l’inscription Azure EA n’atteigne la date de fin de l’Accord Entreprise, l’administrateur de l’inscription doit prendre l’une des décisions suivantes :

- Renouveler l’inscription en ajoutant un Paiement anticipé Azure supplémentaire.
- Transférer vers une nouvelle inscription.
- Migrer vers le programme d’abonnement Microsoft Online (MOSP).
- Confirmer la désactivation de tous les services associés à l’inscription.

**Expired**<br>
le client Azure EA est exclu du terme prolongé et l’inscription à Azure EA a atteint la date de fin de l’Accord Entreprise. L’inscription va expirer et tous les services associés seront désactivés.

**Transférée**<br>
Les inscriptions où tous les comptes et services associés ont été transférés vers une nouvelle inscription se voit appliquer l’état Transférée.
 > [!NOTE]
 > Les inscriptions ne sont pas automatiquement transférées si un nouveau numéro d’inscription est généré lors du renouvellement. Vous devez inclure votre numéro d’inscription précédent votre demande de renouvellement pour qu’un transfert automatique puisse avoir lieu.

## <a name="next-steps"></a>Étapes suivantes

- Si vous avez besoin de créer une demande de support Azure pour votre inscription EA, consultez [Guide pratique pour créer une demande de support Azure pour un problème lié au Contrat Entreprise](how-to-create-azure-support-request-ea.md).
- Consultez les [questions fréquentes (FAQ) sur Cost Management + Billing](../cost-management-billing-faq.yml) pour voir les questions sur la propriété d’abonnement EA.