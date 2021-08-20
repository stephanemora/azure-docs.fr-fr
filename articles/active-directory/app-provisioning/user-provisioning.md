---
title: Qu’est ce que le provisionnement automatique des utilisateurs dans les applications SaaS dans Azure Active Directory ?
description: Cette introduction explique comment utiliser Azure Active Directory pour provisionner, déprovisionner et mettre à jour en continu des comptes d’utilisateur dans diverses applications SaaS tierces.
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: overview
ms.workload: identity
ms.date: 05/28/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 95bd9ea10f857ef5b4eb72b0fb3449bbcb5a67d9
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437328"
---
# <a name="what-is-app-provisioning-in-azure-active-directory"></a>Qu’est-ce que le provisionnement d’application dans Azure Active Directory ?

Dans Azure Active Directory (Azure AD), le terme *provisionnement de l’application* désigne la création automatique des identités et rôles des utilisateurs pour les applications.
    
![Diagramme illustrant les scénarios de provisionnement.](../governance/media/what-is-provisioning/provisioning.png)

Le provisionnement d’application Azure AD vers SaaS (software as a service) désigne la création automatique de rôles et d’identités utilisateur dans les applications cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) auxquelles les utilisateurs ont besoin d’accéder. En plus de créer des identités utilisateur, l’approvisionnement automatique comprend la maintenance et la suppression d’identités utilisateur en cas de modification de l’état ou des rôles. Les scénarios courants incluent la configuration d’un utilisateur Azure AD dans des applications telles que [Dropbox](../../active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../../active-directory/saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../../active-directory/saas-apps/servicenow-provisioning-tutorial.md), et bien plus encore.

Azure AD prend en charge le provisionnement d’utilisateurs dans des applications SaaS et des applications hébergées localement, ou une solution IaaS (infrastructure as a service) telle qu’une machine virtuelle. Vous avez peut-être une application héritée qui repose sur un magasin d’utilisateurs LDAP ou une base de données SQL. Le service de provisionnement Azure AD vous permet de créer, de mettre à jour et de supprimer des utilisateurs dans des applications locales sans avoir à ouvrir des pare-feu ou à gérer des ports TCP. 

Grâce à des agents légers, vous pouvez provisionner les utilisateurs dans des applications locales et gérer l’accès. Lorsqu’Azure AD est utilisé avec le proxy d’application, vous pouvez gérer l’accès à votre application locale et fournir le provisionnement automatique des utilisateurs (avec le service de provisionnement) ainsi que l’authentification unique (avec le proxy d’application). 

Le provisionnement des applications vous permet :

- **Automatiser l’approvisionnement :** Créez automatiquement des comptes dans les systèmes adéquats pour les nouvelles personnes rejoignant votre équipe ou votre organisation.
- **Automatiser le déprovisionnement** : désactivez automatiquement les comptes dans les systèmes appropriés lorsque des personnes quittent l’équipe ou l’organisation.
- **Synchroniser les données entre les systèmes** : veillez à ce que les identités dans vos applications et systèmes soient tenues à jour suite aux modifications apportées à l’annuaire ou à votre système de gestion des ressources humaines.
- **Approvisionner des groupes :** Approvisionnez des groupes pour les applications qui les prennent en charge.
- **Régir l’accès** : supervisez et auditez les personnes qui ont été provisionnées dans vos applications.
- **Déployer en toute transparence dans les scénarios « brown field » :** Faites correspondre les identités existantes entre les systèmes et facilitez l’intégration, même lorsque les utilisateurs existent déjà dans le système cible.
- **Utiliser une personnalisation riche :** Tirez parti des mappages d’attributs personnalisables qui définissent les données utilisateurs qui doivent circuler entre le système source et le système cible.
- **Obtenir des alertes pour les événements critiques :** Le service d’approvisionnement fournit des alertes pour les événements critiques et permet une intégration de Log Analytics dans laquelle vous pouvez définir des alertes personnalisées pour répondre aux besoins de votre entreprise.

## <a name="what-is-scim"></a>Qu’est-ce que SCIM ?

Pour aider à automatiser l’approvisionnement et le déprovisionnement, les applications exposent les API propriétaires d’utilisateurs et de groupes. Cependant, quiconque a essayé de gérer des utilisateurs dans plus d’une application vous dira que chaque application essaie d’effectuer les mêmes actions, telles que créer ou mettre à jour des utilisateurs, ajouter des utilisateurs à des groupes ou déprovisionner des utilisateurs. Pourtant, toutes ces actions sont implémentées légèrement différemment, en utilisant des chemins de point de terminaison différents, des méthodes différentes pour spécifier les informations utilisateur et un schéma différent pour représenter chaque élément d’information.

Pour relever ces défis, la spécification SCIM (System for Cross-domain Identity Management) fournit un schéma utilisateur commun pour aider les utilisateurs à se déplacer dans, hors et autour des applications. SCIM devient la norme de provisionnement de facto et, lorsqu’elle est utilisée avec des normes de fédération comme SAML (Security Assertions Markup Language) ou OIDC (OpenID Connect), elle fournit aux administrateurs une solution de bout en bout basée sur des normes pour la gestion de l’accès.

Pour obtenir des instructions détaillées sur le développement d’un point de terminaison SCIM afin d’automatiser le provisionnement et le déprovisionnement d’utilisateurs et de groupes dans une application, consultez [Créer un point de terminaison SCIM et configurer l’attribution des utilisateurs](use-scim-to-provision-users-and-groups.md). Pour les applications pré-intégrées dans la Galerie, telles que Slack, Azure Databricks et Snowflake, vous pouvez ignorer la documentation du développeur et utiliser les tutoriels fournis dans [Tutoriels pour l’intégration d’applications SaaS à Azure Active Directory](../../active-directory/saas-apps/tutorial-list.md).

## <a name="manual-vs-automatic-provisioning"></a>Provisionnement manuel ou automatique

Les applications de la galerie Azure AD prennent en charge l’un de ces deux modes de provisionnement :

* Provisionnement **manuel** : utilisé lorsqu’il n’existe pas de connecteur de provisionnement Azure AD automatique pour l’application. Les comptes d’utilisateur doivent être créés manuellement. Par exemple, directement dans le portail d’administration de l’application, ou en chargeant une feuille de calcul contenant les informations du compte d’utilisateur. Consultez la documentation fournie par l’application ou contactez le développeur de l’application pour déterminer les mécanismes disponibles.
* **Automatique** : dans le cadre d’un approvisionnement automatique, un connecteur d’approvisionnement Azure AD a été développé pour cette application. Suivez le tutoriel de configuration pour configurer le provisionnement de votre application. Vous trouverez les tutoriels d’applications dans [Liste de tutoriels pour l’intégration d’applications SaaS avec Azure Active Directory](../../active-directory/saas-apps/tutorial-list.md).

Le mode de provisionnement pris en charge par une application est également visible sous l’onglet **Provisionnement** une fois que vous avez ajouté l’application à vos applications d’entreprise.

## <a name="benefits-of-automatic-provisioning"></a>Avantages de l’approvisionnement automatique

À mesure que le nombre d’applications utilisées dans les organisations modernes continue de croître, les administrateurs informatiques sont chargés de la gestion des accès à l’échelle. Des normes telles que SAML ou OIDC permettent aux administrateurs de configurer rapidement l’authentification unique, mais l’accès exige également que les utilisateurs soient provisionnés dans l’application. Pour de nombreux administrateurs, le provisionnement consiste à créer manuellement chaque compte d’utilisateur ou à charger des fichiers CSV chaque semaine. Ces processus sont fastidieux, coûteux et sujets aux erreurs. Des solutions telles que le JIT (Just-in-Time) SAML ont été adoptées pour automatiser le provisionnement. Les entreprises ont également besoin d’une solution pour déprovisionner des utilisateurs lorsqu’ils quittent l’organisation ou n’ont plus besoin d’accéder à certaines applications suite à un changement de rôle.

Voici quelques-unes des motivations courantes de l’utilisation de l’approvisionnement automatique :

- Optimisation de l’efficacité et de la précision des processus d’approvisionnement.
- Économies sur les coûts liés à l’hébergement et à la gestion de scripts et de solutions d’approvisionnement personnalisés.
- Sécurisation de votre organisation en supprimant instantanément les identités des utilisateurs des applications SaaS lorsqu’ils quittent l’organisation.
- Importation facile de nombreux utilisateurs dans un système ou une application SaaS spécifique.
- Disposer d’un ensemble unique de stratégies pour déterminer qui est approvisionné et qui peut se connecter à une application.

L’approvisionnement d’utilisateurs d’Azure AD peut vous aider à relever ces défis. Pour en savoir plus sur la façon dont les clients utilisent le provisionnement d’utilisateurs Azure AD, consultez l’[étude de cas ASOS](https://aka.ms/asoscasestudy). La vidéo suivante offre une vue d’ensemble du provisionnement d’utilisateurs dans Azure AD.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quels applications et systèmes puis-je utiliser avec l’approvisionnement d’utilisateurs automatique d’Azure AD ?

Azure AD offre une prise en charge préintégrée de plusieurs applications SaaS et systèmes de gestion des ressources humaines connus, ainsi qu’une prise en charge générique des applications qui implémentent des parties spécifiques du [standard SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Applications préintégrées (applications SaaS de la Galerie)**  : vous trouverez toutes les applications pour lesquelles Azure AD prend en charge un connecteur de provisionnement pré-intégré dans [Tutoriels pour l’intégration d’applications SaaS avec Azure Active Directory](../saas-apps/tutorial-list.md). Les applications préintégrées répertoriées dans la galerie utilisent généralement des API de gestion des utilisateurs SCIM 2.0 pour l’approvisionnement. 

   ![Image qui montre les logos DropBox, Salesforce et autres.](./media/user-provisioning/gallery-app-logos.png)

   Si vous souhaitez demander une nouvelle application pour l’approvisionnement, vous pouvez [demander que votre application soit intégrée à notre galerie d’applications](../develop/v2-howto-app-gallery-listing.md). Pour une demande d’approvisionnement d’utilisateurs, nous avons besoin que l’application dispose d’un point de terminaison compatible SCIM. Demandez au fournisseur de l’application de suivre la norme SCIM afin que nous puissions intégrer rapidement l’application à notre plateforme.

* **Applications qui prennent en charge SCIM 2.0** : pour plus d’informations sur la connexion générique d’applications qui implémentent des API de gestion des utilisateurs SCIM 2.0, consultez [Créer un point de terminaison SCIM et configurer le provisionnement d’utilisateurs](use-scim-to-provision-users-and-groups.md).

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Comment configurer l’approvisionnement automatique pour une application ?

Pour les applications préintégrées figurant dans la galerie, des instructions pas à pas sont disponibles pour configurer l’approvisionnement automatique. Consultez [Tutoriels pour l’intégration d’applications SaaS avec Azure Active Directory](../saas-apps/tutorial-list.md). La vidéo suivante montre comment configurer l’approvisionnement automatique d’utilisateurs pour SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Pour les autres applications qui prennent en charge SCIM 2.0, suivez les étapes décrites dans [Créer un point de terminaison SCIM et configurer le provisionnement d’utilisateurs](use-scim-to-provision-users-and-groups.md).


## <a name="next-steps"></a>Étapes suivantes

- [Liste des didacticiels sur l’intégration des applications SaaS](../saas-apps/tutorial-list.md)
- [Personnalisation des mappages d’attributs pour le provisionnement des utilisateurs](customize-application-attributes.md)
- [Filtres d’étendue pour le provisionnement des utilisateurs](define-conditional-rules-for-provisioning-user-accounts.md)
