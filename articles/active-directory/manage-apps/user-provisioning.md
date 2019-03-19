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
ms.date: 07/30/2018
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 198f32b5f3d7572807b6af3e41ccf8085d9cbc0b
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576937"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory

## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Qu’est-ce que l’attribution automatique des utilisateurs dans les applications SaaS ?
Azure Active Directory (Azure AD) vous permet d’automatiser la création, la maintenance et la suppression d’identités utilisateur dans les applications cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) comme Dropbox, Salesforce, ServiceNow et bien plus encore.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

**Voici quelques exemples d’utilisation de cette fonctionnalité :**

* Créez automatiquement des comptes dans les systèmes adéquats pour les nouvelles personnes rejoignant votre équipe ou votre organisation.
* Désactivez automatiquement les comptes dans les systèmes adéquats lorsque des personnes quittent votre équipe ou votre organisation.
* Vérifiez que les identités dans vos applications et systèmes sont tenues à jour selon les modifications apportées au répertoire ou à votre système de gestion des ressources humaines.
* Procédez à l’approvisionnement d’objets non utilisateur, tels que des groupes, pour les applications qui les prennent en charge.

**L’approvisionnement automatique d’utilisateurs inclut également les fonctionnalités suivantes :**

* Possibilité de faire correspondre des identités existantes entre les systèmes source et cible
* Des mappages d’attributs personnalisables qui définissent les données utilisateurs qui doivent circuler entre le système source et le système cible
* Alertes par courrier électronique pour les erreurs d’approvisionnement
* Création de rapports et journaux d’activité pour faciliter la surveillance et la résolution des problèmes

## <a name="why-use-automated-provisioning"></a>Pourquoi utiliser l’approvisionnement automatique ?

Voici les principales raisons pour utiliser cette fonctionnalité :

* Réduction des coûts, des inefficacités et des erreurs humaines associés aux processus d’approvisionnement manuel
* Élimination des coûts liés à l'hébergement et à la gestion de scripts et de solutions d'approvisionnement personnalisés
* Sécurisation de votre organisation en supprimant instantanément les identités des utilisateurs des applications SaaS lorsqu’ils quittent l’organisation
* Importation facile de nombreux utilisateurs dans un système ou une application SaaS spécifique
* Disposer d’un ensemble unique de stratégies pour déterminer qui est approvisionné et qui peut se connecter à une application

## <a name="how-does-automatic-provisioning-work"></a>Comment fonctionne l’approvisionnement automatique ?
    
Le **service d’approvisionnement Azure AD** approvisionne des utilisateurs pour les applications SaaS et les autres systèmes en se connectant aux points de terminaison d’API de gestion des utilisateurs fournis par chaque fournisseur d’application. Ces points de terminaison d’API de gestion des utilisateurs permettent à Azure AD de créer, de mettre à jour et de supprimer des utilisateurs par programmation. Pour les applications sélectionnées, le service d’approvisionnement peut également créer, mettre à jour et supprimer d’autres objets liés à l’identité, tels que les groupes et les rôles. 

![Approvisionnement](./media/user-provisioning/provisioning0.PNG)
*Figure 1 : Le service d’approvisionnement Azure AD*

![Approvisionnement sortant](./media/user-provisioning/provisioning1.PNG)
*Figure 2 : Workflow d’approvisionnement d’utilisateurs « sortant » entre Azure AD et des applications SaaS populaires*

![Approvisionnement entrant](./media/user-provisioning/provisioning2.PNG)
*Figure 3 : Workflow d’approvisionnement d’utilisateurs « entrant » entre des applications de gestion du capital humain populaires, et Azure Active Directory et Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quels applications et systèmes puis-je utiliser avec l’approvisionnement d’utilisateurs automatique d’Azure AD ?

Azure AD offre une prise en charge préintégrée d’un large éventail d’applications SaaS et de systèmes de gestion des ressources humaines populaires, ainsi qu’une prise en charge générique des applications qui implémentent des parties spécifiques du standard SCIM 2.0.

### <a name="pre-integrated-applications"></a>Applications pré-intégrées

Pour obtenir la liste de toutes les applications pour lesquelles Azure AD prend en charge un connecteur d’approvisionnement préintégré, consultez la [liste des didacticiels d’applications pour l’approvisionnement des utilisateurs](../saas-apps/tutorial-list.md).

Pour contacter l’équipe d’ingénierie Azure AD afin de demander une prise en charge de l’approvisionnement pour des applications supplémentaires, écrivez un message sur le [forum des commentaires sur Azure Active Directory](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> Pour qu’une application puisse prendre en charge l’approvisionnement automatique des utilisateurs, elle doit d’abord fournir les API de gestion des utilisateurs requises pour permettre aux programmes externes d’automatiser la création, la gestion et la suppression des utilisateurs. Par conséquent, toutes les applications SaaS ne sont pas compatibles avec cette fonctionnalité. Pour les applications qui prennent en charge les API de gestion des utilisateurs, l’équipe d’ingénierie Azure AD peut développer un connecteur d’approvisionnement. Ce travail s’effectue en fonction des besoins des clients actuels et à venir. 

### <a name="connecting-applications-that-support-scim-20"></a>Connexion d’applications prenant en charge SCIM 2.0

Pour plus d’informations génériques sur la connexion d’applications qui implémentent des API de gestion SCIM 2.0, consultez [Utilisation du protocole SCIM (System for Cross-Domain Identity Management) pour configurer automatiquement des utilisateurs et groupes d’Azure Active Directory dans des applications](use-scim-to-provision-users-and-groups.md).

    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Comment configurer l’approvisionnement automatique pour une application ?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

La configuration du service d’approvisionnement Azure AD pour une application sélectionnée commence dans le **[portail Azure](https://portal.azure.com)**. Dans la section **Azure Active Directory > Applications d’entreprise**, sélectionnez **Ajouter**, **All** (Toutes), puis sélectionnez l’une des options suivantes selon votre scénario :

* Toutes les applications de la section **Applications recommandées** prennent en charge l’approvisionnement automatique. Consultez la [[liste des didacticiels d’applications pour l’approvisionnement des utilisateurs]](../saas-apps/tutorial-list.md) pour en obtenir davantage.

* Utilisez l’option Application ne figurant pas dans la galerie pour les intégrations SCIM personnalisées

![Galerie](./media/user-provisioning/gallery.png)

Dans l’écran de gestion des applications, la configuration de l’approvisionnement se fait à partir de l’onglet **Approvisionnement**.

![Paramètres](./media/user-provisioning/provisioning_settings0.PNG)


* Vous devez indiquer les **informations d’identification de l’administrateur** au service d’approvisionnement Azure AD afin qu’il puisse se connecter à l’API de gestion des utilisateurs fournie par l’application. Cette section vous permet également d’activer les notifications par e-mail en cas d’échec des informations d’identification, ou si le travail d’approvisionnement est [mis en quarantaine](#quarantine).

* Il est possible de configurer des **mappages d’attributs** pour que le contenu de champs spécifiques du système source (exemple : Azure AD) soit synchronisé avec des champs spécifiques du système cible (exemple : ServiceNow). Cette section permet également de configurer l’approvisionnement de groupes en plus de comptes d’utilisateurs si l’application prend en charge cette fonctionnalité. L’option Matching properties (Propriétés correspondantes) permet de sélectionner les champs utilisés pour faire correspondre les comptes entre les systèmes. Les [expressions](functions-for-customizing-application-data.md) permettent de modifier et de transformer les valeurs récupérées à partir du système source avant qu’elles soient écrites dans le système cible. Pour plus d’informations, consultez [Personnalisation des mappages d’attributs](customize-application-attributes.md).

![Paramètres](./media/user-provisioning/provisioning_settings1.PNG)

* Les **filtres d'étendue** indiquent au service d'approvisionnement quels utilisateurs et groupes du système source doivent être approvisionnés ou désapprovisionnés sur le système cible. Deux aspects des filtres d’étendue sont évalués ensemble pour déterminer qui est concerné par l’approvisionnement :

    * **Filter on attribute values** (Filtrer sur les valeurs d’attribut) : le menu Source Object Scope (Portée de l’objet source) dans les mappages d’attributs permet de filtrer sur des valeurs d’attribut spécifiques. Par exemple, vous pouvez spécifier que seuls les utilisateurs dont l’attribut « Service » est défini sur « Ventes » seront concernés par l’approvisionnement. Pour plus d’informations, consultez [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](define-conditional-rules-for-provisioning-user-accounts.md).

    * **Filter on assignments** (Filtrer sur les affectations) : le menu Étendue dans la section Approvisionnement &gt; Paramètres du portail permet de spécifier si seuls les utilisateurs et les groupes « affectés » ou tous les utilisateurs du répertoire Azure AD seront concernés par l’approvisionnement. Pour plus d’informations sur l’affectation d’utilisateurs et de groupes, consultez [Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory](assign-user-or-group-access-portal.md).
    
* Les **paramètres** contrôlent le fonctionnement du service d’approvisionnement pour une application, qu’elle soit ou non en cours d’exécution.

* Les **journaux d’audit** fournissent des enregistrements de chaque opération effectuée par le service d’approvisionnement Azure AD. Pour plus d’informations, consultez le [guide de création de rapports d’approvisionnement](check-status-user-account-provisioning.md).

![Paramètres](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> Le service d’approvisionnement des utilisateurs Azure AD peut également être configuré et géré à l’aide de [l’API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>Que se passe-t-il pendant l’approvisionnement ?

Lorsque Azure AD est le système source, le service d’approvisionnement utilise la [fonctionnalité de requête différentielle de l’API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) pour surveiller les utilisateurs et groupes. Le service d’approvisionnement exécute une synchronisation initiale sur le système source et le système cible, suivie de synchronisations incrémentielles périodiques. 

### <a name="initial-sync"></a>Synchronisation initiale

Lorsque le service d’approvisionnement est démarré, la première synchronisation effectuée permettra de réaliser les opérations suivantes :

1. Interroger tous les utilisateurs et groupes à partir du système source, afin de récupérer tous les attributs définis dans les [mappages d’attributs](customize-application-attributes.md).
2. Filtrer les utilisateurs et les groupes renvoyés à l’aide [d’affectations](assign-user-or-group-access-portal.md) ou de [filtres d’étendue basés sur un attribut](define-conditional-rules-for-provisioning-user-accounts.md) configurés.
3. Quand un utilisateur se trouve être affecté ou dans une étendue pour l’approvisionnement, le service interroge le système cible pour rechercher un utilisateur correspondant à l’aide des [attributs de correspondance](customize-application-attributes.md#understanding-attribute-mapping-properties) désignés. Exemple : si le nom userPrincipal dans le système source est l’attribut de correspondance et s’il est mappé à userName dans le système cible, le service d’approvisionnement interroge le système cible pour les userNames qui correspondent aux valeurs de nom userPrincipal dans le système source.
4. Si aucun utilisateur correspondant n’est trouvé dans le système cible, il est créé à l’aide des attributs renvoyés depuis le système source. Une fois que le compte d’utilisateur a été créé, le service de provisionnement détecte et met en cache l’ID du nouvel utilisateur dans le système cible ; cet ID sera utilisé pour effectuer toutes les opérations futures sur cet utilisateur.
5. Si un utilisateur correspondant est trouvé, il est mis à jour à l’aide des attributs fournis par le système source. Une fois que le compte d’utilisateur correspondant a été trouvé, le service de provisionnement détecte et met en cache l’ID du nouvel utilisateur dans le système cible ; cet ID sera utilisé pour effectuer toutes les opérations futures sur cet utilisateur.
6. Si les mappages d’attributs contiennent des attributs de « référence », le service effectue des mises à jour supplémentaires sur le système cible pour créer et lier les objets référencés. Par exemple, un utilisateur peut avoir un attribut « Manager » dans le système cible, qui est lié à un autre utilisateur créé dans le système cible.
7. Conserver un filigrane à la fin de la synchronisation initiale, qui fournit le point de départ pour les synchronisations incrémentielles suivantes.

Certaines applications telles que ServiceNow, Google Apps et Box prennent non seulement en charge l’approvisionnement des utilisateurs, mais également celui des groupes et de leurs membres. Dans ce type de situation, si l’approvisionnement des groupes est activé dans les [mappages](customize-application-attributes.md), le service d’approvisionnement synchronise les utilisateurs et les groupes, puis les appartenances au groupe. 

### <a name="incremental-syncs"></a>Synchronisations incrémentielles

Après la synchronisation initiale, toutes les synchronisations suivantes effectueront les opérations ci-dessous :

1. Interroger le système source pour rechercher les utilisateurs et les groupes qui ont été mis à jour depuis le dernier filigrane enregistré.
2. Filtrer les utilisateurs et les groupes renvoyés à l’aide [d’affectations](assign-user-or-group-access-portal.md) ou de [filtres d’étendue basés sur un attribut](define-conditional-rules-for-provisioning-user-accounts.md) configurés.
3. Quand un utilisateur se trouve être affecté ou dans une étendue pour l’approvisionnement, le service interroge le système cible pour rechercher un utilisateur correspondant à l’aide des [attributs de correspondance](customize-application-attributes.md#understanding-attribute-mapping-properties) désignés.
4. Si aucun utilisateur correspondant n’est trouvé dans le système cible, il est créé à l’aide des attributs renvoyés depuis le système source. Une fois que le compte d’utilisateur a été créé, le service de provisionnement détecte et met en cache l’ID du nouvel utilisateur dans le système cible ; cet ID sera utilisé pour effectuer toutes les opérations futures sur cet utilisateur.
5. Si un utilisateur correspondant est trouvé, il est mis à jour à l’aide des attributs fournis par le système source. Si le compte d’utilisateur correspondant trouvé est un nouveau compte assigné, le service de provisionnement détecte et met en cache l’ID du nouvel utilisateur dans le système cible ; cet ID sera utilisé pour effectuer toutes les opérations futures sur cet utilisateur.
6. Si les mappages d’attributs contiennent des attributs de « référence », le service effectue des mises à jour supplémentaires sur le système cible pour créer et lier les objets référencés. Par exemple, un utilisateur peut avoir un attribut « Manager » dans le système cible, qui est lié à un autre utilisateur créé dans le système cible.
7. Si un utilisateur qui se trouvait précédemment dans l’étendue de l’approvisionnement est supprimé de l’étendue (y compris si son affectation est annulée), le service désactive l’utilisateur dans le système cible via une mise à jour.
8. Si un utilisateur qui se trouvait précédemment dans l’étendue de l’approvisionnement est désactivé ou supprimé de façon réversible dans le système source, le service désactive l’utilisateur dans le système cible via une mise à jour.
9. Si un utilisateur qui se trouvait précédemment dans l’étendue de l’approvisionnement est définitivement supprimé du système source, le service efface l’utilisateur dans le système cible. Dans Azure AD, les utilisateurs sont supprimés définitivement 30 jours après leur suppression de façon réversible.
10. Conserver un nouveau filigrane à la fin de la synchronisation initiale, qui fournit le point de départ pour les synchronisations incrémentielles suivantes.

>[!NOTE]
> Vous pouvez éventuellement désactiver les opérations de création, mise à jour et suppression à l’aide des cases à cocher **Actions d’objet cible** dans la section [Mappages d’attributs](customize-application-attributes.md). La logique pour désactiver un utilisateur pendant une mise à jour est également contrôlée via un mappage d’attributs à partir d’un champ tel que « accountEnabled ».

Le service d’approvisionnement continuera à exécuter indéfiniment des synchronisations incrémentielles dos à dos, à des intervalles définis dans le [didacticiel spécifique à chaque application](../saas-apps/tutorial-list.md), jusqu’à ce que l’un des événements suivants se produise :

* Le service est arrêté manuellement à l’aide du portail Azure, ou à l’aide de la commande API Graph appropriée 
* Une nouvelle synchronisation initiale est déclenchée à l’aide de l’option **Clear state and restart** (Effacer l’état et redémarrer) dans le portail Azure, ou à l’aide de la commande API Graph appropriée. Cela permet d’effacer les filigranes stockés et de rendre tous les objets source disponibles pour une nouvelle évaluation.
* Une nouvelle synchronisation initiale est déclenchée en raison d’une modification dans les mappages d’attributs ou les filtres d’étendue. Cela permet également d’effacer les filigranes stockés et de rendre tous les objets source disponibles pour une nouvelle évaluation.
* Le processus d’approvisionnement est mis en quarantaine (voir ci-dessous) en raison d’un taux d’erreur élevé et reste en quarantaine pendant plus de quatre semaines. Dans ce cas, le service sera automatiquement désactivé.

### <a name="errors-and-retries"></a>Erreurs et nouvelles tentatives

Si un utilisateur ne peut pas être ajouté, mis à jour ou supprimé dans le système cible en raison d’une erreur dans le système cible, une nouvelle tentative sera effectuée lors du prochain cycle de synchronisation. Si l’utilisateur continue d’échouer, les nouvelles tentatives ne seront possibles qu’à une fréquence réduite, pour finalement n’être autorisées qu’une seule fois par jour. Pour résoudre le problème, les administrateurs devront vérifier les [journaux d’audit](check-status-user-account-provisioning.md) pour identifier les événements « traitement d’escrow », afin de déterminer la cause racine et prendre les mesures nécessaires. Les échecs courants peuvent inclure :

* Utilisateurs n’ayant pas d’attribut renseigné dans le système source alors qu’il est requis dans le système cible
* Utilisateurs ayant une valeur d’attribut dans le système source pour laquelle il existe une contrainte unique dans le système cible, et la même valeur se trouve dans un autre enregistrement utilisateur

Ces échecs peuvent être résolus en ajustant les valeurs d’attribut de l’utilisateur concerné dans le système source, ou en ajustant les mappages d’attributs pour ne pas provoquer de conflits.   

### <a name="quarantine"></a>Mise en quarantaine

Si la plupart ou la totalité des appels effectués sur le système cible échouent en permanence en raison d’une erreur (comme dans le cas d’informations d’identification non valides), le travail d’approvisionnement passe à un état « mis en quarantaine ». Cela est indiqué dans le [rapport de synthèse sur l’approvisionnement](check-status-user-account-provisioning.md) et via les notifications par e-mail si elles ont été configurées dans le portail Azure. 

Lors de la mise en quarantaine, la fréquence des synchronisations incrémentielles est progressivement réduite à une fois par jour. 

Le travail d’approvisionnement sera supprimé de la mise en quarantaine après la correction de toutes les erreurs qui posent problème, et le prochain cycle de synchronisation démarre. Si le travail d’approvisionnement reste en quarantaine pendant plus de quatre semaines, celui-ci est désactivé.


## <a name="how-long-will-it-take-to-provision-users"></a>Combien de temps faut-il pour approvisionner des utilisateurs ?

Les performances seront différentes selon que votre travail d’approvisionnement effectue une synchronisation initiale ou une synchronisation incrémentielle, comme décrit dans la section précédente.

Pour les **synchronisations initiales**, le temps nécessaire dépend de divers facteurs, notamment le nombre d’utilisateurs et de groupes dans la portée pour l’approvisionnement, ainsi que le nombre total d’utilisateurs et de groupe dans le système source. Une liste complète des facteurs affectant les performances de la synchronisation initiale est présentée plus loin dans cette section.

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

* Nombre total d'utilisateurs et de groupes concernés par l'approvisionnement.

* Nombre total d'utilisateurs, de groupes et de membres de groupe présents dans le système source (Azure AD).

* Si les utilisateurs dans l’étendue pour l’approvisionnement sont mis en correspondance ou non avec des utilisateurs existants dans l’application cible, ou doivent être créés la première fois. Les travaux de synchronisation pour lesquels tous les utilisateurs sont créés pour la première fois prennent environ *deux fois plus* de temps que les travaux de synchronisation pour lesquels tous les utilisateurs sont mis en correspondance avec des utilisateurs existants.

* Nombre d’erreurs dans les [journaux d’audit](check-status-user-account-provisioning.md). Les performances sont ralenties s'il y a beaucoup d'erreurs et que le service d'approvisionnement est passé en quarantaine.    

* Limites de taux de requêtes et limitation implémentées par le système cible. Certains systèmes cible implémentent des limites de taux de requêtes et une limitation qui peuvent affecter les performances lors d’opérations de synchronisation de grande envergure. Dans ces conditions, une application qui reçoit trop rapidement un trop grand nombre de requêtes risque d’afficher un taux de réponse plus faible ou d’interrompre la connexion. Pour améliorer les performances, le connecteur doit ajuster ce taux en évitant d’envoyer les requêtes d’application plus rapidement que ce que l’application est capable de traiter. Les connecteurs d’approvisionnement intégrés à Microsoft permettent d’effectuer cet ajustement. 

* Le nombre et la taille des groupes affectés. La synchronisation des groupes affectés prend plus de temps que la synchronisation des utilisateurs. Le nombre et la taille des groupes affectés ont un impact sur les performances. Si une application comporte des [mappages activés pour la synchronisation des objets de groupe](customize-application-attributes.md#editing-group-attribute-mappings), les propriétés de groupe, telles que les noms de groupe et les appartenances, sont synchronisées en plus des utilisateurs. Ces synchronisations supplémentaires prendront plus temps que la seule synchronisation des objets utilisateur.


## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Comment savoir si les utilisateurs sont correctement attribués ?

Toutes les opérations effectuées par le service d’attribution d’utilisateurs sont enregistrées dans les journaux d’audit Azure AD. Cela comprend toutes les opérations de lecture et d’écriture effectuées sur les systèmes sources et cibles, ainsi que les données utilisateur qui ont été lues ou écrites à chaque opération.

Pour plus d’informations sur la lecture des journaux d’audit dans le portail Azure, consultez le [guide de création de rapports sur le provisionnement](check-status-user-account-provisioning.md).


## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Comment résoudre les problèmes liés à l’attribution d’utilisateurs ?

Pour obtenir des instructions basées sur un scénario concernant la manière de résoudre des problèmes d’approvisionnement automatique d’utilisateurs, voir [Problèmes lors de la configuration et de l’approvisionnement des utilisateurs pour une application](application-provisioning-config-problem.md).


## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Quelles sont les bonnes pratiques à appliquer pour l’attribution automatique d’utilisateurs ?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Si vous souhaitez obtenir un exemple de plan de déploiement pas à pas pour l’attribution d’utilisateurs dans une application, consultez le [guide de déploiement des identités pour l’attribution d’utilisateurs](https://aka.ms/userprovisioningdeploymentplan).

## <a name="more-frequently-asked-questions"></a>Autres questions fréquentes

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>L’attribution d’utilisateurs automatique dans les applications SaaS est-elle possible pour les utilisateurs B2B dans Azure AD ?

Oui, il est possible d’utiliser le service d’attribution d’utilisateurs Azure AD pour attribuer des utilisateurs B2B (ou invités) d’Azure AD dans des applications SaaS.

Toutefois, pour permettre aux utilisateurs B2B de se connecter à l’application SaaS à l’aide d’Azure AD, vous devez configurer la fonctionnalité d’authentification unique basée sur SAML de l’application SaaS d’une manière particulière. Pour plus d’informations sur la configuration des applications SaaS afin d’autoriser les connexions d’utilisateurs B2B, consultez [Configurer des applications SaaS pour B2B Collaboration]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>L’attribution d’utilisateurs automatique dans les applications SaaS est-elle possible pour les groupes dynamiques dans Azure AD ?

Oui. Quand il est configuré pour « synchroniser uniquement les utilisateurs et groupes attribués », le service d’attribution d’utilisateurs Azure AD peut attribuer des utilisateurs, ou annuler leur attribution, dans une application SaaS selon que les utilisateurs sont ou non membres d’un [groupe dynamique](../users-groups-roles/groups-create-rule.md). Les groupes dynamiques peuvent également être définis avec l’option pour « synchroniser tous les utilisateurs et groupes ».

Toutefois, l’utilisation de groupes dynamiques peut impacter les performances globales de l’attribution d’utilisateurs de bout en bout d’Azure AD dans les applications SaaS. Si vous utilisez des groupes dynamiques, gardez à l’esprit les mises en garde et suggestions suivantes :

* L’attribution d’un utilisateur dans un groupe dynamique, ou l’annulation de son attribution, dans une application SaaS est plus ou moins rapide selon la vitesse à laquelle le groupe dynamique réussit à analyser les changements d’appartenance. Pour plus d’informations sur la vérification de l’état de traitement d’un groupe dynamique, consultez [Vérifier l’état de traitement d’une règle d’appartenance](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

* Quand vous utilisez des groupes dynamiques, vous devez examiner attentivement les règles en lien avec l’attribution d’utilisateurs et l’annulation de leur attribution, car la suppression d’une appartenance entraîne un événement d’annulation de l’attribution.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>L’attribution d’utilisateurs automatique dans les applications SaaS est-elle possible pour les groupes imbriqués dans Azure AD ?

 Non. Quand il est configuré pour « synchroniser uniquement les utilisateurs et groupes assignés », le service d’attribution d’utilisateurs Azure AD n’est pas en mesure d’obtenir ou d’attribuer des utilisateurs qui sont membres de groupes imbriqués. Il peut uniquement obtenir et attribuer les utilisateurs qui se trouvent directement dans le groupe assigné explicitement.

Il s’agit d’une limitation des « assignations basées sur les groupes dans les applications », qui concerne également l’authentification unique. Cette limitation est décrite dans [Utilisation d’un groupe pour gérer l’accès aux applications SaaS](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

La solution est alors d’assigner explicitement (ou [d’inclure dans l’étendue](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) les groupes qui contiennent les utilisateurs à attribuer.

## <a name="related-articles"></a>Articles connexes

* [Liste des didacticiels sur l’intégration des applications SaaS](../saas-apps/tutorial-list.md)
* [Personnalisation des mappages d’attributs pour l’approvisionnement des utilisateurs](customize-application-attributes.md)
* [Écriture d’expressions pour les mappages d’attributs](functions-for-customizing-application-data.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](define-conditional-rules-for-provisioning-user-accounts.md)
* [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](use-scim-to-provision-users-and-groups.md)
* [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Vue d’ensemble de l’API de synchronisation Azure AD)
