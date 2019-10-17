---
title: Attribution automatique des utilisateurs dans les applications SaaS dans Azure AD | Microsoft Docs
description: Cette introduction explique comment utiliser Azure AD pour approvisionner, annuler l’approvisionnement et mettre à jour de façon continue des comptes d’utilisateurs dans diverses applications SaaS tierces.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ef652b05f62218ee1d0e72543bfa546f0c14abe
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001698"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory

Azure Active Directory (Azure AD) vous permet d’automatiser la création, la maintenance et la suppression d’identités utilisateur dans les applications cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) comme Dropbox, Salesforce, ServiceNow et bien plus encore. Ce processus est appelé provisionnement automatique des utilisateurs pour les applications SaaS.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

Cette fonctionnalité vous permet de :

- Créez automatiquement des comptes dans les systèmes adéquats pour les nouvelles personnes rejoignant votre équipe ou votre organisation.
- Désactivez automatiquement les comptes dans les systèmes adéquats lorsque des personnes quittent votre équipe ou votre organisation.
- Vérifiez que les identités dans vos applications et systèmes sont tenues à jour selon les modifications apportées au répertoire ou à votre système de gestion des ressources humaines.
- Procédez à l’approvisionnement d’objets non utilisateur, tels que des groupes, pour les applications qui les prennent en charge.

Le provisionnement automatique des utilisateurs inclut également cette fonctionnalité :

- Possibilité de faire correspondre des identités existantes entre les systèmes source et cible.
- Des mappages d’attributs personnalisables qui définissent les données utilisateurs qui doivent circuler entre le système source et le système cible
- Alertes par courrier électronique pour les erreurs d’approvisionnement
- Création de rapports et journaux d’activité pour faciliter la supervision et la résolution des problèmes

## <a name="why-use-automated-provisioning"></a>Pourquoi utiliser l’approvisionnement automatique ?

Voici les principales raisons pour utiliser cette fonctionnalité :

- Réduction des coûts, des inefficacités et des erreurs humaines associés aux processus d’approvisionnement manuel
- Élimination des coûts liés à l'hébergement et à la gestion de scripts et de solutions d'approvisionnement personnalisés
- Sécurisation de votre organisation en supprimant instantanément les identités des utilisateurs des applications SaaS lorsqu’ils quittent l’organisation.
- Importation facile de nombreux utilisateurs dans un système ou une application SaaS spécifique.
- Disposer d’un ensemble unique de stratégies pour déterminer qui est approvisionné et qui peut se connecter à une application.

## <a name="how-does-automatic-provisioning-work"></a>Comment fonctionne l’approvisionnement automatique ?

Le **service d’approvisionnement Azure AD** approvisionne des utilisateurs pour les applications SaaS et les autres systèmes en se connectant aux points de terminaison d’API de gestion des utilisateurs fournis par chaque fournisseur d’application. Ces points de terminaison d’API de gestion des utilisateurs permettent à Azure AD de créer, de mettre à jour et de supprimer des utilisateurs par programmation. Pour les applications sélectionnées, le service d’approvisionnement peut également créer, mettre à jour et supprimer d’autres objets liés à l’identité, tels que les groupes et les rôles.

![Service de provisionnement Azure AD](./media/user-provisioning/provisioning0.PNG)
*Figure 1 : Le service d’approvisionnement Azure AD*

![Workflow de provisionnement des utilisateurs sortant](./media/user-provisioning/provisioning1.PNG)
*Figure 2 : Workflow d’approvisionnement d’utilisateurs « sortant » entre Azure AD et des applications SaaS populaires*

![Workflow de provisionnement des utilisateurs entrant](./media/user-provisioning/provisioning2.PNG)
*Figure 3 : Workflow d’approvisionnement d’utilisateurs « entrant » entre des applications de gestion du capital humain populaires, et Azure Active Directory et Windows Server Active Directory*

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quels applications et systèmes puis-je utiliser avec l’approvisionnement d’utilisateurs automatique d’Azure AD ?

Azure AD offre une prise en charge préintégrée de plusieurs applications SaaS et systèmes de gestion des ressources humaines connus, ainsi qu’une prise en charge générique des applications qui implémentent des parties spécifiques du [standard SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

### <a name="pre-integrated-applications"></a>Applications pré-intégrées

Pour obtenir la liste de toutes les applications pour lesquelles Azure AD prend en charge un connecteur d’approvisionnement préintégré, consultez la [liste des didacticiels d’applications pour l’approvisionnement des utilisateurs](../saas-apps/tutorial-list.md).

Pour contacter l’équipe d’ingénierie Azure AD afin de demander une prise en charge de l’approvisionnement pour des applications supplémentaires, écrivez un message sur le [forum des commentaires sur Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> Pour qu’une application puisse prendre en charge l’approvisionnement automatique des utilisateurs, elle doit d’abord fournir les API de gestion des utilisateurs requises pour permettre aux programmes externes d’automatiser la création, la gestion et la suppression des utilisateurs. Par conséquent, toutes les applications SaaS ne sont pas compatibles avec cette fonctionnalité. Pour les applications qui prennent en charge les API de gestion des utilisateurs, l’équipe d’ingénierie Azure AD peut développer un connecteur d’approvisionnement. Ce travail s’effectue en fonction des besoins des clients actuels et à venir.

### <a name="connecting-applications-that-support-scim-20"></a>Connexion d’applications prenant en charge SCIM 2.0

Pour plus d’informations génériques sur la connexion d’applications qui implémentent des API de gestion SCIM 2.0, consultez [Utilisation du protocole SCIM (System for Cross-Domain Identity Management) pour configurer automatiquement des utilisateurs et groupes d’Azure Active Directory dans des applications](use-scim-to-provision-users-and-groups.md).

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Comment configurer l’approvisionnement automatique pour une application ?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Utilisez le portail Azure Active Directory pour configurer le service d’approvisionnement Azure AD pour une application sélectionnée.

1. Ouvrez le **[ portail Azure Active Directory](https://aad.portal.azure.com)** .
1. Dans le volet gauche, sélectionnez **Applications d’entreprise**. Une liste de toutes les applications configurées s’affiche.
1. Choisissez **+ Nouvelle application** pour ajouter une application. Ajoutez l’une des applications suivantes, selon votre scénario :

   - L’option **Ajouter votre application** prend en charge les intégrations SCIM personnalisées.
   - Toutes les applications de la section **Ajouter depuis la galerie** > **Applications recommandées** prennent en charge l’approvisionnement automatique. Consultez la [[liste des didacticiels d’applications pour l’approvisionnement des utilisateurs]](../saas-apps/tutorial-list.md) pour en obtenir davantage.

1. Renseignez des informations et sélectionnez **Ajouter**. La nouvelle application est ajoutée à la liste des applications d’entreprise et s’ouvre sur son écran de gestion d’application.
1. Sélectionnez **Approvisionnement** pour gérer les paramètres d’approvisionnement du compte utilisateur de l’application.

   ![Affiche l’écran Paramètres de provisionnement](./media/user-provisioning/provisioning_settings0.PNG)

1. Sélectionnez l’option Automatique comme **Mode d’approvisionnement** pour spécifier les paramètres des informations d’identification administrateur, des mappages, des arrêts et des démarrages et de la synchronisation.

   - Développez **Informations d’identification administrateur** pour saisir les informations d’identification requises par Azure AD pour se connecter à l’API de gestion des utilisateurs de l’application. Cette section vous permet également d’activer les notifications par e-mail en cas d’échec des informations d’identification, ou si le travail d’approvisionnement est [mis en quarantaine](#quarantine).
   - Développez **Mappages** pour afficher et modifier les attributs utilisateur qui circulent entre Azure AD et l’application cible lorsque des comptes d’utilisateur sont configurés ou mis à jour. Cette section permet également de configurer l’approvisionnement de groupes et de comptes d’utilisateurs si l’application prend en charge cette fonctionnalité. Sélectionnez un mappage dans la table pour ouvrir l’éditeur de mappage sur la droite, où vous pouvez afficher et personnaliser les attributs de l’utilisateur.

     Les **filtres d’étendue** indiquent au service d’approvisionnement quels utilisateurs et groupes du système source doivent être approvisionnés ou désapprovisionnés sur le système cible. Dans le volet **Mappage d’attribut**, sélectionnez **Étendue de l’objet source** pour filtrer des valeurs d’attribut spécifiques. Par exemple, vous pouvez spécifier que seuls les utilisateurs dont l’attribut « Service » est défini sur « Ventes » seront concernés par l’approvisionnement. Pour plus d’informations, consultez [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](define-conditional-rules-for-provisioning-user-accounts.md).

     Pour plus d’informations, consultez [Personnalisation des mappages d’attributs](customize-application-attributes.md).

   - Les **paramètres** contrôlent le fonctionnement du service d’approvisionnement pour une application, qu’elle soit ou non en cours d’exécution. Le menu **Étendue** vous permet de spécifier si seuls les utilisateurs et les groupes affectés ou tous les utilisateurs du répertoire Azure AD seront concernés par l’approvisionnement. Pour plus d’informations sur l’affectation d’utilisateurs et de groupes, consultez [Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory](assign-user-or-group-access-portal.md).

Dans l’écran de gestion des applications, sélectionnez **Journaux de provisionnement (préversion)** pour voir les enregistrements de chaque opération exécutée par le service de provisionnement Azure AD. Pour plus d’informations, consultez le [guide de création de rapports d’approvisionnement](check-status-user-account-provisioning.md).

![Exemple - Écran des journaux de provisionnement d’une application](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> Le service d’approvisionnement des utilisateurs Azure AD peut également être configuré et géré à l’aide de [l’API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

## <a name="what-happens-during-provisioning"></a>Que se passe-t-il pendant l’approvisionnement ?

Lorsque Azure AD est le système source, le service d’approvisionnement utilise la [fonctionnalité de requête différentielle de l’API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) pour surveiller les utilisateurs et groupes. Le service de provisionnement exécute un cycle initial sur le système source et le système cible, qui est suivi de cycles incrémentiels périodiques.

### <a name="initial-cycle"></a>Cycle initial

Lorsque le service d’approvisionnement est démarré, la première synchronisation effectuée permettra de réaliser les opérations suivantes :

1. Interroger tous les utilisateurs et groupes à partir du système source, afin de récupérer tous les attributs définis dans les [mappages d’attributs](customize-application-attributes.md).
1. Filtrer les utilisateurs et les groupes renvoyés à l’aide [d’affectations](assign-user-or-group-access-portal.md) ou de [filtres d’étendue basés sur un attribut](define-conditional-rules-for-provisioning-user-accounts.md) configurés.
1. Quand un utilisateur est affecté ou se trouve dans une étendue pour l’approvisionnement, le service interroge le système cible pour rechercher un utilisateur correspondant à l’aide des [attributs de correspondance](customize-application-attributes.md#understanding-attribute-mapping-properties) spécifiés. Exemple : si le nom userPrincipal dans le système source est l’attribut de correspondance et s’il est mappé à userName dans le système cible, le service d’approvisionnement interroge le système cible pour les userNames qui correspondent aux valeurs de nom userPrincipal dans le système source.
1. Si aucun utilisateur correspondant n’est trouvé dans le système cible, il est créé à l’aide des attributs renvoyés depuis le système source. Une fois que le compte d’utilisateur a été créé, le service de provisionnement détecte et met en cache l’ID du nouvel utilisateur dans le système cible ; cet ID sera utilisé pour exécuter toutes les opérations futures sur cet utilisateur.
1. Si un utilisateur correspondant est trouvé, il est mis à jour à l’aide des attributs fournis par le système source. Une fois que le compte d’utilisateur a été créé, le service de provisionnement détecte et met en cache l’ID du nouvel utilisateur dans le système cible ; cet ID sera utilisé pour exécuter toutes les opérations futures sur cet utilisateur.
1. Si les mappages d’attributs contiennent des attributs de « référence », le service effectue des mises à jour supplémentaires sur le système cible pour créer et lier les objets référencés. Par exemple, un utilisateur peut avoir un attribut « Manager » dans le système cible, qui est lié à un autre utilisateur créé dans le système cible.
1. Conserver un filigrane à la fin du cycle initial, qui fournit le point de départ pour les cycles incrémentiels ultérieurs.

Certaines applications telles que ServiceNow, G Suite et Box prennent non seulement en charge l’approvisionnement des utilisateurs, mais également celui des groupes et de leurs membres. Dans ce type de situation, si l’approvisionnement des groupes est activé dans les [mappages](customize-application-attributes.md), le service d’approvisionnement synchronise les utilisateurs et les groupes, puis les appartenances au groupe.

### <a name="incremental-cycles"></a>Cycles incrémentiels

Après le cycle initial, tous les autres cycles effectuent les opérations suivantes :

1. Interroger le système source pour rechercher les utilisateurs et les groupes qui ont été mis à jour depuis le dernier filigrane enregistré.
1. Filtrer les utilisateurs et les groupes renvoyés à l’aide [d’affectations](assign-user-or-group-access-portal.md) ou de [filtres d’étendue basés sur un attribut](define-conditional-rules-for-provisioning-user-accounts.md) configurés.
1. Quand un utilisateur est affecté ou se trouve dans une étendue pour l’approvisionnement, le service interroge le système cible pour rechercher un utilisateur correspondant à l’aide des [attributs de correspondance](customize-application-attributes.md#understanding-attribute-mapping-properties) spécifiés.
1. Si aucun utilisateur correspondant n’est trouvé dans le système cible, il est créé à l’aide des attributs renvoyés depuis le système source. Une fois que le compte d’utilisateur a été créé, le service de provisionnement détecte et met en cache l’ID du nouvel utilisateur dans le système cible ; cet ID sera utilisé pour exécuter toutes les opérations futures sur cet utilisateur.
1. Si un utilisateur correspondant est trouvé, il est mis à jour à l’aide des attributs fournis par le système source. Si le compte d’utilisateur correspondant trouvé est un nouveau compte assigné, le service de provisionnement détecte et met en cache l’ID du nouvel utilisateur dans le système cible ; cet ID sera utilisé pour exécuter toutes les opérations futures sur cet utilisateur.
1. Si les mappages d’attributs contiennent des attributs de « référence », le service effectue des mises à jour supplémentaires sur le système cible pour créer et lier les objets référencés. Par exemple, un utilisateur peut avoir un attribut « Manager » dans le système cible, qui est lié à un autre utilisateur créé dans le système cible.
1. Si un utilisateur qui se trouvait précédemment dans l’étendue de l’approvisionnement est supprimé de l’étendue (y compris si son affectation est annulée), le service désactive l’utilisateur dans le système cible via une mise à jour.
1. Si un utilisateur qui se trouvait précédemment dans l’étendue de l’approvisionnement est désactivé ou supprimé de façon réversible dans le système source, le service désactive l’utilisateur dans le système cible via une mise à jour.
1. Si un utilisateur qui se trouvait précédemment dans l’étendue de l’approvisionnement est définitivement supprimé du système source, le service efface l’utilisateur dans le système cible. Dans Azure AD, les utilisateurs sont supprimés définitivement 30 jours après leur suppression de façon réversible.
1. Conserver un nouveau filigrane à la fin du cycle initial, qui fournit le point de départ pour les cycles incrémentiels ultérieurs.

> [!NOTE]
> Vous pouvez éventuellement désactiver les opérations de **création**, **mise à jour** et **suppression** à l’aide des cases à cocher **Actions d’objet cible** dans la section [Mappages](customize-application-attributes.md). La logique pour désactiver un utilisateur pendant une mise à jour est également contrôlée via un mappage d’attributs à partir d’un champ tel que « accountEnabled ».

Le service de provisionnement continue à exécuter indéfiniment des cycles incrémentiels consécutifs, à des intervalles définis dans le [tutoriel spécifique à chaque application](../saas-apps/tutorial-list.md), jusqu’à ce que l’un des événements suivants se produise :

- Le service est arrêté manuellement à l’aide du portail Azure, ou à l’aide de la commande API Graph appropriée 
- Un nouveau cycle initial est déclenché au moyen de l’option **Effacer l’état en cours et redémarrer** dans le portail Azure, ou à l’aide de la commande API Graph appropriée. Cette action permet d’effacer les filigranes stockés et de rendre tous les objets source disponibles pour une nouvelle évaluation.
- Une nouveau cycle initial est déclenché en raison d’une modification dans les mappages d’attributs ou les filtres d’étendue. Cette action permet également d’effacer les filigranes stockés et de rendre tous les objets source disponibles pour une nouvelle évaluation.
- Le processus d’approvisionnement est mis en quarantaine (voir ci-dessous) en raison d’un taux d’erreur élevé et reste en quarantaine pendant plus de quatre semaines. Dans ce cas, le service sera automatiquement désactivé.

### <a name="errors-and-retries"></a>Erreurs et nouvelles tentatives

Si un utilisateur ne peut pas être ajouté, mis à jour ou supprimé dans le système cible en raison d’une erreur dans le système cible, une nouvelle tentative est effectuée lors du prochain cycle de synchronisation. Si l’utilisateur continue d’échouer, les nouvelles tentatives ne seront possibles qu’à une fréquence réduite, pour finalement n’être autorisées qu’une seule fois par jour. Pour résoudre le problème, les administrateurs doivent vérifier les [journaux de provisionnement](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) pour déterminer la cause racine et prendre les mesures nécessaires. Les échecs courants peuvent inclure :

- Utilisateurs n’ayant pas d’attribut renseigné dans le système source alors qu’il est requis dans le système cible
- Utilisateurs ayant une valeur d’attribut dans le système source pour laquelle il existe une contrainte unique dans le système cible, et la même valeur se trouve dans un autre enregistrement utilisateur

Ces échecs peuvent être résolus en ajustant les valeurs d’attribut de l’utilisateur concerné dans le système source, ou en ajustant les mappages d’attributs pour ne pas provoquer de conflits.

### <a name="quarantine"></a>Mise en quarantaine

Si la plupart ou la totalité des appels effectués sur le système cible échouent en permanence en raison d’une erreur (comme dans le cas d’informations d’identification non valides), le travail d’approvisionnement passe à un état « mis en quarantaine ». Cet état est indiqué dans le [rapport de synthèse sur l’approvisionnement](check-status-user-account-provisioning.md) et via les notifications par e-mail si elles ont été configurées dans le portail Azure.

Lors de la mise en quarantaine, la fréquence des cycles incrémentiels est progressivement réduite à une fois par jour.

Le travail d’approvisionnement sera supprimé de la mise en quarantaine après la correction de toutes les erreurs qui posent problème, et le prochain cycle de synchronisation démarre. Si le travail d’approvisionnement reste en quarantaine pendant plus de quatre semaines, celui-ci est désactivé.

## <a name="how-long-will-it-take-to-provision-users"></a>Combien de temps faut-il pour approvisionner des utilisateurs ?

Les performances varient selon que votre tâche d’approvisionnement exécute un cycle d’approvisionnement initial ou un cycle incrémentiel. Pour en savoir plus sur la durée de l’approvisionnement et comment surveiller l’était du service d’approvisionnement, consultez [Vérifier l’état de l’approvisionnement d’utilisateurs](application-provisioning-when-will-provisioning-finish-specific-user.md).

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Comment savoir si les utilisateurs sont correctement attribués ?

Toutes les opérations effectuées par le service de provisionnement d’utilisateurs sont enregistrées dans les [Journaux de provisionnement (préversion)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) Azure AD. Cela comprend toutes les opérations de lecture et d’écriture effectuées sur les systèmes sources et cibles, ainsi que les données utilisateur qui ont été lues ou écrites à chaque opération.

Pour plus d’informations sur la lecture des journaux de provisionnement dans le portail Azure, consultez le [guide de création de rapports sur le provisionnement](check-status-user-account-provisioning.md).

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Comment résoudre les problèmes liés à l’attribution d’utilisateurs ?

Pour obtenir des instructions basées sur un scénario concernant la manière de résoudre des problèmes d’approvisionnement automatique d’utilisateurs, voir [Problèmes lors de la configuration et de l’approvisionnement des utilisateurs pour une application](application-provisioning-config-problem.md).

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Quelles sont les bonnes pratiques à appliquer pour l’attribution automatique d’utilisateurs ?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Si vous souhaitez obtenir un exemple de plan de déploiement pas à pas pour l’attribution d’utilisateurs dans une application, consultez le [guide de déploiement des identités pour l’attribution d’utilisateurs](https://aka.ms/userprovisioningdeploymentplan).

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>L’attribution d’utilisateurs automatique dans les applications SaaS est-elle possible pour les utilisateurs B2B dans Azure AD ?

Oui, il est possible d’utiliser le service d’attribution d’utilisateurs Azure AD pour attribuer des utilisateurs B2B (ou invités) d’Azure AD dans des applications SaaS.

Toutefois, pour permettre aux utilisateurs B2B de se connecter à l’application SaaS à l’aide d’Azure AD, vous devez configurer la fonctionnalité d’authentification unique basée sur SAML de l’application SaaS d’une manière particulière. Pour plus d’informations sur la configuration des applications SaaS afin d’autoriser les connexions d’utilisateurs B2B, consultez [Configurer des applications SaaS pour B2B Collaboration]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>L’attribution d’utilisateurs automatique dans les applications SaaS est-elle possible pour les groupes dynamiques dans Azure AD ?

Oui. Quand il est configuré pour « synchroniser uniquement les utilisateurs et groupes attribués », le service d’attribution d’utilisateurs Azure AD peut attribuer des utilisateurs, ou annuler leur attribution, dans une application SaaS selon que les utilisateurs sont ou non membres d’un [groupe dynamique](../users-groups-roles/groups-create-rule.md). Les groupes dynamiques peuvent également être définis avec l’option pour « synchroniser tous les utilisateurs et groupes ».

Toutefois, l’utilisation de groupes dynamiques peut impacter les performances globales de l’attribution d’utilisateurs de bout en bout d’Azure AD dans les applications SaaS. Si vous utilisez des groupes dynamiques, gardez à l’esprit les mises en garde et suggestions suivantes :

- L’attribution d’un utilisateur dans un groupe dynamique, ou l’annulation de son attribution, dans une application SaaS est plus ou moins rapide selon la vitesse à laquelle le groupe dynamique réussit à analyser les changements d’appartenance. Pour plus d’informations sur la vérification de l’état de traitement d’un groupe dynamique, consultez [Vérifier l’état de traitement d’une règle d’appartenance](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

- Quand vous utilisez des groupes dynamiques, vous devez examiner attentivement les règles en lien avec l’attribution d’utilisateurs et l’annulation de leur attribution, car la suppression d’une appartenance entraîne un événement d’annulation de l’attribution.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>L’attribution d’utilisateurs automatique dans les applications SaaS est-elle possible pour les groupes imbriqués dans Azure AD ?

Non. Quand il est configuré pour « synchroniser uniquement les utilisateurs et groupes assignés », le service d’attribution d’utilisateurs Azure AD n’est pas en mesure d’obtenir ou d’attribuer des utilisateurs qui sont membres de groupes imbriqués. Il peut uniquement obtenir et attribuer les utilisateurs qui se trouvent directement dans le groupe assigné explicitement.

Il s’agit d’une limitation des « assignations basées sur les groupes dans les applications », qui concerne également l’authentification unique. Cette limitation est décrite dans [Utilisation d’un groupe pour gérer l’accès aux applications SaaS](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

La solution est alors d’assigner explicitement (ou [d’inclure dans l’étendue](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) les groupes qui contiennent les utilisateurs à attribuer.

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>L’approvisionnement entre Azure AD et une application cible utilise-t-il un canal chiffré ?

Oui. Nous utilisons le chiffrement HTTPS SSL pour le serveur cible.

## <a name="related-articles"></a>Articles connexes

- [Liste des didacticiels sur l’intégration des applications SaaS](../saas-apps/tutorial-list.md)
- [Personnalisation des mappages d’attributs pour le provisionnement des utilisateurs](customize-application-attributes.md)
- [Écriture d’expressions pour les mappages d’attributs](functions-for-customizing-application-data.md)
- [Filtres d’étendue pour le provisionnement des utilisateurs](define-conditional-rules-for-provisioning-user-accounts.md)
- [Utilisation de SCIM pour activer le provisionnement automatique des utilisateurs et des groupes d’Azure AD sur des applications](use-scim-to-provision-users-and-groups.md)
- [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Vue d’ensemble de l’API de synchronisation Azure AD)
