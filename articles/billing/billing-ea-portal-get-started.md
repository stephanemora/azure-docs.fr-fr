---
title: Bien démarrer avec le portail Azure EA
description: Cet article explique de quelle façon les clients d’Azure Entreprise (EA) peuvent utiliser le portail Azure EA.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 1882b283f376a1bb8706132263c83e1a24ec0705
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900928"
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

![Diagramme de hiérarchies Azure EA simples](./media/billing-ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Rôles utilisateur Entreprise

Pour l’administration des services Azure dans votre inscription, il existe quatre rôles utilisateur d’administrateur d’entreprise différents :

- Administrateur d’entreprise
- Administrateur de service
- Propriétaire du compte
- Administrateur de services fédérés

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

Chaque compte nécessite un compte professionnel, scolaire ou Microsoft unique. Pour plus d’informations sur les rôles d’administrateur dans le portail Azure EA, consultez [Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure](billing-understand-ea-roles.md).

### <a name="service-administrator"></a>Administrateur de services fédérés

L’administrateur de service est autorisé à gérer les services dans le portail Azure et à attribuer le rôle de coadministrateur à des utilisateurs.

## <a name="activate-your-enrollment"></a>Activer votre inscription

Pour activer votre service, l’administrateur d’entreprise initial ouvre le portail Azure EA sur [https://ea.azure.com](https://ea.azure.com) et se connecte en utilisant l’adresse e-mail indiquée dans son e-mail d’invitation.

Si vous avez plusieurs inscriptions, choisissez celle à activer. Par défaut, seules les inscriptions actives sont affichées. Pour voir l’historique des inscriptions, désactivez l’option **Active** en haut à droite du portail Azure EA.

Sous Enrollment (Inscription), l’état affiche **Active**.

![Exemple d’inscription active](./media/billing-ea-portal-get-started/ea-enrollment-status.png)

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

Pour plus d’informations sur les rôles d’administrateur d’entreprise, consultez [Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure](billing-understand-ea-roles.md).

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

![Exemple de boîte de dialogue Add Department Administrator (Ajouter un administrateur de service)](./media/billing-ea-portal-get-started/ea-create-add-department-admin.png)

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

![Exemple illustrant la liste des comptes et l’option d’ajout de compte](./media/billing-ea-portal-get-started/create-ea-add-an-account.png)

Vous pouvez ajouter un autre compte en cliquant sur **Add Another Account** (Ajouter un autre compte), ou en cliquant sur **Add** (Ajouter) en bas à droite dans la barre d’outils de gauche.

Pour confirmer la propriété du compte :

1. Connectez-vous au portail Azure EA.
2. Confirmez la propriété du compte en vérifiant l’état. L’état doit passer de **Pending** (En attente) à **Start/End date** (Date de début/fin). La date de début/fin correspond à la date à laquelle l’utilisateur s’est connecté pour la première fois et à la date de fin du contrat.


## <a name="change-account-owner"></a>Changer le propriétaire du compte

Les administrateurs d’entreprise peuvent utiliser le portail Azure EA pour transférer la propriété du compte des abonnements dans une inscription. L’action déplace tous les abonnements d’un compte d’utilisateur source vers un compte d’utilisateur de destination.

Points importants sur le transfert des informations d’un compte d’utilisateur :

- Les transferts d’un compte professionnel ou scolaire vers un autre compte professionnel ou scolaire sont pris en charge.
- Les transferts d’un compte Microsoft vers un compte professionnel ou scolaire sont pris en charge.
- Les transferts d’un compte professionnel ou scolaire vers un compte Microsoft ne sont pas pris en charge.
- Les transferts d’un compte Microsoft vers un autre compte Microsoft sont pris en charge. Le compte cible doit être un compte Azure Commerce valide pour être autorisé comme cible des transferts. Pour les nouveaux comptes, vous êtes invité à créer un compte Azure Commerce quand vous vous connectez au portail Azure EA. Pour les comptes existants, vous devez d’abord créer un abonnement Azure pour que le compte soit éligible.
- Quand vous effectuez un transfert d’abonnement, Microsoft met à jour le propriétaire du compte.

Stratégies RBAC :

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

![Image représentant le symbole de changement du propriétaire du compte](./media/billing-ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

Pour transférer la propriété du compte pour un seul abonnement :

1. Dans la barre de navigation de gauche, cliquez sur **Manage** (Gérer).
2. Cliquez sur l’onglet **Account** (Compte) et pointez sur un compte.
3. Cliquez sur le symbole de transfert des abonnements, à droite. Le symbole illustre une page.
4. Sélectionnez un abonnement éligible, puis cliquez sur **Next** (Suivant).
5. Confirmez le transfert et cliquez sur **Submit** (Envoyer).

![Image représentant le symbole de transfert des abonnements](./media/billing-ea-portal-get-started/ea-transfer-subscriptions.png)

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

## <a name="transfer-pay-as-you-go-subscription-to-ea-subscription"></a>Changer un abonnement avec paiement à l’utilisation en abonnement Entreprise Azure (EA)

Pour changer un abonnement individuel avec les tarifs du paiement à l’utilisation en abonnement Entreprise Azure (EA), vous devez créer une demande de support dans le portail Azure. Pour créer une demande de support, cliquez sur **+ Nouvelle demande de support** dans la zone Aide et support.


## <a name="view-usage-summary-and-download-reports"></a>Consulter le résumé de l’utilisation et télécharger les rapports

Les administrateurs d’entreprise peuvent voir un résumé de leurs données d’utilisation, engagement financier consommé et frais associés à l’utilisation supplémentaire dans le portail Azure EA. Les frais sont présentés au niveau du résumé pour tous les comptes et abonnements.

Pour voir l’utilisation détaillée de comptes spécifiques

Téléchargez le rapport Usage Detail (Détails de l’utilisation). Cliquez sur **Reports** (Rapports), puis sur l’onglet **Download Usage** (Télécharger l’utilisation). Dans la liste des rapports, cliquez sur **Download** (Télécharger) à côté du rapport mensuel qui vous intéresse.

Le rapport n’inclut pas les taxes applicables. Une latence pouvant atteindre huit heures peut être observée entre le moment où l’utilisation est engagée et celui où elle apparaît dans le rapport.

Pour voir les rapports et graphes du résumé de l’utilisation :

1. Dans le portail Azure EA, dans la zone de navigation de gauche, cliquez sur **Reports** (Rapports) et accédez à l’onglet **Usage Summary** (Résumé de l’utilisation).  
  ![](./media/billing-ea-portal-get-started/create-ea-view usage-summary-and-download-reports.png)
2. Sélectionnez une durée d’engagement.
3. Basculez entre **M** (Mensuel) et **C** (Personnalisé) en haut à droite de la page pour afficher le **résumé de l’utilisation** avec des dates de début et de fin personnalisées.  
  ![](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. Sélectionnez une période ou un mois sur le graphe pour afficher des détails supplémentaires.
5. Le graphe montre l’utilisation par mois, en détaillant l’engagement utilisé, le dépassement des frais de service, les frais facturés séparément et les frais de la Place de marché.
6. Pour le mois sélectionné, filtrez par services, comptes et abonnements sous le graphe.
7. Basculez entre un affichage **Charge by Services** (Frais par services) et **Charge by Hierarchy** (Frais par hiérarchie).
8. Développez et réduisez les options **Azure Service** (Service Azure), **Charges Billed Separately** (Frais facturés séparément) et **Azure Marketplace** (Place de marché Azure) pour afficher les détails.

Voici une vidéo qui montre comment voir les données d’utilisation :

[Vidéo sur l’affichage des données d’utilisation dans le portail Azure EA](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Télécharger les rapports CSV

Les administrateurs d’entreprise utilisent la page de téléchargement de rapport mensuel pour télécharger plusieurs rapports dans des fichiers au format CSV. À savoir :

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

![Exemple de page Download Usage (Télécharger l’utilisation)](./media/billing-ea-portal-get-started/create-ea-download-csv-reports.png)

Voici une vidéo qui montre comment télécharger les données d’utilisation :

[Vidéo sur le téléchargement des données d’utilisation dans le portail Azure EA](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

## <a name="schedule-an-onboarding-call"></a>Planifier un appel d’intégration

Si vous souhaitez planifier une session d’intégration pour un client, créez une demande de support dans le [portail Azure EA](https://support.microsoft.com/supportrequestform/e114582c-4e51-af46-10b1-1f0cc141e133). Sélectionnez **Onboarding** (Intégration) dans **Issue Category** (Catégorie de problème).

## <a name="next-steps"></a>Étapes suivantes
- Les administrateurs du portail Azure EA doivent lire la [documentation consacrée à l’administration du portail Azure EA](billing-ea-portal-administration.md) pour découvrir les tâches d’administration courantes.
- Si vous avez besoin d’aide pour résoudre des problèmes rencontrés avec le portail Azure EA, consultez [Résoudre les problèmes d’accès au portail Azure EA](billing-ea-portal-troubleshoot.md).
