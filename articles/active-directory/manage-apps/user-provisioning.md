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
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f661aa67f04de23c7b4871e78d3628c639e7567
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144552"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Automatiser l’approvisionnement et le déprovisionnement des utilisateurs dans les applications avec Azure Active Directory

Dans Azure Active Directory (Azure AD), le terme **approvisionnement de l’application** désigne la création automatique des identités et rôles des utilisateurs dans les applications cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) auxquelles les utilisateurs ont besoin d’accéder. En plus de créer des identités utilisateur, l’approvisionnement automatique comprend la maintenance et la suppression d’identités utilisateur en cas de modification de l’état ou des rôles. Les scénarios courants incluent la configuration d’un utilisateur Azure AD dans des applications telles que [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial), [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial), et bien plus encore.

![Schéma de présentation de l’approvisionnement](media/user-provisioning/provisioning-overview.png)

Cette fonctionnalité vous permet de :

- **Automatiser l’approvisionnement :** Créez automatiquement des comptes dans les systèmes adéquats pour les nouvelles personnes rejoignant votre équipe ou votre organisation.
- **Automatiser le déprovisionnement :** Désactivez automatiquement les comptes dans les systèmes adéquats lorsque des personnes quittent votre équipe ou votre organisation.
- **Synchroniser les données entre les systèmes :** Vérifiez que les identités dans vos applications et systèmes sont tenues à jour selon les modifications apportées au répertoire ou à votre système de gestion des ressources humaines.
- **Approvisionner des groupes :** Approvisionnez des groupes pour les applications qui les prennent en charge.
- **Régir l’accès :** Surveillez et auditez les personnes qui ont été approvisionnées dans vos applications.
- **Déployer en toute transparence dans les scénarios « brown field » :** Faites correspondre les identités existantes entre les systèmes et facilitez l’intégration, même lorsque les utilisateurs existent déjà dans le système cible.
- **Utiliser une personnalisation riche :** Tirez parti des mappages d’attributs personnalisables qui définissent les données utilisateurs qui doivent circuler entre le système source et le système cible.
- **Obtenir des alertes pour les événements critiques :** Le service d’approvisionnement fournit des alertes pour les événements critiques et permet une intégration de Log Analytics dans laquelle vous pouvez définir des alertes personnalisées pour répondre aux besoins de votre entreprise.

## <a name="benefits-of-automatic-provisioning"></a>Avantages de l’approvisionnement automatique

À mesure que le nombre d’applications utilisées dans les organisations modernes continue de croître, les administrateurs informatiques sont chargés de la gestion des accès à l’échelle. Des normes telles que SAML (Security Assertions Markup Language) ou OIDC (Open ID Connect) permettent aux administrateurs de configurer rapidement l’authentification unique (SSO), mais l’accès requiert également que les utilisateurs soient approvisionnés dans l’application. Pour de nombreux administrateurs, l’approvisionnement consiste à créer manuellement chaque compte d’utilisateur ou à télécharger des fichiers CSV chaque semaine, mais ces processus prennent du temps, sont coûteux et peuvent engendrer des erreurs. Des solutions telles que SAML juste-à-temps (JIT) ont été adoptées pour automatiser l’approvisionnement, mais les entreprises ont également besoin d’une solution pour déprovisionner des utilisateurs lorsqu’ils quittent l’organisation ou n’ont plus besoin d’accéder à certaines applications en fonction du changement de rôle.

Voici quelques-unes des motivations courantes de l’utilisation de l’approvisionnement automatique :

- Optimisation de l’efficacité et de la précision des processus d’approvisionnement.
- Économies sur les coûts liés à l’hébergement et à la gestion de scripts et de solutions d’approvisionnement personnalisés.
- Sécurisation de votre organisation en supprimant instantanément les identités des utilisateurs des applications SaaS lorsqu’ils quittent l’organisation.
- Importation facile de nombreux utilisateurs dans un système ou une application SaaS spécifique.
- Disposer d’un ensemble unique de stratégies pour déterminer qui est approvisionné et qui peut se connecter à une application.

L’approvisionnement d’utilisateurs d’Azure AD peut vous aider à relever ces défis. Pour en savoir plus sur la façon dont les clients utilisent l’approvisionnement d’utilisateurs Azure AD, vous pouvez lire l’[étude de cas ASOS](https://aka.ms/asoscasestudy). La vidéo ci-dessous offre une vue d’ensemble de l’approvisionnement d’utilisateurs dans Azure AD :

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quels applications et systèmes puis-je utiliser avec l’approvisionnement d’utilisateurs automatique d’Azure AD ?

Azure AD offre une prise en charge préintégrée de plusieurs applications SaaS et systèmes de gestion des ressources humaines connus, ainsi qu’une prise en charge générique des applications qui implémentent des parties spécifiques du [standard SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Applications préintégrées (applications SaaS de la galerie)** . Vous trouverez toutes les applications pour lesquelles Azure AD prend en charge un connecteur d’approvisionnement préintégré dans la [liste des tutoriels d’applications pour l’approvisionnement d’utilisateurs](../saas-apps/tutorial-list.md). Les applications préintégrées répertoriées dans la galerie utilisent généralement des API de gestion des utilisateurs SCIM 2.0 pour l’approvisionnement. 

   ![Logo Salesforce](media/user-provisioning/gallery-app-logos.png)

   Si vous souhaitez demander une nouvelle application pour l’approvisionnement, vous pouvez [demander que votre application soit intégrée à notre galerie d’applications](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing). Pour une demande d’approvisionnement d’utilisateurs, nous avons besoin que l’application dispose d’un point de terminaison compatible SCIM. Veuillez demander au fournisseur de l’application de suivre la norme SCIM afin que nous puissions intégrer rapidement l’application à notre plateforme.

* **Applications prenant en charge SCIM 2.0**. Pour plus d’informations génériques sur la connexion d’applications qui implémentent des API de gestion des utilisateurs SCIM 2.0, consultez [Utilisation de SCIM pour approvisionner automatiquement des utilisateurs et groupes d’Azure Active Directory dans des applications](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-scim"></a>Qu’est-ce que SCIM ?

Pour aider à automatiser l’approvisionnement et le déprovisionnement, les applications exposent les API propriétaires d’utilisateurs et de groupes. Cependant, quiconque a essayé de gérer des utilisateurs dans plus d’une application vous dira que chaque application essaie d’effectuer les mêmes actions simples, telles que créer ou mettre à jour des utilisateurs, ajouter des utilisateurs à des groupes ou déprovisionner des utilisateurs. Pourtant, toutes ces actions simples sont implémentées un peu différemment, en utilisant des chemins d’accès de point de terminaison différents, des méthodes différentes pour spécifier les informations utilisateur et un schéma différent pour représenter chaque élément d’information.

Pour relever ces défis, la spécification SCIM fournit un schéma utilisateur commun pour aider les utilisateurs à se déplacer dans, hors et autour des applications. SCIM devient de facto la norme pour l’approvisionnement et, lorsqu’elle est utilisée conjointement avec des normes de fédération comme SAML ou OpenID Connect, fournit aux administrateurs une solution de bout en bout basée sur des normes pour la gestion des accès.

Pour obtenir des instructions détaillées sur l’utilisation de SCIM afin d’automatiser l’approvisionnement et le déprovisionnement d’utilisateurs et de groupes vers une application, consultez [Provisionnement d’utilisateurs SCIM avec Azure Active Directory](use-scim-to-provision-users-and-groups.md).

## <a name="how-does-automatic-provisioning-work"></a>Comment fonctionne l’approvisionnement automatique ?

Le **service d’approvisionnement Azure AD** approvisionne des utilisateurs pour les applications SaaS et les autres systèmes en se connectant aux points de terminaison d’API de gestion des utilisateurs fournis par chaque fournisseur d’application. Ces points de terminaison d’API de gestion des utilisateurs permettent à Azure AD de créer, de mettre à jour et de supprimer des utilisateurs par programmation. Pour les applications sélectionnées, le service d’approvisionnement peut également créer, mettre à jour et supprimer d’autres objets liés à l’identité, tels que les groupes et les rôles.

![Service de provisionnement Azure AD](./media/user-provisioning/provisioning0.PNG)
*Figure 1 : Le service d’approvisionnement Azure AD*

![Workflow de provisionnement des utilisateurs sortant](./media/user-provisioning/provisioning1.PNG)
*Figure 2 : Workflow d’approvisionnement d’utilisateurs « sortant » entre Azure AD et des applications SaaS populaires*

![Workflow de provisionnement des utilisateurs entrant](./media/user-provisioning/provisioning2.PNG)
*Figure 3 : Workflow d’approvisionnement d’utilisateurs « entrant » entre des applications de gestion du capital humain populaires, et Azure Active Directory et Windows Server Active Directory*

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Comment configurer l’approvisionnement automatique pour une application ?

Pour les applications préintégrées figurant dans la galerie, des instructions pas à pas sont disponibles pour configurer l’approvisionnement automatique. Consultez la [liste des tutoriels pour les applications de galerie intégrées](https://docs.microsoft.com/azure/active-directory/saas-apps/). La vidéo suivante montre comment configurer l’approvisionnement automatique d’utilisateurs pour SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Pour les autres applications qui prennent en charge SCIM 2.0, suivez les étapes décrites dans l’article [Provisionnement d’utilisateurs SCIM avec Azure Active Directory](use-scim-to-provision-users-and-groups.md).

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

Le travail d’approvisionnement sera supprimé de la mise en quarantaine après la correction de toutes les erreurs qui posent problème, et le prochain cycle de synchronisation démarre. Si le travail d’approvisionnement reste en quarantaine pendant plus de quatre semaines, celui-ci est désactivé. Pour en savoir plus sur l’état de quarantaine, [cliquez ici](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="how-long-will-it-take-to-provision-users"></a>Combien de temps faut-il pour approvisionner des utilisateurs ?

Les performances varient selon que votre tâche d’approvisionnement exécute un cycle d’approvisionnement initial ou un cycle incrémentiel. Pour en savoir plus sur la durée de l’approvisionnement et comment surveiller l’était du service d’approvisionnement, consultez [Vérifier l’état de l’approvisionnement d’utilisateurs](application-provisioning-when-will-provisioning-finish-specific-user.md).

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Comment savoir si les utilisateurs sont correctement attribués ?

Toutes les opérations effectuées par le service de provisionnement d’utilisateurs sont enregistrées dans les [Journaux de provisionnement (préversion)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) Azure AD. Cela comprend toutes les opérations de lecture et d’écriture effectuées sur les systèmes sources et cibles, ainsi que les données utilisateur qui ont été lues ou écrites à chaque opération.

Pour plus d’informations sur la lecture des journaux de provisionnement dans le portail Azure, consultez le [guide de création de rapports sur le provisionnement](check-status-user-account-provisioning.md).

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Comment résoudre les problèmes liés à l’attribution d’utilisateurs ?

Pour obtenir des instructions basées sur un scénario concernant la manière de résoudre des problèmes d’approvisionnement automatique d’utilisateurs, voir [Problèmes lors de la configuration et de l’approvisionnement des utilisateurs pour une application](application-provisioning-config-problem.md).

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Quelles sont les bonnes pratiques à appliquer pour l’attribution automatique d’utilisateurs ?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Si vous souhaitez obtenir un exemple de plan de déploiement pas à pas pour l’attribution d’utilisateurs dans une application, consultez le [guide de déploiement des identités pour l’attribution d’utilisateurs](https://aka.ms/deploymentplans/userprovisioning).

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
