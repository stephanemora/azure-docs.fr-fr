---
title: Bien démarrer avec le portail Azure Enterprise
description: Cet article explique comment les clients Contrat Entreprise Azure (Azure EA) utilisent le portail Azure Enterprise.
author: bandersmsft
ms.author: banders
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 5b84cbf4ae7065f13442852cfbf646aa49771e1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78272427"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Bien démarrer avec le portail Azure Enterprise

Cet article aide les clients Contrat Entreprise Azure (Azure EA) directs et indirects à commencer à utiliser le [portail Azure Enterprise](https://ea.azure.com). Obtenir des informations de base sur les thèmes suivants :

- La structure du portail Azure Enterprise.
- Les rôles utilisés dans le portail Azure Enterprise.
- Création d’un abonnement.
- Analyse des coûts dans les portails Azure Enterprise et Azure.

Regardez cette vidéo pour visionner une session d’intégration complète dans le portail Azure Enterprise :

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-enterprise-portal-hierarchy"></a>Hiérarchie du portail Azure Enterprise

La hiérarchie du portail Azure Enterprise se compose des éléments suivants :

- Le **portail Azure Enterprise** : un portail de gestion en ligne qui vous permet de gérer les coût de vos services Azure EA. Vous pouvez :

  - créer une hiérarchie Azure EA avec des services, des comptes et des abonnements ;
  - rapprocher les coûts des services consommés, télécharger des rapports d’utilisation et consulter les tarifs ;
  - créer des clés API pour votre inscription.

- **Les services** vous aident à segmenter les coûts en regroupements logiques. Les services vous permettent de définir un budget ou un quota au niveau du service.

- Les **comptes** sont des unités d’organisation dans le portail Azure Enterprise. Vous pouvez utiliser des comptes pour gérer les abonnements et les rapports d’accès.

- Les **abonnements** sont la plus petite unité du portail Azure Enterprise. Ce sont les conteneurs des services Azure qui sont gérés par l’administrateur de service.

Le diagramme ci-dessous illustre des hiérarchies Azure EA simples.

![Diagramme de hiérarchies Azure EA simples](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Rôles d’utilisateur Entreprise

Les rôles d’utilisateur d’administration suivants font partie de l’inscription de votre entreprise :

- Administrateur d’entreprise
- Administrateur de service
- Propriétaire du compte
- Administrateur de services fédérés
- Contact de notification

Les rôles fonctionnent dans deux portails différents pour effectuer des tâches. Vous utilisez le [portail Azure Enterprise](https://ea.azure.com) pour gérer la facturation et les coûts, et le [portail Azure](https://portal.azure.com) pour gérer les services Azure.

Les rôles utilisateur sont associés à un compte d’utilisateur. Pour valider l’authenticité de l’utilisateur, chaque utilisateur doit avoir un compte professionnel, scolaire ou Microsoft valide. Assurez-vous que chaque compte est associé à une adresse e-mail activement supervisée. Les notifications de compte sont envoyées à l’adresse e-mail.

Quand vous configurez des utilisateurs, vous pouvez attribuer plusieurs comptes au rôle d’administrateur d’entreprise. Toutefois, un seul compte peut contenir le rôle de propriétaire du compte. Vous pouvez également attribuer les rôles d’administrateur d’entreprise et de propriétaire du compte à un seul compte.

### <a name="enterprise-administrator"></a>Administrateur d’entreprise

Les utilisateurs ayant ce rôle ont le niveau d’accès le plus élevé. Ils peuvent effectuer les tâches suivantes :

- Gérer les comptes et les propriétaires de compte.
- Gérer d’autres administrateurs d’entreprise.
- Gérer les administrateurs de service.
- Gérer les contacts de notification.
- Consulter l’utilisation pour tous les comptes.
- Consulter les frais non facturés pour tous les comptes.

Il peut y avoir plusieurs administrateurs d’entreprise dans une inscription Entreprise. Vous pouvez accorder un accès en lecture seule aux administrateurs d’entreprise. Tous les administrateurs d’entreprise héritent le rôle Administrateur de service.

### <a name="department-administrator"></a>Administrateur de service

Les utilisateurs ayant ce rôle peuvent effectuer les tâches suivantes :

- Créer et gérer des services.
- Créer des propriétaires de compte.
- Consulter les détails de l’utilisation des services qu’ils gèrent.
- Consulter les coûts, s’ils ont les autorisations appropriées.

Il peut y avoir plusieurs administrateurs de service dans chaque inscription Entreprise.

Vous pouvez accorder un accès en lecture seule aux administrateurs de service lorsque vous modifiez ou créez un administrateur de service. Définissez l’option de lecture seule sur **Yes** (Oui).

### <a name="account-owner"></a>Propriétaire du compte

Les utilisateurs ayant ce rôle peuvent effectuer les tâches suivantes :

- Créer et gérer des abonnements.
- Gérer les administrateurs de service.
- Consulter l’utilisation des abonnements.

Chaque compte nécessite un compte professionnel, scolaire ou Microsoft unique. Pour plus d’informations sur les rôles d’administrateur dans le portail Azure Enterprise, consultez [Comprendre les rôles d’administrateur Accord Entreprise Azure dans Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Administrateur de services fédérés

Le rôle d’administrateur de service est autorisé à gérer les services dans le portail Azure et à attribuer le rôle de coadministrateur à des utilisateurs.

### <a name="notification-contact"></a>Contact de notification

Le contact de notification reçoit des notifications d’utilisation liées à l’inscription.

## <a name="activate-your-enrollment"></a>Activer votre inscription

Pour activer votre service, l’administrateur d’entreprise initial ouvre le [portail Azure Enterprise](https://ea.azure.com) et se connecte en utilisant l’adresse e-mail indiquée dans son e-mail d’invitation.

Si vous avez été configuré comme administrateur d’entreprise, vous n’êtes pas obligé de recevoir l’e-mail d’activation. Accédez au [portail Azure Enterprise](https://ea.azure.com) et connectez-vous avec l’adresse e-mail de votre compte professionnel, scolaire ou Microsoft et votre mot de passe.

Si vous avez plusieurs inscriptions, choisissez celle à activer. Par défaut, seules les inscriptions actives sont affichées. Pour afficher l’historique des inscriptions, désactivez l’option **Active** en haut à droite du portail Azure Enterprise.

Sous **Inscription**, l’état affiche **Active**.

![Exemple d’inscription active](./media/ea-portal-get-started/ea-enrollment-status.png)

Seuls les administrateurs d’entreprise Azure existants peuvent créer d’autres administrateurs d’entreprise.

### <a name="create-another-enterprise-administrator"></a>Créer un autre administrateur d’entreprise

Ajouter un autre administrateur d’entreprise :

1. Connectez-vous au [portail Azure Enterprise](https://ea.azure.com).
1. Accédez à **Gérer** > **Détail de l’inscription**.
1. Sélectionnez **+ Ajouter un administrateur** en haut à droite.

Assurez-vous que vous avez l’adresse e-mail de l’utilisateur et la méthode d’authentification par défaut, par exemple l’authentification avec un compte professionnel, scolaire ou Microsoft.

Si vous n’êtes pas l’administrateur d’entreprise, contactez un administrateur d’entreprise pour lui demander de vous ajouter à une inscription. Une fois que vous avez été ajouté à une inscription, vous recevez un e-mail d’activation.

Si votre administrateur d’entreprise ne peut pas vous aider, créez une [demande de support pour le portail Azure Enterprise](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Fournissez les informations suivantes :

- Numéro d’inscription
- Adresse e-mail à ajouter et type d’authentification (avec un compte professionnel, scolaire ou Microsoft)
- Approbation reçue par e-mail d’un administrateur d’entreprise existant
  - Si l’administrateur d’entreprise existant n’est pas disponible, contactez votre partenaire ou votre conseiller logiciel pour lui demander de modifier les détails de contact dans l’outil du Centre de gestion des licences en volume (VLSC).

Pour plus d’informations sur les rôles d’administrateur d’entreprise, consultez [Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure](understand-ea-roles.md).

## <a name="create-an-azure-enterprise-department"></a>Créer un service Azure Enterprise

Les administrateurs d’entreprise et les administrateurs de service utilisent les services pour organiser les services Entreprise Azure, et créer des rapports sur l’utilisation par service et par centre de coûts. L’administrateur d’entreprise peut :

- ajouter ou supprimer des services ;
- associer un compte à un service ;
- créer des administrateurs de service ;
- autoriser les administrateurs de service à consulter les tarifs et les coûts.

Un administrateur de service peut ajouter de nouveaux comptes à ses services. Il peut supprimer des comptes de ses services, mais pas de l’inscription.

Pour ajouter un service :

1. Connectez-vous au portail Azure Enterprise.
1. Dans le volet gauche, sélectionnez **Gérer**.
1. Sélectionnez l’onglet **Service**, puis sélectionnez **+ Ajouter un service**.
1. Entrez les informations.
   Le nom du service est le seul champ obligatoire. Il doit comprendre au moins trois caractères.
1. Quand vous avez terminé, sélectionnez **Ajouter**.

## <a name="add-a-department-administrator"></a>Ajouter un administrateur de service

Après avoir créé un service, l’administrateur d’entreprise peut ajouter des administrateurs de service et associer chacun d’eux à un service. Les administrateurs de service peut effectuer les actions suivantes pour leurs services :

- Créer d’autres administrateurs de service
- Consulter et modifier les propriétés d’un service, telles que le nom ou le centre de coûts
- Ajouter des comptes
- Supprimer des comptes
- Télécharger les détails d'utilisation
- Afficher l’utilisation et les frais mensuels <sup>1</sup>

> <sup>1</sup> Un administrateur d’entreprise doit accorder ces autorisations. Si vous avez l’autorisation de consulter les données d’utilisation et les frais mensuels du service, mais que vous ne voyez pas ces informations, contactez votre partenaire.

### <a name="to-add-a-department-administrator"></a>Pour ajouter un administrateur de service

En tant qu’administrateur d’entreprise :

1. Connectez-vous au portail Azure Enterprise.
1. Dans le volet gauche, sélectionnez **Gérer**.
1. Sélectionnez l’onglet **Service**, puis sélectionnez le service.
1. Sélectionnez **+ Ajouter administrateur** et entrez les informations requises.
1. Pour accorder l’accès en lecture seule, définissez l’option **Lecture seule** sur **Oui**, puis sélectionnez **Ajouter**.

![Exemple de boîte de dialogue Ajouter un administrateur de service](./media/ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Pour accorder l’accès en lecture seule

Vous pouvez accorder l’accès en lecture seule aux administrateurs de service.

- Lorsque vous créez un administrateur de service, définissez l’option de lecture seule sur **Oui**.

- Pour changer l’accès d’un administrateur de service existant :
   1. Sélectionnez un service, puis sélectionnez le symbole de crayon à côté de l’**administrateur de service** à modifier.
   1. Définissez l’option lecture seule ouverte sur **Oui**, puis sélectionnez **Enregistrer**.

Les administrateurs d’entreprise obtiennent automatiquement les autorisations d’administrateur de service.

## <a name="add-an-account"></a>Ajouter un compte

La structure des comptes et des abonnements a un impact sur la façon dont ces éléments sont administrés, ainsi que sur leur mode d’affichage dans vos factures et rapports. Les divisions métier, les équipes fonctionnelles et les emplacements géographiques sont des exemples de structures organisationnelles courantes.

Pour ajouter un compte :

1. Dans le portail Azure Enterprise, sélectionnez **Gérer** dans le volet de navigation de gauche.
1. Sélectionnez l’onglet **Compte**. Dans la page **Compte**, sélectionnez **+ Ajouter un compte**.
1. Sélectionnez un service ou laissez-le comme non attribué, puis sélectionnez le type d’authentification souhaité.
1. Tapez un nom convivial pour identifier le compte dans les rapports.
1. Tapez l’adresse **e-mail du propriétaire du compte** à associer au nouveau compte.
1. Confirmez l’adresse e-mail, puis cliquez sur **Ajouter**.

![Exemple illustrant la liste des comptes et l’option d’ajout de compte](./media/ea-portal-get-started/create-ea-add-an-account.png)

Pour ajouter un autre compte, sélectionnez **Ajouter un autre compte**, ou sélectionnez **Ajouter** en bas à droite dans la barre d’outils de gauche.

Pour confirmer la propriété du compte :

1. Connectez-vous au portail Azure Enterprise.
1. Afficher l’état.

   L’état doit passer de **Pending** (En attente) à **Start/End date** (Date de début/fin). La date de début/fin correspond à la date à laquelle l’utilisateur s’est connecté pour la première fois et à la date de fin du contrat.
1. Lorsque le message **Avertissement** s’affiche, le propriétaire du compte doit sélectionner **Continuer** pour activer le compte la première fois qu’il se connecte au portail Azure Enterprise.

## <a name="change-account-owner"></a>Changer le propriétaire du compte

Les administrateurs d’entreprise peuvent utiliser le portail Azure Enterprise pour transférer la propriété du compte des abonnements dans une inscription. L’action déplace tous les abonnements d’un compte d’utilisateur source vers un compte d’utilisateur de destination.

Tenez compte de ces informations importantes lors du transfert de comptes :

- Vous pouvez effectuer les transferts suivants :
  - D’un compte professionnel ou scolaire vers un autre compte professionnel ou scolaire.
  - D’un compte Microsoft vers un compte professionnel ou scolaire.
  - D’un compte Microsoft vers un autre compte Microsoft.

    Le compte cible doit être un compte Azure Commerce valide pour être autorisé comme cible des transferts. Pour les nouveaux comptes, vous êtes invité à créer un compte Azure Commerce quand vous vous connectez au portail Azure Enterprise. Pour les comptes existants, vous devez d’abord créer un abonnement Azure pour que le compte soit éligible.

- Vous pouvez effectuer un transfert d’un compte professionnel ou scolaire vers un compte Microsoft.

- Quand vous effectuez un transfert d’abonnement, Microsoft met à jour le propriétaire du compte.

Découvrez les stratégies suivantes de contrôle d’accès en fonction du rôle (RBAC) :

- Lorsque vous effectuez des transferts d’abonnement entre deux ID d’organisation dans le même locataire, les stratégies RBAC ainsi que les attributions de rôles d’administrateur et de coadministrateur de service existantes sont conservées.
- Les autres transferts d’abonnements entraînent la perte de vos stratégies RBAC et de vos attributions de rôles.
- Les stratégies et les rôles d’administrateur ne sont pas transférés entre les annuaires. Les administrateurs de service sont mis à jour pour refléter le propriétaire du compte de destination.

Avant de changer le propriétaire d’un compte :

1. Dans le portail Azure Enterprise, affichez l’onglet **Compte** et identifiez le compte source. Le compte source doit être actif.
1. Identifiez le compte de destination et assurez-vous qu’il est actif.

Pour transférer la propriété du compte pour tous les abonnements :

1. Connectez-vous au portail Azure Enterprise.
1. Dans la zone de navigation de gauche, sélectionnez **Gérer**.
1. Sélectionnez l’onglet **Compte**, puis pointez sur un compte.
1. Sélectionnez l’icône de changement du propriétaire du compte à droite. L’icône représente une personne.
1. Choisissez un compte éligible, puis cliquez sur **Suivant**.
1. Confirmez le transfert et sélectionnez **Envoyer**.

![Image représentant le symbole de changement du propriétaire du compte](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Pour transférer la propriété du compte pour un seul abonnement :

1. Connectez-vous au portail Azure Enterprise.
1. Dans la zone de navigation de gauche, sélectionnez **Gérer**.
1. Sélectionnez l’onglet **Compte**, puis pointez sur un compte.
1. Sélectionnez l’icône de transfert des abonnements à droite. L’icône représente une page.
1. Choisissez un abonnement éligible, puis sélectionnez **Suivant**.
1. Confirmez le transfert, puis sélectionnez **Envoyer**.

![Image représentant le symbole de transfert des abonnements](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

Regardez cette vidéo pour voir gestion des utilisateurs du portail Azure Enterprise :

> [!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Création d’un abonnement

Les propriétaires de compte peuvent consulter et gérer les abonnements. Vous pouvez utiliser des abonnements pour donner aux équipes de votre organisation l’accès à des projets et environnements de développement. Il peut s’agir d’environnements de test, de production, de développement et de préproduction, par exemple.

En créant des abonnements différents pour chacun des environnements d’application, vous sécurisez davantage chaque environnement.

- Vous pouvez également attribuer un compte d’administrateur de service distinct par abonnement.
- Vous pouvez associer plusieurs services aux abonnements.
- Le propriétaire du compte crée des abonnements et attribue un compte d’administrateur de service à chaque abonnement dans son compte.

### <a name="add-a-subscription"></a>Ajouter un abonnement

Aidez-vous des informations suivantes pour ajouter un abonnement.

La première fois que vous ajoutez un abonnement à votre compte, vous êtes invité à accepter le contrat d’abonnement à Microsoft Online (MOSA) et un plan tarifaire. Bien qu’ils ne s’appliquent pas aux clients ayant souscrit un Contrat Entreprise, le MOSA et le plan tarifaire sont requis pour créer votre abonnement. L’avenant à votre inscription Contrat Entreprise Microsoft Azure se substitue aux éléments cités ci-dessus, et votre lien contractuel ne change pas. Lorsque vous y êtes invité, cochez la case qui indique que vous acceptez les conditions.

_Microsoft Azure Enterprise_ est le nom par défaut lorsqu’un abonnement est créé. Vous pouvez modifier le nom de l’abonnement pour le différencier des autres abonnements dans votre inscription et vous assurer qu’il est reconnaissable dans les rapports au niveau de l’entreprise.

Pour ajouter un abonnement :

1. Dans le portail Azure Enterprise, connectez-vous au compte.
1. Sélectionnez l’onglet **Administrateur**, puis sélectionnez **Abonnement** en haut de la page.
1. Vérifiez que vous êtes connecté en tant que propriétaire du compte.
1. Sélectionnez **+ Ajouter l’abonnement**, puis sélectionnez **Achat**.

   La première fois que vous ajoutez un abonnement à un compte, vous devez fournir vos informations de contact. Quand vous ajoutez des abonnements supplémentaires, vos informations de contact sont remplies automatiquement.

1. Sélectionnez **Abonnements**, puis choisissez l’abonnement créé.
1. Sélectionnez **Modifier les détails de l’abonnement**.
1. Modifiez les champs **Nom de l’abonnement** et **Administrateur de service**, puis cochez la case.

   Le nom de l’abonnement apparaît sur les rapports. C’est le nom du projet associé à l’abonnement dans le portail de développement.

Cela peut prendre jusqu’à 24 heures pour que les nouveaux abonnements soient visibles dans la liste des abonnements. Une fois que vous avez créé un abonnement, vous pouvez :

- [Modifier les détails de l’abonnement](https://account.azure.com/Subscriptions)
- [Gérer les services de l’abonnement](https://portal.azure.com/#home)

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Transformer un abonnement Enterprise en abonnement avec paiement à l’utilisation

Pour transformer un abonnement Enterprise en abonnement individuel avec des tarifs de paiement à l’utilisation, vous devez créer une demande de support dans le portail Azure Enterprise. Pour créer une demande de support, sélectionnez **+ Nouvelle demande de support** dans la zone **Aide et support**.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Associer un compte existant à votre abonnement avec paiement à l’utilisation

Si vous avez déjà un compte Microsoft Azure sur le portail Azure, entrez le compte scolaire, professionnel ou Microsoft pour l’associer à votre inscription Accord Entreprise.

### <a name="associate-an-existing-account"></a>Associer un compte existant

1. Sur le portail Azure Enterprise, sélectionnez **Gérer**.
1. Sélectionnez l’onglet **Compte**.
1. Sélectionnez **+ Ajouter un compte**.
1. Entrez le compte professionnel, scolaire ou Microsoft associé au compte Azure existant.
1. Confirmez le compte associé au compte Azure existant.
1. Spécifiez le nom à utiliser pour identifier ce compte dans les rapports.
1. Sélectionnez **Ajouter**.
1. Pour ajouter un compte supplémentaire, vous pouvez sélectionner à nouveau l’option **+ Ajouter un compte**, ou bien revenir à la page d’accueil en sélectionnant le bouton **Administrateur**.
1. Si vous affichez la page **Compte**, le compte que vous venez d’ajouter s’affiche avec l’état **En attente**.

### <a name="confirm-account-ownership"></a>Confirmer la propriété du compte

1. Connectez-vous au compte de messagerie associé au compte professionnel, scolaire ou Microsoft que vous avez fourni.
1. Ouvrez l’e-mail de notification intitulé _« Invitation à activer votre compte sur le service Microsoft Azure de la part de Microsoft Volume Licensing »_ .
1. Cliquez sur le lien **Connectez-vous à Microsoft Azure Enterprise Portal** dans l’invitation.
1. Sélectionnez **Connexion**.
1. Entrez votre compte professionnel, scolaire ou Microsoft et votre mot de passe pour vous connecter et confirmer la propriété du compte.

### <a name="azure-marketplace"></a>Place de marché Azure

Bien que la plupart des abonnements puissent passer de l’environnement de paiement à l’utilisation à un Accord Entreprise Azure, ce n’est pas le cas des services de la place de marché Azure. Pour obtenir une vue unique de tous les abonnements et facturations, nous vous recommandons d’ajouter les services de la place de marché au portail Azure Enterprise.

1. Connectez-vous au portail Azure Enterprise.
1. Sélectionnez **Gérer** dans le volet de navigation de gauche.
1. Sélectionnez l’onglet **EnrollmentTab**.
1. Consultez la section **Détails de l’inscription**.
1. À droite du champ Place de marché Azure, sélectionnez l’icône de crayon pour l’activer. Sélectionnez **Enregistrer**.

Le propriétaire du compte peut désormais acheter des services de la place de marché Azure qui faisaient précédemment partie de l’abonnement avec paiement à l’utilisation.

Après que les nouveaux abonnements de la place de marché Azure sont activés dans votre inscription Azure EA, annulez les services de la place de marché Azure créés dans l’environnement de paiement à l’utilisation. Cette étape est essentielle afin que vos abonnements de la place de marché Azure ne tombent pas dans un état incorrect lors de l’expiration de votre instrument de paiement à l’utilisation.

### <a name="msdn"></a>MSDN

Les abonnements MSDN sont automatiquement convertis en MSDN Dev/Test, et l’offre Azure EA perd tout crédit monétaire existant.

### <a name="azure-in-open"></a>Azure dans Open

Si vous associez un abonnement Azure dans Open à un Accord Entreprise, vous annulez les crédits Azure dans Open non consommés. Par conséquent, nous vous avons recommandé de consommer tout le crédit d’un abonnement Azure dans Open avant d’ajouter le compte à votre Accord Entreprise.  

### <a name="accounts-with-support-subscriptions"></a>Comptes avec abonnements au support

Si votre Accord Entreprise ne dispose pas d’un abonnement au support et que vous ajoutez un compte existant avec un abonnement au support au portail Azure Enterprise, votre abonnement au support MOSA ne sera pas automatiquement transféré. Vous devrez racheter un abonnement au support dans Azure EA durant la période de grâce, soit à la fin du mois suivant.

## <a name="view-usage-summary-and-download-reports"></a>Consulter le résumé de l’utilisation et télécharger les rapports

Les administrateurs d’entreprise peuvent voir un résumé de leurs données d’utilisation, l’engagement financier consommé et les frais associés à l’utilisation supplémentaire dans le portail Azure Enterprise. Les frais sont présentés au niveau du résumé pour tous les comptes et abonnements.

Pour voir l’utilisation détaillée de comptes spécifiques, téléchargez le rapport de détails de l’utilisation :

1. Connectez-vous au portail Azure Enterprise.
1. Sélectionnez **Rapports**.
1. Sélectionnez l’onglet **Télécharger l’utilisation**.
1. Dans la liste des rapports, sélectionnez **Télécharger** à côté du rapport mensuel qui vous intéresse.

   > [!NOTE]
   > Le rapport des détails de l’utilisation n’inclut pas les taxes applicables.
   >
   > Une latence pouvant atteindre huit heures peut être observée entre le moment où l’utilisation est engagée et celui où elle apparaît dans le rapport.

Pour voir les rapports et graphes du résumé de l’utilisation :

1. Connectez-vous au portail Azure Enterprise.

1. Sélectionnez une durée d’engagement.

   Pour modifier la plage de dates pour le **Résumé de l’utilisation**, vous pouvez basculer de **M** (mensuel) à **C** (personnalisé) en haut à droite de la page, puis entrer des dates de début et de fin personnalisées.

   ![Créer et afficher un résumé de l’utilisation et télécharger les rapports dans un affichage personnalisé](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Pour afficher des détails supplémentaires, vous pouvez sélectionner une période ou un mois sur le graphe.

   - Le graphe montre l’utilisation par mois, en détaillant l’engagement utilisé, le dépassement des frais de service, les frais facturés séparément et les frais de la place de marché Azure.
   - Pour le mois sélectionné, vous pouvez utiliser les champs situés sous le graphe pour filtrer par services, comptes et abonnements.
   - Vous pouvez basculer entre un affichage **Frais par services** et **Frais par hiérarchie**.
   - Affichez les détails du **service Azure**, des **frais facturés séparément** et de la **place de marché Azure** en développant les sections correspondantes.

Regardez cette vidéo pour voir comment afficher l’utilisation :

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Télécharger les rapports CSV

Les administrateurs d’entreprise utilisent la page de téléchargement de rapport mensuel pour télécharger les rapports suivants dans des fichiers au format CSV :

- Solde et frais
- Détails de l’utilisation
- Frais de la place de marché Azure
- Price sheet

Pour télécharger des rapports :

1. Dans le portail Azure Enterprise, sélectionnez **Rapport**.
2. Sélectionnez **Télécharger l’utilisation** en haut de la page.
3. Sélectionnez **Download** (Télécharger) à côté du rapport mensuel qui vous intéresse.

   > [!NOTE]
   > Une latence pouvant atteindre cinq jours peut être observée entre la date effective de l’utilisation et celle où l’utilisation est indiquée dans les rapports.
   >
   > Les utilisateurs qui téléchargent les fichiers CSV dans Excel avec Safari peuvent rencontrer des erreurs de mise en forme. Pour éviter de telles erreurs, ouvrez ces fichiers à l’aide d’un éditeur de texte.

![Exemple de page Download Usage (Télécharger l’utilisation)](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

Regardez cette vidéo pour voir comment télécharger les données d’utilisation :

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Téléchargement des rapports avancés

Vous pouvez utiliser le téléchargement de rapports avancés pour obtenir des rapports sur des plages de dates ou des comptes spécifique. Le fichier de sortie est au format CSV pour prendre en charge les jeux d’enregistrements volumineux.

1. Dans le portail Azure Enterprise, sélectionnez **Téléchargement des rapports avancés**.
1. Sélectionnez une plage de dates appropriée et les comptes appropriés.
1. Sélectionnez **Request Usage Data** (Demander les données d’utilisation).
1. Sélectionnez le bouton **Actualiser** jusqu’à ce que l’état du rapport soit **Télécharger**.
1. Télécharger le rapport.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Télécharger les rapports d’utilisation et les informations de facturation pour une inscription antérieure

Vous pouvez télécharger des rapports d’utilisation et des informations de facturation pour une inscription antérieure après un transfert d’inscription. Les rapports d’historique sont disponibles dans le portail Azure Enterprise et sous la gestion des coûts.

Le portail Azure Enterprise filtre les inscriptions inactives en dehors de l’affichage. Vous devez désactiver la case à cocher **Active** pour afficher les inscriptions transférées inactives.  

![Désactiver la case à cocher active permet à l’utilisateur de voir les inscriptions inactives](./media/ea-portal-get-started/unchecked-active-box.png)

## <a name="azure-ea-term-glossary"></a>Glossaire des termes Azure EA

- **Compte**  : unité d’organisation dans le portail Azure Enterprise. Elle est utilisée pour gérer des abonnements et pour créer des rapports.
- **Propriétaire du compte** : la personne qui gère les abonnements et les administrateurs de services sur Azure. Elles peuvent afficher les données d’utilisation sur ce compte et ses abonnements associés.
- **Abonnement de modification** : abonnement d’un an ou équivalent dans le cadre de la modification de l’inscription.
- **Engagement** : engagement d’un montant monétaire annuel pour les services Azure à un tarif d’engagement réduit pour une utilisation à partir de ce prépaiement.
- **Administrateur de service** : personne identifiée qui gère les services, crée des comptes et des propriétaires de comptes, affiche les détails d’utilisation des services qu’elle gère et peut afficher les coûts quand des autorisations lui sont accordées.
- **Numéro d’inscription** : identificateur unique fourni par Microsoft pour identifier l’inscription spécifique associée à un Accord Entreprise.
- **Administrateur d’entreprise** : personne qui gère les services, les propriétaires de service, les comptes et les propriétaires de comptes sur Azure. Elles ont la possibilité de gérer les administrateurs d’entreprise, ainsi que d’afficher les données d’utilisation, les quantités facturées et les frais non facturés pour tous les comptes et abonnements associés à l’inscription d’entreprise.
- **Contrat Entreprise** : Un contrat de licence Microsoft pour les clients avec des achats centralisés qui souhaitent normaliser leur organisation entière sur la technologie Microsoft et maintenir une infrastructure informatique sur des logiciels Microsoft.
- **Inscription à un contrat Entreprise** : inscription au programme Accord Entreprise fournissant des produits Microsoft en volume à tarif réduit.
- **Compte Microsoft** : service web qui permet aux sites participants d’authentifier un utilisateur avec un ensemble unique d’informations d’identification.
- **Modification d’inscription Microsoft Azure Enterprise (modification de l’inscription)**  : modification signée par une entreprise, qui lui permet d’accéder à Azure dans le cadre de l’inscription de l’entreprise.
- **Portail Azure Enterprise** : portail utilisé par nos clients d’entreprise pour gérer leurs comptes Azure et leurs abonnements associés.
- **Quantité de ressources consommées** : quantité d’un service Azure utilisée durant un mois.
- **Administrateur de services** : personne identifiée qui accède aux abonnements et aux projets de développement sur le portail Azure Enterprise et les gère.
- **Abonnement**: représente un abonnement au portail Azure Enterprise et est un conteneur de services Microsoft Azure gérés par le même administrateur de services.
- **Compte professionnel ou scolaire** : pour les organisations qui ont configuré Active Directory avec une fédération dans le cloud, et dont tous les comptes se trouvent sur un seul locataire.

### <a name="enrollment-statuses"></a>États de l'inscription

- **Pending** : l’administrateur de l’inscription doit se connecter au portail Azure Enterprise. Une fois la connexion établie, l’inscription passe à l’état Active.
- **Actif** : l’inscription est active et les comptes et les abonnements peuvent être créés dans le portail Azure Enterprise. L’inscription reste active jusqu’à la date de fin de l’Accord Entreprise.
- **Durée prolongée indéterminée** : une durée prolongée indéterminée prend place quand la date de fin d’Accord Entreprise est atteinte. Il permet aux clients Azure EA qui ont opté pour le terme étendu de continuer à utiliser des services Azure indéfiniment à la fin de leur Accord Entreprise.

   Avant que l’inscription Azure EA n’atteigne la date de fin de l’Accord Entreprise, l’administrateur de l’inscription doit prendre l’une des décisions suivantes :

  - Renouveler l’inscription en ajoutant un engagement financier.
  - Transférer vers une nouvelle inscription.
  - Migrer vers le programme d’abonnement Microsoft Online (MOSP).
  - Confirmer la désactivation de tous les services associés à l’inscription.
- **Expiré** : le client Azure EA est exclu du terme prolongé et l’inscription à Azure EA a atteint la date de fin de l’Accord Entreprise. L’inscription va expirer et tous les services associés seront désactivés.
- **Transférée** : Les inscriptions où tous les comptes et services associés ont été transférés vers une nouvelle inscription se voit appliquer l’état Transférée.
  >[!NOTE]
  > Les inscriptions ne sont pas automatiquement transférées si un nouveau numéro d’inscription est généré lors du renouvellement. Vous devez inclure votre numéro d’inscription précédent votre demande de renouvellement pour qu’un transfert automatique puisse avoir lieu.

## <a name="get-started-on-azure-ea---faq"></a>FAQ de prise en main d’Azure EA

Cette section fournit des informations sur les questions types posées par les clients au cours du processus d’intégration.  

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>Puis-je associer mon compte Azure existant à l’inscription Azure EA ?

Oui. Tous les abonnements Azure pour lesquels vous êtes propriétaire du compte seront convertis en Accord Entreprise. Les abonnements qui utilisent le crédit mensuel, tels que Visual Studio, AzurePass, MPN et BizSpark, entre autres, sont inclus. Vous perdrez le crédit mensuel lors de la conversion de ce type d’abonnements.

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>J’ai accidentellement associé mon compte Azure existant à l’inscription Azure EA. Par conséquent, j’ai perdu mon crédit mensuel. Puis-je récupérer mon crédit mensuel ?

Si vous vous êtes connecté en tant que propriétaire d’un compte Azure EA avec les mêmes informations d’identification que pour votre abonnement Visual Studio, vous pouvez récupérer votre avantage Azure d’abonnement Visual Studio en effectuant l’une des actions suivantes :

- Supprimez le propriétaire de votre compte du portail Azure Enterprise après avoir supprimé ou déplacé des abonnements Azure associés. Inscrivez-vous ensuite à nouveau aux avantages individuels de Visual Studio Azure.
- Supprimez l’abonné Visual Studio du site d’administration dans VLSC et réaffectez l’abonnement à un compte, cette fois avec des informations d’identification différentes. Inscrivez-vous ensuite à nouveau aux avantages individuels de Visual Studio Azure.

### <a name="what-type-of-subscription-should-i-create"></a>Quel type d’abonnement devrais-je créer ?

Le portail Azure Enterprise offre deux types d’abonnements pour les clients d’entreprise :

- Microsoft Azure Enterprise, qui est idéal pour :
  - Toutes les utilisations de production
  - Meilleurs prix basés sur les dépenses d’infrastructure

  Pour plus d'informations, [contactez l’équipe commerciale Azure](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Enterprise Dev/Test, qui est idéal pour :
  - Toutes les charges de travail de développement/test d'équipe
  - Charges de travail de test/développement individuelles moyennes à fortes
  - Accès aux images MSDN spéciales et aux tarifs de service préférentiels

  Pour plus d’informations, consultez [l’offre Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Est-il possible de transférer la propriété de l’abonnement à un autre compte ?

Oui, vous pouvez transférer la propriété de l’abonnement à un autre compte. Par exemple, si un compte A possède trois abonnements, l’administrateur de l’entreprise peut transférer un abonnement au compte B, l’autre au compte C et l’autre au compte D. Il peut également transférer tous les abonnements au compte E.

Pour transférer des abonnements :

1. Sur le portail Azure Enterprise, sélectionnez **Gérer** > **Compte**.
1. Pointez sur **Compte** tout à droite pour voir les options **Transférer la propriété** (icône représentant une personne) et **Transférer l’abonnement** (icône de liste). Ces options sont visibles uniquement pour les comptes actifs.

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Mon nom d’abonnement est le même que le nom de l’offre. Dois-je remplacer le nom de l’abonnement par un nom significatif pour mon organisation ?

Lorsque vous créez un abonnement, le nom par défaut est fonction du type d’offre que vous choisissez. Nous vous recommandons de remplacer le nom d’abonnement par un nom qui en facilite le suivi.

Pour modifier le nom :

1. Connectez-vous à [https://account.windowsazure.com](https://account.windowsazure.com).
1. Sélectionnez la liste des abonnements.
1. Sélectionnez l’abonnement que vous souhaitez modifier.
1. Sélectionnez l’icône **Gérer l’abonnement**.
1. Modifiez les détails de l’abonnement.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Comment puis-je suivre les coûts engendrés par le centre de coûts ?

Pour suivre les coûts par centre de coût, vous devez définir le centre de coûts à l’un des niveaux suivants :

- department
- Compte
- Abonnement

En fonction de vos besoins, vous pouvez utiliser le même centre de coûts pour suivre l’utilisation et les coûts associés à un centre de coûts particulier.

Par exemple, pour suivre le coût d’un projet spécial dans lequel plusieurs services sont impliqués, vous souhaitez peut-être définir le centre de coûts au niveau de l’abonnement pour suivre l’utilisation et les coûts.

Vous ne pouvez pas définir un centre de coûts au niveau du service. Si vous souhaitez suivre l’utilisation au niveau du service, vous pouvez utiliser la fonctionnalité _Balise_ disponible au niveau du service.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Comment faire suivre l’utilisation et les dépenses de différents services dans mon organisation ?

Vous pouvez créer autant de services que nécessaire dans le cadre de votre inscription Azure EA. Pour suivre correctement l’utilisation, assurez-vous que plusieurs services ne se partagent pas les abonnements.

Après avoir créé des services et des abonnements, vous pouvez voir les données dans le rapport d’utilisation. Ces informations peuvent vous aider à suivre l’utilisation ainsi qu’à gérer les coûts et les dépenses au niveau du service.

Vous pouvez également accéder aux données d’utilisation par le biais de l’API de création de rapports. Pour obtenir des informations détaillées et des exemples de code, consultez [API REST Azure Enterprise](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis).

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Puis-je définir un quota de dépenses et recevoir des alertes lorsque je m’approche de ma limite ?

Vous pouvez définir un quota de dépense au niveau du service. Le système vous informera automatiquement lorsque vos limites de dépense atteindront 50 %, 75 %, 90 % et 100 % du quota que vous avez défini.

Pour définir votre quota de dépenses, sélectionnez un service, puis sélectionnez l’icône de modification. Après avoir modifié les détails de la limite de dépenses, sélectionnez **Enregistrer**.

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>J’ai utilisé des groupes de ressources pour implémenter le RBAC et suivre l’utilisation. Comment puis-je afficher les détails d’utilisation associés ?

Si vous utilisez des _groupes de ressources_ et des _balises_, ces informations sont suivies au niveau du service, et vous pouvez y accéder dans le fichier de téléchargement d’utilisation détaillé (CSV). Consultez le [rapport d’utilisation à télécharger](https://ea.azure.com/report/downloadusage) dans le portail Azure Enterprise.

Vous pouvez également accéder à l’utilisation via l’API. Pour obtenir des informations détaillées et des exemples de code, consultez [API REST Azure Enterprise](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis).

> [!NOTE]
> Vous ne pouvez appliquer des balises qu’à des ressources qui prennent en charge les opérations Azure Resource Manager. Si vous avez créé une machine virtuelle, un réseau virtuel ou un stockage par le biais du modèle de déploiement classique (tel que via le portail Azure classique), vous ne pouvez pas appliquer de balise à cette ressource. Vous devez redéployer ces ressources via Resource Manager pour prendre en charge le balisage. Toutes les autres ressources prennent en charge le balisage.

### <a name="can-i-perform-analyses-using-power-bi"></a>Puis-je effectuer des analyses à l’aide de Power BI ?

Oui. Avec le Pack de contenu Microsoft Azure Enterprise pour Power BI, vous pouvez effectuer les opérations suivantes :

- Importer et analyser rapidement la consommation Azure pour l’inscription de votre entreprise.
- Déterminer le service, le compte ou l’abonnement dont la consommation est la plus élevée.
- Découvrir le service le plus utilisé par votre organisation.
- Suivre les dépenses et les tendances d’utilisation.

Pour utilisez Power BI :

1. rendez-vous sur le site web de Power BI.
1. Connectez-vous avec un compte professionnel ou scolaire valide.

   Le compte professionnel ou scolaire peut être soit identique à celui utilisé pour accéder à l’inscription via le portail Azure Enterprise, soit différent.
1. Dans le tableau de bord des services, choisissez la vignette Microsoft Azure Enterprise et sélectionnez **Connecter**.
1. Sur l’écran **Se connecter à Azure Enterprise**, entrez les informations suivantes :
    - URL d’environnement Azure : [https://ea.azure.com](https://ea.azure.com)
    - Nombre de mois : entre 1 et 36
    - Numéro d’inscription : votre numéro d’inscription
1. Sélectionnez **Suivant**.
1. Entrez la clé API dans la zone **Clé de compte**.

   Vous trouverez la clé API dans le portail Azure Enterprise. Regardez sous l’onglet **Télécharger l’utilisation**, puis sélectionnez **Clé d’accès API**. Copiez la clé, puis collez-la dans la zone **Clé de compte** dans Power BI.

Selon la taille du jeu de données, le chargement des données peut prendre entre 5 et 30 minutes dans Power BI.

La création de rapport de Power BI est disponible pour les clients Azure EA directs, les partenaires et les clients indirects qui sont en mesure d’afficher les informations de facturation.

## <a name="next-steps"></a>Étapes suivantes

- Les administrateurs du portail Azure Enterprise doivent lire la [documentation consacrée à l’administration du portail Azure Enterprise](ea-portal-administration.md) pour découvrir les tâches d’administration courantes.
- Si vous avez besoin d’aide pour résoudre des problèmes rencontrés avec le portail Azure Enterprise, consultez [Résoudre les problèmes d’accès au portail Azure Enterprise](ea-portal-troubleshoot.md).
- Pour obtenir un guide d’intégration d’Azure EA, consultez [Guide d’intégration d’Azure EA (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).
