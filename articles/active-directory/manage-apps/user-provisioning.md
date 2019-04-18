---
title: Attribution automatique des utilisateurs dans les applications SaaS dans Azure AD | Microsoft Docs
description: Cette introduction explique comment utiliser Azure AD pour approvisionner, annuler l’approvisionnement et mettre à jour de façon continue des comptes d’utilisateurs dans diverses applications SaaS tierces.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2019
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e8aaa60359fcfb85c79c4210f7c5cc14633c7b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894404"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory

## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Qu’est-ce que l’attribution automatique des utilisateurs dans les applications SaaS ?
Azure Active Directory (Azure AD) vous permet d’automatiser la création, la maintenance et la suppression d’identités utilisateur dans le cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) les applications telles que Dropbox, Salesforce, ServiceNow et bien plus encore.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

**Cette fonctionnalité vous permet de :**

- Créez automatiquement des comptes dans les systèmes adéquats pour les nouvelles personnes rejoignant votre équipe ou votre organisation.
- Désactivez automatiquement les comptes dans les systèmes adéquats lorsque des personnes quittent votre équipe ou votre organisation.
- Vérifiez que les identités dans vos applications et systèmes sont tenues à jour selon les modifications apportées au répertoire ou à votre système de gestion des ressources humaines.
- Procédez à l’approvisionnement d’objets non utilisateur, tels que des groupes, pour les applications qui les prennent en charge.

**Approvisionnement automatique des utilisateurs inclut également cette fonctionnalité :**

- Possibilité de faire correspondre des identités existantes entre les systèmes source et cible
- Des mappages d’attributs personnalisables qui définissent les données utilisateurs qui doivent circuler entre le système source et le système cible
- Alertes par courrier électronique pour les erreurs d’approvisionnement
- Création de rapports et journaux d’activité pour faciliter la supervision et la résolution des problèmes

## <a name="why-use-automated-provisioning"></a>Pourquoi utiliser l’approvisionnement automatique ?

Voici les principales raisons pour utiliser cette fonctionnalité :

- Réduction des coûts, des inefficacités et des erreurs humaines associés aux processus d’approvisionnement manuel
- Élimination des coûts liés à l'hébergement et à la gestion de scripts et de solutions d'approvisionnement personnalisés
- Sécurisation de votre organisation en supprimant instantanément les identités des utilisateurs à partir d’applications SaaS lorsqu’ils quittent l’organisation.
- Importation facilement un grand nombre d’utilisateurs dans une application SaaS spécifique ou un système.
- Avoir un seul ensemble de stratégies pour déterminer qui est approvisionné et qui peuvent se connecter à une application.

## <a name="how-does-automatic-provisioning-work"></a>Comment fonctionne l’approvisionnement automatique ?
    
Le **Service d’approvisionnement Azure AD** approvisionne des utilisateurs pour les applications SaaS et d’autres systèmes en vous connectant aux points de terminaison de gestion des API utilisateur fournies par chaque fournisseur de l’application. Ces points de terminaison d’API de gestion des utilisateurs permettent à Azure AD de créer, de mettre à jour et de supprimer des utilisateurs par programmation. Pour les applications sélectionnées, le service d’approvisionnement peut également créer, mettre à jour et supprimer des objets supplémentaires liées à l’identité, comme les groupes et rôles. 

![Approvisionnement](./media/user-provisioning/provisioning0.PNG)
*Figure 1 : Le service d’approvisionnement Azure AD*

![Approvisionnement sortant](./media/user-provisioning/provisioning1.PNG)
*Figure 2 : Workflow d’approvisionnement d’utilisateurs « sortant » entre Azure AD et des applications SaaS populaires*

![Approvisionnement entrant](./media/user-provisioning/provisioning2.PNG)
*Figure 3 : Workflow d’approvisionnement d’utilisateurs « entrant » entre des applications de gestion du capital humain populaires, et Azure Active Directory et Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quels applications et systèmes puis-je utiliser avec l’approvisionnement d’utilisateurs automatique d’Azure AD ?

Fonctionnalités Azure AD intégrées préalablement prise en charge de nombreux SaaS applications et ressources humaines systèmes courants et la prise en charge générique pour les applications qui implémentent des parties spécifiques du standard SCIM 2.0.

### <a name="pre-integrated-applications"></a>Applications pré-intégrées

Pour obtenir la liste de toutes les applications pour lesquelles Azure AD prend en charge un connecteur d’approvisionnement préintégré, consultez la [liste des didacticiels d’applications pour l’approvisionnement des utilisateurs](../saas-apps/tutorial-list.md).

Pour contacter l’équipe d’ingénierie Azure AD afin de demander une prise en charge de l’approvisionnement pour des applications supplémentaires, écrivez un message sur le [forum des commentaires sur Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> Pour qu’une application puisse prendre en charge l’approvisionnement automatique des utilisateurs, elle doit d’abord fournir les API de gestion des utilisateurs requises pour permettre aux programmes externes d’automatiser la création, la gestion et la suppression des utilisateurs. Par conséquent, toutes les applications SaaS ne sont pas compatibles avec cette fonctionnalité. Pour les applications qui ne prennent pas en charge les API de gestion utilisateur, l’équipe d’ingénierie Azure AD peut ensuite créer un connecteur d’approvisionnement pour ces applications, et ce travail s’effectue en fonction des besoins des clients actuels et futurs. 

### <a name="connecting-applications-that-support-scim-20"></a>Connexion d’applications prenant en charge SCIM 2.0

Pour plus d’informations génériques sur la connexion d’applications qui implémentent des API de gestion SCIM 2.0, consultez [Utilisation du protocole SCIM (System for Cross-Domain Identity Management) pour configurer automatiquement des utilisateurs et groupes d’Azure Active Directory dans des applications](use-scim-to-provision-users-and-groups.md).

    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Comment configurer l’approvisionnement automatique pour une application ?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Utilisez le portail Azure Active Directory pour configurer le service d’une application sélectionnée de provisionnement Azure AD.

1. Ouvrez le  **[portail Azure Active Directory](https://aad.portal.azure.com)**.

1. Sélectionnez **applications d’entreprise** dans le volet gauche. Une liste de toutes les applications configurées est présentées.

1. Choisissez **+ nouvelle application** pour ajouter une application. Ajoutez une des options suivantes selon votre scénario :

   - Le **ajouter votre propre application** option prend en charge les intégrations SCIM personnalisées.

   - Toutes les applications dans le **ajouter à partir de la galerie** > **applications recommandées** provisionnement automatique de section prise en charge. Consultez la [[liste des didacticiels d’applications pour l’approvisionnement des utilisateurs]](../saas-apps/tutorial-list.md) pour en obtenir davantage.

1. Fournir des détails et sélectionnez **ajouter**. La nouvelle application est ajoutée à la liste des applications d’entreprise et s’ouvre sur son écran de gestion d’application.

1. Sélectionnez **approvisionnement** pour gérer les paramètres de l’application d’approvisionnement de comptes utilisateur.

   ![Paramètres](./media/user-provisioning/provisioning_settings0.PNG)

1. Sélectionnez l’option automatique pour le **Mode d’approvisionnement** pour spécifier les paramètres pour les informations d’identification administrateur, mappages, démarrage et l’arrêt et la synchronisation.

   - Développez **informations d’identification administrateur** à entrer les informations d’identification requises pour Azure AD pour se connecter à l’API de gestion de l’application utilisateur. Cette section vous permet également d’activer les notifications par courrier électronique si les informations d’identification échouent, ou le travail d’approvisionnement est [mise en quarantaine](#quarantine).

   - Développez **mappages** pour afficher et modifier les attributs utilisateur qui circulent entre Azure AD et l’application cible lorsque les comptes d’utilisateur sont configurés ou mis à jour. Si l’application cible le prend en charge, cette section vous permet éventuellement utiliser la configuration des groupes et comptes d’utilisateur. Sélectionnez un mappage dans la table pour ouvrir l’éditeur de mappage vers la droite, où vous pouvez afficher et personnaliser les attributs de l’utilisateur.
   
     **Filtres d’étendue** indiquent le service d’approvisionnement les utilisateurs et groupes dans le système source doivent être mis en service ou annulés dans le système cible. Dans le **mappage d’attributs** volet, sélectionnez **portée de l’objet Source** pour filtrer sur les valeurs d’attribut spécifiques. Par exemple, vous pouvez spécifier que seuls les utilisateurs dont l’attribut « Service » est défini sur « Ventes » seront concernés par l’approvisionnement. Pour plus d’informations, consultez [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](define-conditional-rules-for-provisioning-user-accounts.md).
    
     Pour plus d’informations, consultez [Personnalisation des mappages d’attributs](customize-application-attributes.md).

   - **Paramètres** contrôlent le fonctionnement de ce service pour une application, notamment si elle est en cours d’exécution. Le **étendue** menu vous permet de spécifier si seuls les utilisateurs affectés et les groupes doivent être dans l’étendue de l’approvisionnement, ou si tous les utilisateurs dans l’annuaire Azure AD doivent être configurés. Pour plus d’informations sur l’affectation d’utilisateurs et de groupes, consultez [Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory](assign-user-or-group-access-portal.md).

Dans l’écran de gestion d’application, sélectionnez **journaux d’Audit** pour afficher les enregistrements de chaque opération exécuté par Azure AD service d’approvisionnement. Pour plus d’informations, consultez le [guide de création de rapports d’approvisionnement](check-status-user-account-provisioning.md).

![Paramètres](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> Le service d’approvisionnement des utilisateurs Azure AD peut également être configuré et géré à l’aide de [l’API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>Que se passe-t-il pendant l’approvisionnement ?

Lorsque Azure AD est le système source, le service d’approvisionnement utilise la [fonctionnalité de requête différentielle de l’API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) pour surveiller les utilisateurs et groupes. Le service d’approvisionnement exécute une synchronisation initiale sur le système source et le système cible, suivie de synchronisations incrémentielles périodiques. 

### <a name="initial-sync"></a>Synchronisation initiale

Lorsque le service d’approvisionnement est démarré, la première synchronisation éditoriaux sera :

1. Interroger tous les utilisateurs et groupes à partir du système source, afin de récupérer tous les attributs définis dans les [mappages d’attributs](customize-application-attributes.md).
2. Filtrer les utilisateurs et les groupes renvoyés à l’aide [d’affectations](assign-user-or-group-access-portal.md) ou de [filtres d’étendue basés sur un attribut](define-conditional-rules-for-provisioning-user-accounts.md) configurés.
3. Quand un utilisateur est affecté ou dans l’étendue de l’approvisionnement, le service interroge le système cible pour un utilisateur correspondant à l’aide de la [attributs de correspondance](customize-application-attributes.md#understanding-attribute-mapping-properties). Exemple : si le nom userPrincipal dans le système source est l’attribut de correspondance et s’il est mappé à userName dans le système cible, le service d’approvisionnement interroge le système cible pour les userNames qui correspondent aux valeurs de nom userPrincipal dans le système source.
4. Si un utilisateur correspondant est introuvable dans le système cible, il est créé à l’aide des attributs renvoyés depuis le système source. Une fois le compte d’utilisateur est créé, le service d’approvisionnement détecte et met en cache les ID du système cible pour le nouvel utilisateur, ce qui est utilisé pour exécuter toutes les opérations futures sur cet utilisateur.
5. Si un utilisateur correspondant est trouvé, il est mis à jour à l’aide des attributs fournis par le système source. Une fois le compte d’utilisateur est mis en correspondance, le service d’approvisionnement détecte et met en cache les ID du système cible pour le nouvel utilisateur, ce qui est utilisé pour exécuter toutes les opérations futures sur cet utilisateur.
6. Si les mappages d’attributs contiennent des attributs de « référence », le service effectue des mises à jour supplémentaires sur le système cible pour créer et lier les objets référencés. Par exemple, un utilisateur peut avoir un attribut « Manager » dans le système cible, qui est lié à un autre utilisateur créé dans le système cible.
7. Conserver un filigrane à la fin de la synchronisation initiale, qui fournit le point de départ pour les synchronisations incrémentielles plus tard.

Certaines applications telles que ServiceNow, Google Apps et Box prennent non seulement en charge l’approvisionnement des utilisateurs, mais également celui des groupes et de leurs membres. Dans ce cas, si l’approvisionnement du groupe est activée dans le [mappages](customize-application-attributes.md), le service d’approvisionnement synchronise les utilisateurs et les groupes, puis synchronise plus tard les appartenances de groupe. 

### <a name="incremental-syncs"></a>Synchronisations incrémentielles

Après la synchronisation initiale, tous les autres synchronisations seront :

1. Interroger le système source pour rechercher les utilisateurs et les groupes qui ont été mis à jour depuis le dernier filigrane enregistré.
2. Filtrer les utilisateurs et les groupes renvoyés à l’aide [d’affectations](assign-user-or-group-access-portal.md) ou de [filtres d’étendue basés sur un attribut](define-conditional-rules-for-provisioning-user-accounts.md) configurés.
3. Quand un utilisateur est affecté ou dans l’étendue de l’approvisionnement, le service interroge le système cible pour un utilisateur correspondant à l’aide de la [attributs de correspondance](customize-application-attributes.md#understanding-attribute-mapping-properties).
4. Si un utilisateur correspondant est introuvable dans le système cible, il est créé à l’aide des attributs renvoyés depuis le système source. Une fois le compte d’utilisateur est créé, le service d’approvisionnement détecte et met en cache les ID du système cible pour le nouvel utilisateur, ce qui est utilisé pour exécuter toutes les opérations futures sur cet utilisateur.
5. Si un utilisateur correspondant est trouvé, il est mis à jour à l’aide des attributs fournis par le système source. Dans le cas d’un compte qui vient d’être affecté est mis en correspondance, le service d’approvisionnement détecte et met en cache les ID du système cible pour le nouvel utilisateur, ce qui est utilisé pour exécuter toutes les opérations futures sur cet utilisateur.
6. Si les mappages d’attributs contiennent des attributs de « référence », le service effectue des mises à jour supplémentaires sur le système cible pour créer et lier les objets référencés. Par exemple, un utilisateur peut avoir un attribut « Manager » dans le système cible, qui est lié à un autre utilisateur créé dans le système cible.
7. Si un utilisateur qui se trouvait précédemment dans l’étendue de l’approvisionnement est supprimé de l’étendue (y compris si son affectation est annulée), le service désactive l’utilisateur dans le système cible via une mise à jour.
8. Si un utilisateur qui se trouvait précédemment dans l’étendue de l’approvisionnement est désactivé ou supprimé de façon réversible dans le système source, le service désactive l’utilisateur dans le système cible via une mise à jour.
9. Si un utilisateur qui se trouvait précédemment dans l’étendue de l’approvisionnement est définitivement supprimé du système source, le service efface l’utilisateur dans le système cible. Dans Azure AD, les utilisateurs sont supprimés définitivement 30 jours après leur suppression réversible.
10. Conserver un nouveau filigrane à la fin de la synchronisation incrémentielle, ce qui fournit le point de départ pour les synchronisations incrémentielles plus tard.

>[!NOTE]
> Vous pouvez éventuellement désactiver la **créer**, **mise à jour**, ou **supprimer** opérations à l’aide de la **actions d’objet cible** cases à cocher dans la [Mappages](customize-application-attributes.md) section. La logique pour désactiver un utilisateur pendant une mise à jour est également contrôlée via un mappage d’attributs à partir d’un champ tel que « accountEnabled ».

Le service d’approvisionnement poursuit en cours d’exécution des synchronisations incrémentielles DOS à DOS indéfiniment, à des intervalles définis dans le [didacticiel spécifique à chaque application](../saas-apps/tutorial-list.md), jusqu'à ce qu’un des événements suivants se produit :

- Le service est arrêté manuellement à l’aide du portail Azure, ou à l’aide de la commande API Graph appropriée 
- Une nouvelle synchronisation initiale est déclenchée à l’aide de l’option **Clear state and restart** (Effacer l’état et redémarrer) dans le portail Azure, ou à l’aide de la commande API Graph appropriée. Cette action efface les filigranes stockés et rend tous les objets de source doit être évaluée à nouveau.
- Une nouvelle synchronisation initiale est déclenchée en raison d’une modification dans les mappages d’attributs ou des filtres d’étendue. Cette action également efface les filigranes stockés et rend tous les objets de source doit être évaluée à nouveau.
- Le processus d’approvisionnement est mis en quarantaine (voir ci-dessous) en raison d’un taux d’erreur élevé et reste en quarantaine pendant plus de quatre semaines. Dans ce cas, le service sera automatiquement désactivé.

### <a name="errors-and-retries"></a>Erreurs et nouvelles tentatives

Si un utilisateur individuel ne peut pas être ajouté, mis à jour ou supprimé dans le système cible en raison d’une erreur dans le système cible, l’opération est retentée lors du prochain cycle de synchronisation. Si l’utilisateur continue d’échouer, les nouvelles tentatives ne seront possibles qu’à une fréquence réduite, pour finalement n’être autorisées qu’une seule fois par jour. Pour résoudre le problème, les administrateurs doivent vérifier le [journaux d’audit](check-status-user-account-provisioning.md) pour « traitement d’escrow » des événements afin de déterminer la racine provoquer et prenez les mesures appropriées. Les échecs courants peuvent inclure :

- Utilisateurs n’ayant pas d’attribut renseigné dans le système source alors qu’il est requis dans le système cible
- Les utilisateurs ayant une valeur d’attribut dans le système source pour lequel il existe une contrainte unique dans le système cible et la même valeur est présentes dans un autre enregistrement d’utilisateur

Ces échecs peuvent être résolus en ajustant les valeurs d’attribut de l’utilisateur concerné dans le système source, ou en ajustant les mappages d’attributs pour ne pas provoquer de conflits.   

### <a name="quarantine"></a>Mise en quarantaine

Si la plupart ou la totalité des appels effectués de manière cohérente sur le système cible échouent en raison d’une erreur (tel que les informations d’identification administrateur non valide), la tâche d’approvisionnement passe à l’état « quarantaine ». Cet état est indiqué dans le [rapport de synthèse](check-status-user-account-provisioning.md) et par courrier électronique si les notifications par e-mail ont été configurées dans le portail Azure. 

Lors de la mise en quarantaine, la fréquence des synchronisations incrémentielles est progressivement réduite à une fois par jour. 

La tâche d’approvisionnement est supprimée de la quarantaine lorsque toutes les erreurs qui posent problème sont fixes et démarre le prochain cycle de synchronisation. Si le travail d’approvisionnement reste en quarantaine pendant plus de quatre semaines, celui-ci est désactivé.


## <a name="how-long-will-it-take-to-provision-users"></a>Combien de temps faut-il pour approvisionner des utilisateurs ?

Performances varient selon que votre travail d’approvisionnement s’exécute une synchronisation initiale ou une synchronisation incrémentielle.

Pour **initiale des synchronisations**, le temps de travail dépend de nombreux facteurs, notamment le nombre d’utilisateurs et groupes dans l’étendue de l’approvisionnement et le nombre total d’utilisateurs et de groupe dans le système source. Une liste complète des facteurs affectant les performances de la synchronisation initiale est présentée plus loin dans cette section.

Pour les **synchronisations incrémentielles**, le temps nécessaire dépend du nombre de modifications détectées dans ce cycle de synchronisation. S’il existe moins de 5 000 utilisateurs ou les changements d’appartenance au groupe, le travail peut finir dans un cycle de synchronisation incrémentielle. 

Le tableau suivant récapitule les temps de synchronisation des scénarios d’approvisionnement courants. Dans ces scénarios, le système source est Azure AD et le système cible est une application SaaS. Les temps de synchronisation sont dérivés d’une analyse statistique des travaux de synchronisation pour les applications SaaS ServiceNow, Workplace, Salesforce et Google Apps.


| Configuration d’étendue | Utilisateurs, groupes et membres dans l’étendue | Temps de la synchronisation initiale | Temps de la synchronisation incrémentielle |
| -------- | -------- | -------- | -------- |
| Synchroniser les utilisateurs et groupes assignés uniquement |  < 1 000 |  < 30 minutes | < 30 minutes |
| Synchroniser les utilisateurs et groupes assignés uniquement |  1 000 - 10 000 | 142 - 708 minutes | < 30 minutes |
| Synchroniser les utilisateurs et groupes assignés uniquement |   10 000 - 100 000 | 1 170 - 2 340 minutes | < 30 minutes |
| Synchroniser l'ensemble des utilisateurs et groupes dans Azure AD |  < 1 000 | < 30 minutes  | < 30 minutes |
| Synchroniser l'ensemble des utilisateurs et groupes dans Azure AD |  1 000 - 10 000 | < 30 - 120 minutes | < 30 minutes |
| Synchroniser l'ensemble des utilisateurs et groupes dans Azure AD |  10 000 - 100 000  | 713 - 1 425 minutes | < 30 minutes |
| Synchroniser l’ensemble des utilisateurs dans Azure AD|  < 1 000  | < 30 minutes | < 30 minutes |
| Synchroniser l’ensemble des utilisateurs dans Azure AD | 1 000 - 10 000  | 43 - 86 minutes | < 30 minutes |


Pour la configuration **Synchroniser l’utilisateur et les groupes affectés uniquement**, vous pouvez utiliser les formules suivantes pour déterminer les temps minimum et maximum attendus pour la **synchronisation initiale** :

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Résumé des facteurs qui influencent le temps nécessaire pour terminer une **synchronisation initiale**  :

- Nombre total d'utilisateurs et de groupes concernés par l'approvisionnement.

- Nombre total d'utilisateurs, de groupes et de membres de groupe présents dans le système source (Azure AD).

- Les utilisateurs dans l’étendue de l’approvisionnement sont mis en correspondance avec des utilisateurs existants dans l’application cible ou qu’ils doivent être créés pour la première fois. Les travaux de synchronisation pour laquelle tous les utilisateurs sont créés pour la première fois prennent environ *deux fois plus longtemps* comme synchroniser des tâches pour lesquelles tous les utilisateurs sont mis en correspondance avec les utilisateurs existants.

- Nombre d’erreurs dans les [journaux d’audit](check-status-user-account-provisioning.md). Les performances sont ralenties s'il y a beaucoup d'erreurs et que le service d'approvisionnement est passé en quarantaine.    

- Limites de taux de requêtes et limitation implémentées par le système cible. Certains systèmes cible implémentent des limites de taux de demande et la limitation, ce qui peut affecter les performances lors des opérations de synchronisation importantes. Dans ces conditions, une application qui reçoit trop rapidement un trop grand nombre de requêtes risque d’afficher un taux de réponse plus faible ou d’interrompre la connexion. Pour améliorer les performances, le connecteur doit ajuster ce taux en évitant d’envoyer les requêtes d’application plus rapidement que ce que l’application est capable de traiter. Les connecteurs d’approvisionnement intégrés à Microsoft permettent d’effectuer cet ajustement. 

- Le nombre et la taille des groupes affectés. La synchronisation des groupes affectés prend plus de temps que la synchronisation des utilisateurs. Le nombre et la taille des groupes affectés ont un impact sur les performances. Si une application comporte des [mappages activés pour la synchronisation des objets de groupe](customize-application-attributes.md#editing-group-attribute-mappings), les propriétés de groupe, telles que les noms de groupe et les appartenances, sont synchronisées en plus des utilisateurs. Ces synchronisations supplémentaires prendront plus temps que la seule synchronisation des objets utilisateur.


## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Comment savoir si les utilisateurs sont correctement attribués ?

Toutes les opérations exécutées par l’utilisateur de service d’approvisionnement sont enregistrées dans Azure AD journaux d’audit. Cela inclut tous les lire et écrire des opérations effectuées sur les systèmes source et cible et les données de l’utilisateur qui a été lues ou écrites lors de chaque opération.

Pour plus d’informations sur la façon de lire les journaux d’audit dans le portail Azure, consultez le [guide de création de rapports d’approvisionnement](check-status-user-account-provisioning.md).


## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Comment résoudre les problèmes liés à l’attribution d’utilisateurs ?

Pour obtenir des instructions basées sur un scénario concernant la manière de résoudre des problèmes d’approvisionnement automatique d’utilisateurs, voir [Problèmes lors de la configuration et de l’approvisionnement des utilisateurs pour une application](application-provisioning-config-problem.md).


## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Quelles sont les bonnes pratiques à appliquer pour l’attribution automatique d’utilisateurs ?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Si vous souhaitez obtenir un exemple de plan de déploiement pas à pas pour l’attribution d’utilisateurs dans une application, consultez le [guide de déploiement des identités pour l’attribution d’utilisateurs](https://aka.ms/userprovisioningdeploymentplan).

## <a name="more-frequently-asked-questions"></a>Autres questions fréquentes

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>L’attribution d’utilisateurs automatique dans les applications SaaS est-elle possible pour les utilisateurs B2B dans Azure AD ?

Oui, il est possible d’utiliser l’approvisionnement des utilisateurs du service pour approvisionner B2B (ou invité) dans Azure AD pour les applications SaaS d’utilisateurs Azure AD.

Toutefois, pour B2B aux utilisateurs de se connecter à l’application SaaS à l’aide d’Azure AD, l’application SaaS doit avoir son basée sur SAML unique fonctionnalité d’authentification configurée de manière spécifique. Pour plus d’informations sur la façon de configurer les applications SaaS pour prendre en charge des connexions à partir des utilisateurs de B2B, consultez [SaaS de configurer des applications pour la collaboration B2B]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>L’attribution d’utilisateurs automatique dans les applications SaaS est-elle possible pour les groupes dynamiques dans Azure AD ?

Oui. Lorsque configuré pour « synchroniser uniquement affecté les utilisateurs et groupes », l’utilisateur Azure AD service d’approvisionnement peut approvisionner ou les utilisateurs de retirer dans une application SaaS, selon qu’ils soient membres d’un [groupe dynamique](../users-groups-roles/groups-create-rule.md). Les groupes dynamiques peuvent également être définis avec l’option pour « synchroniser tous les utilisateurs et groupes ».

Toutefois, l’utilisation de groupes dynamiques peut impacter les performances globales de l’attribution d’utilisateurs de bout en bout d’Azure AD dans les applications SaaS. Lorsque vous utilisez des groupes dynamiques, gardez ces mises en garde et les recommandations à l’esprit :

- L’attribution d’un utilisateur dans un groupe dynamique, ou l’annulation de son attribution, dans une application SaaS est plus ou moins rapide selon la vitesse à laquelle le groupe dynamique réussit à analyser les changements d’appartenance. Pour plus d’informations sur la vérification de l’état de traitement d’un groupe dynamique, consultez [Vérifier l’état de traitement d’une règle d’appartenance](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

- Lorsque vous utilisez des groupes dynamiques, les règles doivent être soigneusement planifié avec l’approvisionnement et annulation d’approvisionnement à l’esprit, comme une perte des résultats de l’appartenance dans un événement d’annulation.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>L’attribution d’utilisateurs automatique dans les applications SaaS est-elle possible pour les groupes imbriqués dans Azure AD ?

Non. Lorsque configuré pour « synchroniser uniquement affecté les utilisateurs et groupes », l’utilisateur Azure AD service d’approvisionnement n’est pas en mesure de lire ou configurer les utilisateurs qui se trouvent dans des groupes imbriqués. Il peut uniquement lire et approvisionner les utilisateurs qui sont membres immédiats du groupe affecté explicitement.

Il s’agit d’une limitation des « assignations basées sur les groupes dans les applications », qui concerne également l’authentification unique. Cette limitation est décrite dans [Utilisation d’un groupe pour gérer l’accès aux applications SaaS](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

Pour résoudre ce problème, vous devez affecter explicitement (ou autre [étendue dans](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) les groupes qui contiennent les utilisateurs qui doivent être approvisionnés.

## <a name="related-articles"></a>Articles connexes

- [Liste des didacticiels sur l’intégration des applications SaaS](../saas-apps/tutorial-list.md)
- [Personnalisation des mappages d’attributs pour l’approvisionnement des utilisateurs](customize-application-attributes.md)
- [Écriture d’expressions pour les mappages d’attributs](functions-for-customizing-application-data.md)
- [Filtres d’étendue pour l’approvisionnement des utilisateurs](define-conditional-rules-for-provisioning-user-accounts.md)
- [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](use-scim-to-provision-users-and-groups.md)
- [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Vue d’ensemble de l’API de synchronisation Azure AD)
