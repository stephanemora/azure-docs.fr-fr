---
title: Bien démarrer avec le portail Azure EA
description: Cet article explique de quelle façon les clients d’Azure Entreprise (EA) peuvent utiliser le portail Azure EA.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: ace3c251d979a67666d2aaf01dca01e257bed66b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75985729"
---
# <a name="get-started-with-the-azure-ea-portal"></a>Bien démarrer avec le portail Azure EA

Cet article aide les clients directs et indirects d’Azure Entreprise (EA) à se familiariser avec le [portail Azure EA](https://ea.azure.com), en leur apportant des informations de base sur :

- La structure du portail Azure EA.
- Les rôles utilisés dans le portail Azure EA.
- La procédure à suivre pour commencer à créer des abonnements.
- L’analyse des coûts dans le portail Azure EA et le portail Azure.

Voici une vidéo qui montre une session d’intégration complète du portail Azure EA :

[Vidéo de l’intégration du portail Azure EA](https://www.youtube.com/watch?v=OiZ1GdBpo-I)

>[!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-ea-portal-hierarchy"></a>Hiérarchie du portail Azure EA

La hiérarchie du portail Azure EA se compose des éléments suivants :

**Portail Microsoft Azure EA** : le portail Azure EA est un portail d’administration en ligne qui vous permet de gérer les coûts de vos services Azure EA. Dans ce portail, vous créez une hiérarchie Azure EA, qui inclut des services, des comptes et des abonnements. Vous utilisez également ce portail pour rapprocher les coûts des services consommés, télécharger des rapports sur l’utilisation et consulter les grilles tarifaires. Enfin, ce portail vous permet de créer des clés API pour votre inscription.

**Services** : vous créez des services pour faciliter la segmentation des coûts en regroupements logiques, puis vous définissez un budget ou un quota pour chaque service.

**Comptes** : les comptes sont des unités d’organisation dans le portail Azure EA ; ils sont utilisés pour gérer les abonnements. Les comptes servent également à la création de rapports.

**Abonnements** : les abonnements constituent la plus petite unité dans le portail Azure EA. Ce sont les conteneurs des services Azure qui sont gérés par l’administrateur de service.

Le diagramme ci-dessous illustre des hiérarchies Azure EA simples.

![Diagramme de hiérarchies Azure EA simples](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Rôles d’utilisateur Entreprise

Pour l’administration des services Azure dans votre inscription, il existe cinq rôles utilisateur d’administrateur d’entreprise différents :

- Administrateur d’entreprise
- Administrateur de service
- Propriétaire du compte
- Administrateur de services fédérés
- Contact de notification

Les rôles permettent d’effectuer des tâches dans deux portails Microsoft Azure différents. Le portail Azure EA (https://ea.azure.com) est conçu pour vous aider à gérer la facturation et les coûts. Le portail Azure (https://portal.azure.com) s’utilise pour gérer les services Azure.

Les rôles utilisateur sont associés à un compte d’utilisateur. Pour valider l’authenticité de l’utilisateur, chaque utilisateur doit avoir un compte professionnel, scolaire ou Microsoft valide. Assurez-vous que chaque compte est associé à une adresse e-mail activement supervisée. Les notifications de compte sont envoyées à l’adresse e-mail.

Quand vous configurez des utilisateurs, vous pouvez associer plusieurs comptes professionnels, scolaires ou Microsoft au rôle Administrateur d’entreprise. En revanche, vous ne pouvez associer qu’un seul compte professionnel, scolaire ou Microsoft au rôle Propriétaire du compte. Par ailleurs, vous pouvez attribuer à la fois le rôle Administrateur d’entreprise et le rôle Propriétaire du compte à un même compte professionnel, scolaire ou Microsoft.

### <a name="enterprise-administrator"></a>Administrateur d’entreprise

Le rôle Administrateur d’entreprise possède le niveau d’accès le plus élevé. Les utilisateurs ayant ce rôle peuvent :

- Gérer les comptes et les propriétaires de compte
- Gérer d’autres administrateurs d’entreprise
- Gérer les administrateurs de service
- Gérer les contacts de notification
- Consulter l’utilisation pour tous les comptes
- Consulter les frais non facturés pour tous les comptes

Il peut y avoir plusieurs administrateurs d’entreprise dans une inscription Entreprise. Vous pouvez accorder un accès en lecture seule aux administrateurs d’entreprise. Tous les administrateurs d’entreprise héritent le rôle Administrateur de service.

### <a name="department-administrator"></a>Administrateur de service

Les utilisateurs ayant ce rôle peuvent :

- Créer et gérer des services
- Créer des propriétaires de compte
- Consulter les détails de l’utilisation des services qu’ils gèrent
- Consulter les coûts, s’ils ont les autorisations appropriées

Il peut y avoir plusieurs administrateurs de service dans chaque inscription Entreprise.

Vous pouvez accorder l’accès en lecture seule aux administrateurs de service. Pour accorder l’accès en lecture seule, modifiez ou créez un administrateur de service et définissez l’option de lecture seule sur **Yes** (Oui).

### <a name="account-owner"></a>Propriétaire du compte

Les utilisateurs ayant ce rôle peuvent :

- Créer et gérer des abonnements
- Gérer les administrateurs de service
- Consulter l’utilisation pour les abonnements

Chaque compte nécessite un compte professionnel, scolaire ou Microsoft unique. Pour plus d’informations sur les rôles d’administrateur dans le portail Azure EA, consultez [Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Administrateur de services fédérés

L’administrateur de service est autorisé à gérer les services dans le portail Azure et à attribuer le rôle de coadministrateur à des utilisateurs.

### <a name="notification-contact"></a>Contact de notification

Le contact de notification reçoit des notifications d’utilisation liées à l’inscription.

## <a name="activate-your-enrollment"></a>Activer votre inscription

Pour activer votre service, l’administrateur d’entreprise initial ouvre le portail Azure EA sur [https://ea.azure.com](https://ea.azure.com) et se connecte en utilisant l’adresse e-mail indiquée dans son e-mail d’invitation.

Si vous avez été configuré comme administrateur EA, vous n’êtes pas obligé de recevoir l’e-mail d’activation pour vous connecter au portail Azure EA. Vous pouvez passer à [https://ea.azure.com](https://ea.azure.com) et vous connecter avec votre adresse e-mail (professionnelle, scolaire ou Live ID) et un mot de passe.

Si vous avez plusieurs inscriptions, choisissez celle à activer. Par défaut, seules les inscriptions actives sont affichées. Pour voir l’historique des inscriptions, désactivez l’option **Active** en haut à droite du portail Azure EA.

Sous Enrollment (Inscription), l’état affiche **Active**.

![Exemple d’inscription active](./media/ea-portal-get-started/ea-enrollment-status.png)

Seuls les administrateurs d’entreprise Azure existants peuvent créer d’autres administrateurs d’entreprise.

### <a name="create-another-enterprise-admin"></a>Créer un autre administrateur d’entreprise

- Connectez-vous au [portail Azure EA](https://ea.azure.com), accédez à **Manage** > **Enrollment Detail** (Gérer > Détail de l’inscription), puis cliquez sur **+ Add Administrator** (+ Ajouter un administrateur) en haut à droite de la page.

Assurez-vous que vous connaissez les adresses e-mail de l’utilisateur et la méthode d’authentification par défaut (par exemple, authentification avec un compte professionnel, scolaire ou Microsoft). Vous avez besoin de ces informations pour ajouter un utilisateur.

Si vous n’êtes pas l’administrateur EA, contactez un administrateur EA pour lui demander de vous ajouter à une inscription. Une fois que vous avez été ajouté à une inscription, vous recevez un e-mail d’activation.

Si votre administrateur EA ne peut pas vous aider, créez une [demande de support Azure EA](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Fournissez les informations suivantes :

- Numéro d’inscription
- Adresse e-mail à ajouter et type d’authentification (avec un compte professionnel, scolaire ou Microsoft)
- Approbation reçue par e-mail d’un administrateur EA existant
  - Si l’administrateur EA existant n’est pas disponible, contactez votre partenaire ou votre conseiller logiciel pour lui demander de modifier les détails de contact dans l’outil VLSC.

Pour plus d’informations sur les rôles d’administrateur d’entreprise, consultez [Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure](understand-ea-roles.md).

## <a name="create-an-azure-ea-department"></a>Créer un service Azure EA

Les administrateurs d’entreprise et les administrateurs de service utilisent les services pour organiser les services Entreprise Azure, et créer des rapports sur l’utilisation par service et par centre de coûts. L’administrateur d’entreprise peut :

- Ajouter ou supprimer des services
- Associer un compte à un service
- Créer des administrateurs de service
- Autoriser les administrateurs de service à consulter les tarifs et les coûts

Un administrateur de service peut ajouter de nouveaux comptes à ses services. Il peut supprimer des comptes de ses services, mais pas de l’inscription.

Pour ajouter un service :

1. Dans la barre de navigation de gauche, cliquez sur **Manage** (Gérer).
2. Cliquez sur l’onglet **Department** (Service), cliquez sur **+ Add Department** (+ Ajouter un service), puis entrez les informations requises.
3. Le nom du service est le seul champ obligatoire. Il doit comprendre au moins trois caractères.
4. Lorsque vous avez terminé, cliquez sur **Add** (Ajouter).

## <a name="add-a-department-admin"></a>Ajouter un administrateur de service

Après avoir créé un service, l’administrateur d’entreprise Azure peut ajouter des administrateurs de service et associer chacun d’eux à un service. L’administrateur de service peut :

- Créer d’autres administrateurs de service
- Consulter et modifier les propriétés d’un service, comme le nom ou le centre de coûts
- Ajouter un compte pour ses services
- Supprimer des comptes de ses services
- Télécharger les détails d’utilisation de ses services
- Consulter l’utilisation et les frais mensuels de son service si un administrateur d’entreprise lui a accordé l’autorisation appropriée <sup>1</sup>

### <a name="to-add-a-department-admin"></a>Pour ajouter un administrateur de service

En tant qu’administrateur d’entreprise :

1. Dans la barre de navigation de gauche, cliquez sur **Manage** (Gérer).
2. Cliquez sur l’onglet **Department** (Service), puis cliquez sur le service.
3. Cliquez sur **+ Add Administrator** (+ Ajouter un administrateur) et entrez les informations requises.
4. Pour accorder l’accès en lecture seule, définissez l’option **Read-Only** (Lecture seule) sur **Yes** (Oui), puis cliquez sur **Add** (Ajouter).

![Exemple de boîte de dialogue Add Department Administrator (Ajouter un administrateur de service)](./media/ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Pour accorder l’accès en lecture seule

Vous pouvez accorder l’accès en lecture seule aux administrateurs de service. Quand vous créez un administrateur de service :

- Définissez l’option de lecture seule sur **Yes** (Oui).

Pour changer l’accès d’un administrateur de service existant :

1. Sélectionnez un service, puis cliquez sur le symbole de crayon à côté de l’**administrateur de service** à modifier.
2. Définissez l’option de lecture seule sur **Yes** (Oui). Cliquez ensuite sur **Save** (Enregistrer).

Les utilisateurs ayant le rôle d’administrateur d’entreprise obtiennent automatiquement les autorisations d’administrateur de service.

<sup>1</sup> Si vous avez l’autorisation de consulter les données d’utilisation et les frais mensuels du service, mais que vous ne voyez pas ces informations, contactez votre partenaire.

## <a name="add-an-account"></a>Ajouter un compte

La structure des comptes et des abonnements a un impact sur la façon dont ces éléments sont administrés, ainsi que sur leur mode d’affichage dans vos factures et rapports. Les structures selon les divisions métier, les équipes fonctionnelles et les emplacements géographiques sont des exemples d’organisations courantes.

Pour ajouter un compte :

1. Dans le portail Azure EA, cliquez sur **Manage** (Gérer) dans la zone de navigation de gauche.
2. Cliquez sur l’onglet **Account** (Compte) et, dans la page **Account** (Compte), cliquez sur **+ Add Account** (+ Ajouter un compte).
3. Sélectionnez un service ou laissez-le comme non attribué, puis sélectionnez le type d’authentification souhaité.
4. Tapez un nom convivial sous lequel le compte sera identifié dans les rapports.
5. Tapez l’adresse **Account Owner Email** (E-mail du propriétaire du compte) à associer au nouveau compte.
6. Confirmez l’adresse e-mail, puis cliquez sur **Add** (Ajouter).

![Exemple illustrant la liste des comptes et l’option d’ajout de compte](./media/ea-portal-get-started/create-ea-add-an-account.png)

Vous pouvez ajouter un autre compte en cliquant sur **Add Another Account** (Ajouter un autre compte), ou en cliquant sur **Add** (Ajouter) en bas à droite dans la barre d’outils de gauche.

Pour confirmer la propriété du compte :

1. Connectez-vous au portail Azure EA.
1. Confirmez la propriété du compte en vérifiant l’état. L’état doit passer de **Pending** (En attente) à **Start/End date** (Date de début/fin). La date de début/fin correspond à la date à laquelle l’utilisateur s’est connecté pour la première fois et à la date de fin du contrat.
1. Lorsque le message « avertissement » s’affiche, le propriétaire du compte doit cliquer sur **Continuer** pour activer le compte la première fois qu’il se connecte au portail Azure EA.


## <a name="change-account-owner"></a>Changer le propriétaire du compte

Les administrateurs d’entreprise peuvent utiliser le portail Azure EA pour transférer la propriété du compte des abonnements dans une inscription. L’action déplace tous les abonnements d’un compte d’utilisateur source vers un compte d’utilisateur de destination.

Points importants sur le transfert des informations d’un compte d’utilisateur :

- Les transferts d’un compte professionnel ou scolaire vers un autre compte professionnel ou scolaire sont pris en charge.
- Les transferts d’un compte Microsoft vers un compte professionnel ou scolaire sont pris en charge.
- Les transferts d’un compte professionnel ou scolaire vers un compte Microsoft ne sont pas pris en charge.
- Les transferts d’un compte Microsoft vers un autre compte Microsoft sont pris en charge. Le compte cible doit être un compte Azure Commerce valide pour être autorisé comme cible des transferts. Pour les nouveaux comptes, vous êtes invité à créer un compte Azure Commerce quand vous vous connectez au portail Azure EA. Pour les comptes existants, vous devez d’abord créer un abonnement Azure pour que le compte soit éligible.
- Quand vous effectuez un transfert d’abonnement, Microsoft met à jour le propriétaire du compte.

Stratégies de contrôle d’accès en fonction du rôle :

- Seuls les transferts d’abonnement Azure entre deux ID d’organisation dans le même locataire conservent les stratégies de contrôle d’accès en fonction du rôle (RBAC) Azure, les attributions de rôle d’administrateur de service et les attributions de rôle de coadministrateur qui avaient été initialement définies. Pour tous les autres transferts d’abonnement, les stratégies RBAC ainsi que les attributions de rôles d’administrateur et de coadministrateur de service existantes sont perdues. Les stratégies et les rôles d’administrateur ne sont pas transférés entre les annuaires. Les administrateurs de service sont mis à jour pour refléter le propriétaire du compte de destination.
- Lorsque vous effectuez des transferts d’abonnement entre deux ID d’organisation dans le même locataire, les stratégies RBAC ainsi que les attributions de rôles d’administrateur et de coadministrateur de service existantes sont conservées.

Avant de changer le propriétaire d’un compte :

1. Affichez l’onglet **Account** (Compte) et identifiez le compte source. Le compte source doit être actif.
2. Identifiez le compte de destination. Celui-ci doit être actif.

Pour transférer la propriété du compte pour tous les abonnements :

1. Dans la barre de navigation de gauche, cliquez sur **Manage** (Gérer).
2. Cliquez sur l’onglet **Account** (Compte) et pointez sur un compte.
3. Cliquez sur le symbole de changement du propriétaire du compte, à droite. Le symbole illustre une personne.
4. Sélectionnez un compte éligible, puis cliquez sur **Next** (Suivant).
5. Confirmez le transfert et cliquez sur **Submit** (Envoyer).

![Image représentant le symbole de changement du propriétaire du compte](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Pour transférer la propriété du compte pour un seul abonnement :

1. Dans la barre de navigation de gauche, cliquez sur **Manage** (Gérer).
2. Cliquez sur l’onglet **Account** (Compte) et pointez sur un compte.
3. Cliquez sur le symbole de transfert des abonnements, à droite. Le symbole illustre une page.
4. Sélectionnez un abonnement éligible, puis cliquez sur **Next** (Suivant).
5. Confirmez le transfert et cliquez sur **Submit** (Envoyer).

![Image représentant le symbole de transfert des abonnements](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

Voici une vidéo qui montre comment gérer les utilisateurs du portail Azure EA :

[Vidéo sur la gestion des utilisateurs du portail Azure EA](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Création d’un abonnement

Les propriétaires de compte peuvent consulter et gérer les abonnements. Vous pouvez utiliser des abonnements pour donner aux équipes de votre organisation l’accès à des projets et environnements de développement. Il peut s’agir d’environnements de test, de production, de développement et de préproduction, par exemple. En créant des abonnements différents pour chacun des environnements d’application, vous sécurisez davantage chaque environnement. Vous pouvez également attribuer un compte d’administrateur de service distinct par abonnement. Vous pouvez associer plusieurs services aux abonnements. Le propriétaire du compte crée des abonnements et attribue un compte d’administrateur de service à chaque abonnement dans son compte.

### <a name="add-a-subscription"></a>Ajouter un abonnement

Aidez-vous des informations suivantes pour ajouter un abonnement.

La première fois que vous ajoutez un abonnement à votre compte, vous êtes invité à accepter le contrat MOSA et un plan tarifaire. Bien qu’ils ne s’appliquent pas aux clients avec un Contrat Entreprise, ces éléments sont nécessaires pour créer votre abonnement. L’avenant à votre inscription Contrat Entreprise Microsoft Azure se substitue aux éléments cités ci-dessus, et votre lien contractuel ne change pas. Lorsque vous y êtes invité, cochez la case pour indiquer que vous acceptez les termes du contrat.

Chaque nouvel abonnement est créé avec le nom d’abonnement par défaut, _Microsoft Azure Enterprise_ (Entreprise Microsoft Azure). Vous pouvez changer le nom de l’abonnement pour le distinguer des autres abonnements dans votre inscription. Cela garantit aussi l’identification de l’abonnement dans les rapports au niveau de l’entreprise.

Pour ajouter un abonnement :

1. Dans le portail Azure EA, connectez-vous au compte.
2. Cliquez sur l’onglet **Admin** (Administration), puis cliquez sur **Subscription** (Abonnement) en haut de la page.
2. Vérifiez que vous êtes connecté en tant que propriétaire du compte.
3. Cliquez sur **+ Add Subscription** (+ Ajouter un abonnement), puis cliquez sur **Purchase** (Acheter).
  La première fois que vous ajoutez un abonnement à un compte, vous devez fournir vos informations de contact. Par la suite, quand vous ajoutez des abonnements supplémentaires, vos informations de contact sont remplies automatiquement.
4. Cliquez sur **Subscriptions** (Abonnements), sélectionnez l’abonnement que vous avez créé, puis cliquez sur **Edit Subscription Details** (Modifier les détails de l’abonnement).
5. Mettez à jour les champs **Subscription Name** (Nom de l’abonnement) et **Service Administrator** (Administrateur de service), puis cochez la case.
  Le nom de l’abonnement est indiqué dans les rapports ; il correspond au nom du projet qui est associé à l’abonnement dans le portail de développement.

Cela peut prendre jusqu’à 24 heures pour que les nouveaux abonnements soient visibles dans la liste des abonnements. Une fois que vous avez créé un abonnement, vous pouvez :

- [Modifier les détails de l’abonnement](https://account.azure.com/Subscriptions)
- [Gérer les services de l’abonnement](https://portal.azure.com/#home)

## <a name="transfer-ea-subscription-to-pay-as-you-go-subscription"></a>Changer un abonnement EA en abonnement avec paiement à l’utilisation

Pour changer un abonnement EA en un abonnement individuel avec les tarifs du paiement à l’utilisation, vous devez créer une demande de support dans le portail Azure EA. Pour créer une demande de support, cliquez sur **+ Nouvelle demande de support** dans la zone Aide et support.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Associer un compte existant à votre abonnement avec paiement à l’utilisation

Si vous disposez déjà d’un compte de Microsoft Azure sur le Portail Microsoft Azure, entrez le compte Microsoft ou le compte professionnel ou scolaire associé pour l’associer à votre inscription EA.

### <a name="associate-an-existing-account"></a>Associer un compte existant

1. Dans le Enterprise Portal, cliquez sur **Gérer**.
1. Cliquez sur l’onglet **Compte**.
1. Cliquez sur **+ Ajouter un compte**.
1. Entrez le compte Microsoft ou un compte professionnel ou scolaire associé au compte existant.
1. Confirmez le compte Microsoft ou un compte professionnel ou scolaire associé au compte existant.
1. Spécifiez le nom à utiliser pour identifier ce compte dans les rapports.
1. Cliquez sur **Add**.
1. Vous pouvez ajouter un compte supplémentaire en sélectionnant à nouveau l’option **+Ajouter un compte**, ou bien revenir à la page d’accueil en sélectionnant le bouton **Admin**.
1. Si vous cliquez pour afficher la page **Compte**, le compte que vous venez d’ajouter s’affiche avec l’état **En attente**.

### <a name="confirm-account-ownership"></a>Confirmer la propriété du compte

1. Connectez-vous au compte de messagerie associé au compte Microsoft ou un compte professionnel ou scolaire que vous avez fourni.
1. Ouvrez l’e-mail de notification intitulé _« Invitation à activer votre compte sur le service Microsoft Azure de la part de Microsoft Volume Licensing »_ .
1. Cliquez sur le lien **Connectez-vous au Microsoft Azure Enterprise Portal** dans l’invitation.
1. Cliquez sur **Se connecter**.
1. Entrez votre compte Microsoft ou votre compte professionnel ou scolaire et votre mot de passe pour vous connecter et confirmer la propriété du compte.

### <a name="azure-marketplace"></a>Place de marché Azure

Bien que la plupart des abonnements soient convertis de l’environnement de paiement à l’utilisation à Enterprise Azure, ce n’est pas le cas des services de la place de marché Azure. Pour obtenir une vue unique de tous les abonnements et facturations, nous vous recommandons d’ajouter les services de la place de marché Azure à Enterprise Portal :

1. Cliquez sur **Gérer** dans le volet de navigation de gauche.
1. Cliquez sur l’onglet **Inscription**.
1. Consultez la section Détails de l’inscription.
1. À droite du champ Place de marché Azure, cliquez sur l’icône de crayon pour l’activer, puis sur **Enregistrer**.

Le propriétaire du compte peut désormais acheter des abonnements de la place de marché Azure qui appartiennent au paiement à l’utilisation.

Une fois les abonnements de la place de marché Azure activés dans le cadre de votre inscription, annulez les abonnements de la place de marché créés dans l’environnement de paiement à l’utilisation. Cette étape est essentielle afin que les abonnements de la place de marché ne tombent pas dans un état incorrect lors de l’expiration de votre instrument de paiement à l’utilisation.

### <a name="msdn"></a>MSDN

Les abonnements MSDN sont automatiquement convertis en MSDN Dev/test, et l’offre EA perd tout crédit monétaire existant.

### <a name="azure-in-open"></a>Azure dans Open

L’association d’un abonnement Azure dans Open avec un Contrat Entreprise annulera les crédits Azure dans Open non consommés. Pour éviter la perte éventuelle de crédit, nous recommandons aux clients de consommer tous les crédits d’un abonnement Azure dans Open avant d’ajouter le compte à leur Contrat Entreprise.  

### <a name="accounts-with-support-subscriptions"></a>Comptes avec abonnements au support

Lorsque vous ajoutez des comptes existants au Enterprise Portal ayant un abonnement au support (et que vous ne disposez pas déjà d’un abonnement au support EA), notez que l’abonnement au support MOSA n’est pas automatiquement transféré et que le support doit être reacheté dans EA. Une période de grâce pour la couverture du support sera fournie jusqu’à la fin du mois suivant, afin de laisser le temps nécessaire à la réorganisation du support.

## <a name="view-usage-summary-and-download-reports"></a>Consulter le résumé de l’utilisation et télécharger les rapports

Les administrateurs d’entreprise peuvent voir un résumé de leurs données d’utilisation, engagement financier consommé et frais associés à l’utilisation supplémentaire dans le portail Azure EA. Les frais sont présentés au niveau du résumé pour tous les comptes et abonnements.

Pour voir l’utilisation détaillée de comptes spécifiques :

Téléchargez le rapport Usage Detail (Détails de l’utilisation). Cliquez sur **Reports** (Rapports), puis sur l’onglet **Download Usage** (Télécharger l’utilisation). Dans la liste des rapports, cliquez sur **Download** (Télécharger) à côté du rapport mensuel qui vous intéresse.

Le rapport n’inclut pas les taxes applicables. Une latence pouvant atteindre huit heures peut être observée entre le moment où l’utilisation est engagée et celui où elle apparaît dans le rapport.

Pour voir les rapports et graphes du résumé de l’utilisation :

1. Dans le portail Azure EA, dans la zone de navigation de gauche, cliquez sur **Reports** (Rapports) et accédez à l’onglet **Usage Summary** (Résumé de l’utilisation).  
  ![Créer et afficher un résumé de l’utilisation et télécharger les rapports](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports.png)
2. Sélectionnez une durée d’engagement.
3. Basculez entre **M** (Mensuel) et **C** (Personnalisé) en haut à droite de la page pour afficher le **résumé de l’utilisation** avec des dates de début et de fin personnalisées.  
  ![Créer et afficher un résumé de l’utilisation et télécharger les rapports dans un affichage personnalisé](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. Sélectionnez une période ou un mois sur le graphe pour afficher des détails supplémentaires.
5. Le graphe montre l’utilisation par mois, en détaillant l’engagement utilisé, le dépassement des frais de service, les frais facturés séparément et les frais de la Place de marché.
6. Pour le mois sélectionné, filtrez par services, comptes et abonnements sous le graphe.
7. Basculez entre un affichage **Charge by Services** (Frais par services) et **Charge by Hierarchy** (Frais par hiérarchie).
8. Développez et réduisez les options **Azure Service** (Service Azure), **Charges Billed Separately** (Frais facturés séparément) et **Azure Marketplace** (Place de marché Azure) pour afficher les détails.

Voici une vidéo qui montre comment voir les données d’utilisation :

[Vidéo sur le téléchargement des données d’utilisation dans le portail Azure EA](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Télécharger les rapports CSV

Les administrateurs d’entreprise utilisent la page de téléchargement de rapport mensuel pour télécharger plusieurs rapports dans des fichiers au format CSV. Ils comprennent :

- Balance and Charge (Solde et frais)
- Usage Detail (Détails de l’utilisation)
- Marketplace Charges
- Price Sheet

Pour télécharger des rapports :


1. Dans le portail Azure EA, cliquez sur **Reports** (Rapports).
2. Cliquez sur **Download Usage** (Télécharger l’utilisation) en haut de la page.
3. Sélectionnez **Download** (Télécharger) à côté du rapport mensuel qui vous intéresse.

Une latence pouvant atteindre cinq jours peut être observée entre la date effective de l’utilisation et celle où l’utilisation est indiquée dans les rapports.

Les utilisateurs qui téléchargent les fichiers CSV dans Excel avec Safari peuvent rencontrer des erreurs de mise en forme. Pour éviter de telles erreurs, ouvrez ces fichiers à l’aide d’un éditeur de texte.

![Exemple de page Download Usage (Télécharger l’utilisation)](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

Voici une vidéo qui montre comment télécharger les données d’utilisation :

[Vidéo sur le téléchargement des données d’utilisation dans le portail Azure EA](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Téléchargement des rapports avancés

Pour la création de rapports sur des plages de dates ou des comptes spécifiques, le téléchargement de rapports avancés peut être utilisé. Depuis le 30 août 2016, le format du fichier de sortie passe de .xlsx à .csv afin de prendre en charge les jeux d’enregistrements plus volumineux.

1. Sélectionnez **Téléchargement des rapports avancés**.
1. Sélectionnez **Appropriate Date Range** (Plage de dates appropriée).
1. Sélectionnez **Appropriate Accounts** (Comptes appropriés).
1. Sélectionnez **Request Usage Data** (Demander les données d’utilisation).
1. Sélectionnez le bouton **Actualiser** jusqu’à ce que l’état du rapport soit **Télécharger**.
1. Téléchargez le rapport.

## <a name="ea-term-glossary"></a>Glossaire des termes EA

- **Compte**  : Une unité d’organisation sur le portail Azure EA utilisée pour gérer les abonnements et utilisée pour la création de rapports.
- **Propriétaire du compte** : Personne identifiée pour gérer les abonnements et les administrateurs de service sur Microsoft Azure. Elles peuvent afficher les données d’utilisation sur ce compte et ses abonnements associés.
- **Abonnement de modification** : Un abonnement d’un an ou équivalent dans le cadre de la modification de l’inscription.
- **Engagement** : Engagement d’un montant monétaire annuel pour les services Microsoft Azure à un tarif d’engagement réduit pour une utilisation à partir de ce prépaiement.
- **Administrateur de service** : Personne(s) identifiée(s) pour gérer les services, créer des comptes et des propriétaires de comptes, afficher les détails d’utilisation des services qu’ils gèrent et afficher les coûts quand des autorisations leur sont accordées.
- **Numéro d’inscription** : Identificateur unique fourni par Microsoft pour identifier l’inscription spécifique associée à un contrat Entreprise.
- **Administrateur d’entreprise** : Personne(s) identifiée(s) pour gérer les services et les propriétaires de service et les comptes et propriétaires de compte sur Microsoft Azure. Elles ont la possibilité de gérer les administrateurs d’entreprise, ainsi que d’afficher les données d’utilisation, les quantités facturées et les frais non facturés pour tous les comptes et abonnements associés à l’inscription d’entreprise.
- **Contrat Entreprise** : Un contrat de licence Microsoft pour les clients avec des achats centralisés qui souhaitent normaliser leur organisation entière sur la technologie Microsoft et maintenir une infrastructure informatique sur des logiciels Microsoft.
- **Inscription à un contrat Entreprise** : Inscription dans le programme contrat Entreprise fournissant des produits Microsoft en volume à tarif réduit.
- **Compte Microsoft** : Un service Web qui permet aux sites participants d’authentifier un utilisateur avec un ensemble unique d’informations d’identification.
- **Modification d’inscription Microsoft Azure Enterprise (modification de l’inscription)**  : Une modification signée par une entreprise, qui lui permet d’accéder à Microsoft Azure dans le cadre de l’inscription de l’entreprise.
- **Portail Azure EA** : Portail utilisé par nos clients d’entreprise pour gérer leurs comptes Microsoft Azure et leurs abonnements associés.
- **Quantité de ressources consommées** : Quantité d’un service Microsoft Azure utilisée durant un mois.
- **Administrateur de services** : La personne identifiée pour accéder aux abonnements et projets de développement sur le portail Azure EA et les gérer.
- **Abonnement**: Représente un abonnement au portail Azure EA et est un conteneur de services Microsoft Azure gérés par le même administrateur de services.
- **Compte professionnel ou scolaire** : Pour les organisations qui ont configuré Active Directory avec une fédération dans le cloud, et dont tous les comptes se trouvent sur un seul locataire.

### <a name="enrollment-statuses"></a>États de l'inscription :

- **Pending** : l’administrateur de l’inscription doit se connecter au portail Azure EA. Une fois connecté, l’inscription passe à l’état Active.
- **Actif** : L’inscription est active et les comptes et les abonnements peuvent être créés dans le portail Azure EA. L’inscription reste active jusqu’à la date de fin du contrat Entreprise.
- **Durée prolongée indéterminée** : Une durée prolongée indéterminée prend place quand la date de fin du contrat Entreprise est atteinte. Il permet aux clients EA qui ont opté pour le terme étendu de continuer à utiliser Azure indéfiniment à la fin de leur contrat Entreprise. Avant que l’inscription EA ait atteint la date de fin de contrat Entreprise, l’administrateur de l’inscription doit décider entre le renouvellement de l’inscription en ajoutant un engagement monétaire supplémentaire, le transfert vers une nouvelle inscription, la migration vers Microsoft Online Subscription Program (MOSP), ou la confirmation de la désactivation de tous les services associés à l’inscription.
- **Expiré** : Le client EA a refusé le terme prolongé, l’inscription de contrat entreprise a atteint la date de fin de contrat Entreprise, l’inscription expire et tous les services associés sont désactivés.
- **Transférée** : Les inscriptions où tous les comptes et services associés ont été transférés vers une nouvelle inscription se voit appliquer l’état Transférée. Veuillez noter que les inscriptions ne sont pas automatiquement transférées si un nouveau numéro d’inscription est généré lors du renouvellement. Le numéro d’inscription précédent doit être inclus dans la demande de renouvellement du client pour qu’un transfert automatique puisse avoir lieu.

## <a name="get-started-on-azure-ea-faq"></a>FAQ de prise en main d’Azure EA

Ce document fournit des informations sur les questions types posées par les clients au cours du processus d’intégration.  

### <a name="can-i-associate-my-existing-azure-account-to-enterprise-enrollment"></a>Puis-je associer mon compte Azure existant à l’inscription Entreprise ?

Oui, vous pouvez. Un point important, tous les abonnements Azure pour lesquels vous êtes propriétaire du compte seront convertis en contrat Entreprise. Cela comprend les abonnements qui utilisent le crédit mensuel (par exemple, Visual Studio, AzurePass, MPN, BizSpark, etc.), ce qui signifie que vous perdez alors le crédit mensuel.

### <a name="i-accidentally-associated-my-existing-azure-account-with-enterprise-enrollment-as-a-result-i-lost-my-monthly-credit-is-it-possible-to-get-my-monthly-credit-back"></a>J’ai accidentellement associé mon compte Azure existant à l’inscription Entreprise. Par conséquent, j’ai perdu mon crédit mensuel. Est-il possible d’accéder à mon crédit mensuel ?

Pour récupérer votre offre d’abonnement Visual Studio individuel après vous être authentifié en tant que propriétaire d’un compte EA, après avoir utilisé la même connexion pour EA que votre abonnement Visual Studio, vous devez :
1. Supprimer ce propriétaire de compte du portail EA, après avoir supprimé ou déplacé les abonnements Azure dont il est propriétaire, et faire qu’ils soient de nouveau inscrits pour leurs avantages individuels de Visual Studio Azure.
 OR
1. Supprimer l’abonné Visual Studio du site d’administration dans VLSC et réaffectez l’abonnement, en faisant en sorte qu’il utilise une connexion différente cette fois-ci. Ils peuvent ensuite s’inscrire de nouveau pour leurs avantages individuels de Visual Studio Azure.

### <a name="what-type-of-subscription-should-i-create"></a>Quel type d’abonnement devrais-je créer ?

Le portail EA offre deux types d’abonnements pour les clients d’entreprise :

- Microsoft Azure Enterprise, idéal pour :
  - Toutes les utilisations de production
  - Meilleurs prix basés sur les dépenses d’infrastructure
  - Vous trouverez plus d’informations à l’adresse https://azure.microsoft.com/pricing/enterprise-agreement/
- Enterprise Dev/Test - idéal pour :
  - Toutes les charges de travail de développement/test d'équipe
  - Charges de travail de test/développement individuelles moyennes à fortes
  - Accès aux images MSDN spéciales et aux tarifs de service préférentiels
  - Vous trouverez plus d’informations à l’adresse https://azure.microsoft.com/offers/ms-azr-0148p/

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Est-il possible de transférer la propriété de l’abonnement à un autre compte ?

Oui, il est possible de transférer la propriété de l’abonnement à un autre compte. Par exemple, si un compte A possède trois abonnements, l’administrateur de l’entreprise peut transférer un abonnement au compte B, l’autre au compte C et l’autre au compte D ou bien tous les transférer au compte E.

Vous pouvez accéder à EA et cliquer sur Gérer > Compte, pointer sur **Compte** (à l’extrême droite) et vous verrez l’option Transférer la propriété (icône portrait) et transférer l’abonnement (icône de liste).

Cette option est visible uniquement pour les comptes actifs.

### <a name="i-see-subscription-name-defaults-to-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Je vois le nom par défaut de l’abonnement, dois-je le remplacer par un nom significatif pour mon organisation ?

Le type d’offre que vous choisissez est défini par défaut pour tous les abonnements créés. Nous vous recommandons de remplacer le nom d’abonnement par un nom qui en facilite le suivi.

**Pour changer le nom :**
1. Connectez-vous à [https://account.windowsazure.com](https://account.windowsazure.com).
1. Cliquez sur la liste Abonnement.
1. Sélectionnez l’abonnement.
1. Cliquez sur l’icône **Gérer l’abonnement**.
1. Modifiez les détails de l’abonnement.

### <a name="how-can-i-track-cost-incurred-by-cost-center"></a>Comment puis-je suivre les coûts engendrés par le centre de coût ?

Pour suivre les coûts par centre de coût, vous devez définir le centre de coûts à l’un des niveaux suivants :
- department
- Compte
- Subscription

En fonction de vos besoins, vous pouvez utiliser le même centre de coûts pour suivre l’utilisation et les coûts associés à un centre de coûts particulier.

Par exemple, pour suivre le coût d’un projet spécial dans lequel plusieurs services sont impliqués, vous pouvez utiliser le centre de coût au niveau de l’abonnement pour suivre l’utilisation et le coût.

Vous ne pouvez pas définir le centre de coût au niveau du service et, au cas où vous souhaiteriez suivre l’utilisation au niveau du service, vous pouvez utiliser la fonctionnalité « balise » disponible au niveau du service.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Comment faire suivre l’utilisation et les dépenses de différents services dans mon organisation ?

Vous pouvez créer autant de services que nécessaire dans le cadre de votre inscription EA. Pour suivre correctement l’utilisation, vous devez vous assurer que les abonnements ne sont pas partagés entre les services.

Une fois la création du service et de l’abonnement terminée, vous pouvez voir les informations qui circulent dans le rapport d’utilisation qui vous aideront à suivre l’utilisation et à gérer les coûts et les dépenses au niveau du service.

Vous pouvez également accéder à l’utilisation via des informations détaillées sur l’API et des exemples de code sont disponibles sur [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

### <a name="can-i-set-the-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Puis-je définir le quota de dépense et recevoir des alertes lorsque j’approche ma limite ?

Vous pouvez définir un quota de dépense au niveau du service et le système vous informera automatiquement à mesure que vos limites de dépense atteignent 50 %, 75 %, 90 % et 100 % du quota que vous avez défini.

Pour définir votre quota de dépense, cliquez sur le service auquel vous souhaitez ajouter une limite de dépense, puis cliquez sur l’icône Modifier. Cliquez sur **Enregistrer** pour enregistrer les détails.

### <a name="i-used-resource-groups-rgs-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>J’ai utilisé des groupes de ressources pour implémenter RBAC et suivre l’utilisation, comment puis-je afficher les détails d’utilisation associés ?

Les informations telles que « Groupes de ressources » et « Balises », si elles sont utilisées, sont suivies au niveau du service et les informations sont disponibles dans le fichier des détails de l’utilisation (format CSV), à télécharger depuis le portail Azure EA [https://ea.azure.com/report/downloadusage](https://ea.azure.com/report/downloadusage).

Vous pouvez également accéder à l’utilisation via des informations détaillées sur l’API et des exemples de code sont disponibles sur [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI).

Veuillez noter que vous ne pouvez appliquer des balises qu’à des ressources qui prennent en charge les opérations de Resource Manager. Si vous avez créé une machine virtuelle, un réseau virtuel ou un stockage par le biais du modèle de déploiement classique (tel que via le portail Azure classique), vous ne pouvez pas appliquer de balise à cette ressource. Vous devez redéployer ces ressources via Resource Manager pour prendre en charge le balisage. Toutes les autres ressources prennent en charge le balisage.

### <a name="can-i-perform-analyses-using-power-bi"></a>Puis-je effectuer des analyses à l’aide de Power BI ?

Oui. Avec le pack de contenu Microsoft Azure Enterprise pour Power BI, vous pouvez importer et analyser rapidement la consommation Azure de l'inscription de votre entreprise. Découvrez quel service, compte ou abonnement a consommé le plus d'utilisation, quel service votre organisation a le plus utilisé, ou faites le suivi des dépenses et des tendances d'utilisation.

**Accédez au site Web Power BI :**

 1. Connectez-vous avec un compte professionnel ou scolaire valide.
    - Le compte professionnel ou scolaire peut être identique ou différent de celui utilisé pour accéder à l’inscription via le portail Azure EA.
 1. Dans le tableau de bord des services, choisissez :
    - Vignette Microsoft Azure Enterprise.
    - Cliquez sur **Connecter**.
 1. Sur l’écran « Se connecter à Azure Enterprise », choisissez :
    - URL d’environnement Azure : [https://ea.azure.com](https://ea.azure.com).
    - Nombre de mois : choisissez entre 1 et 36.
    - Numéro d’inscription : entrez le numéro d’inscription.
    - Cliquez sur **Suivant**.
 1. Entrez la clé API dans la zone de la clé d’authentification. Vous pouvez accéder à la clé API dans le portail Azure EA sous l’onglet « Télécharger l’utilisation », puis cliquez sur **Clé d’accès API**.
    - Copiez et collez la clé dans la zone « Clé de compte ».
    - Le chargement des données dure entre 5 et 30 minutes environ dans Power BI, selon la taille du jeu de données.

La création de rapport de Power BI est disponible pour les clients EA directs, les partenaires et les clients indirects qui sont en mesure d’afficher les informations de facturation.

## <a name="next-steps"></a>Étapes suivantes

- Les administrateurs du portail Azure EA doivent lire la [documentation consacrée à l’administration du portail Azure EA](ea-portal-administration.md) pour découvrir les tâches d’administration courantes.
- Si vous avez besoin d’aide pour résoudre des problèmes rencontrés avec le portail Azure EA, consultez [Résoudre les problèmes d’accès au portail Azure EA](ea-portal-troubleshoot.md).
- Pour obtenir un guide d’intégration d’Azure EA, consultez [Guide d’intégration d’Azure EA](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide).
