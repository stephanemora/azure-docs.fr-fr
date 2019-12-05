---
title: Gestion du provisionnement des utilisateurs pour les applications d’entreprise dans Azure AD
description: Découvrez comment gérer l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise à l’aide d’Azure Active Directory
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
ms.date: 04/01/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67b92c32d511300a0645b707f2a263b463937d1b
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558573"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise dans le portail Azure

Cet article explique comment utiliser le [portail Azure](https://portal.azure.com) pour gérer l’approvisionnement et l’annulation de l’approvisionnement automatiques de comptes d’utilisateur pour les applications qui les prennent en charge. Pour en savoir plus sur l’approvisionnement automatique de comptes d’utilisateur, consultez [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Recherche de vos applications dans le portail

Utilisez le portail Azure Active Directory pour afficher et gérer toutes les applications d’entreprise qui sont configurées pour l’authentification unique. Les applications d’entreprise sont des applications qui sont déployées et utilisées au sein de votre organisation. Suivez ces étapes pour afficher et gérer vos applications d’entreprise :

1. Ouvrez le [portail Azure Active Directory](https://aad.portal.azure.com).
1. Dans le volet gauche, sélectionnez **Applications d’entreprise**. Une liste de toutes les applications configurées s’affiche, dont les applications qui ont été ajoutées à partir de la galerie.
1. Sélectionnez n’importe quelle application pour charger son panneau de ressources, où vous pouvez afficher les rapports et gérer les paramètres de l’application.
1. Sélectionnez **Approvisionnement** pour gérer les paramètres d’approvisionnement du compte utilisateur de l’application sélectionnée.

   ![Écran Approvisionnement pour gérer les paramètres d’approvisionnement du compte d'utilisateur](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modes d’approvisionnement

Le volet **Approvisionnement** commence par un menu **Mode**, qui indique les modes d’approvisionnement pris en charge pour une application d’entreprise et permet de les configurer. Les options disponibles incluent :

* **Automatique** : cette option est affichée si Azure AD prend en charge l’approvisionnement ou l’annulation de l’approvisionnement automatiques basés sur une API de comptes d’utilisateur pour cette application. Sélectionnez ce mode pour afficher une interface qui aide les administrateurs :

  * Configurer Azure AD pour se connecter à l’API de gestion de l’application de l’utilisateur
  * Créer des mappages de compte et des flux de travail qui définissent le flux des données d’un compte d’utilisateur entre Azure AD et l’application
  * Gérer le service d’approvisionnement Azure AD

* **Manuel** : cette option est affichée si Azure AD ne prend pas en charge l’approvisionnement automatique de comptes d’utilisateur pour cette application. Dans ce cas, les enregistrements de comptes d’utilisateur stockés dans l’application doivent être gérés à l’aide d’un processus externe qui dépend des fonctionnalités de gestion et d’approvisionnement des utilisateurs fournies par cette application (par exemple l’approvisionnement SAML juste-à-temps).

## <a name="configuring-automatic-user-account-provisioning"></a>Configuration de l’approvisionnement automatique de comptes d’utilisateur

Sélectionnez l’option **Automatique** pour spécifier les paramètres des informations d’identification administrateur, des mappages, des arrêts et des démarrages et de la synchronisation.

### <a name="admin-credentials"></a>Admin Credentials (Informations d’identification de l’administrateur)

Développez **Informations d’identification administrateur** pour saisir les informations d’identification requises par Azure AD pour se connecter à l’API de gestion des utilisateurs de l’application. Les entrées requises dépendent de l’application. Pour en savoir plus sur les types d’informations d’identification requis pour une application spécifique, consultez le [didacticiel de configuration de cette application](user-provisioning.md).

Sélectionnez **Tester la connexion** pour tester les informations d’identification. Azure AD essaie alors de se connecter à l’API de configuration de l’application à l’aide des informations d’identification fournies.

### <a name="mappings"></a>Mappages

Développez **Mappages** pour afficher et modifier les attributs utilisateur qui circulent entre Azure AD et l’application cible lorsque des comptes d’utilisateur sont configurés ou mis à jour.

Il existe un ensemble préconfiguré de mappages entre les objets utilisateur Azure AD et les objets utilisateur de chaque application SaaS. Certaines applications gèrent d’autres types d’objets, tels que des groupes ou des contacts. Sélectionnez un mappage dans la table pour ouvrir l’éditeur de mappage sur la droite, où vous pouvez les afficher et les personnaliser.

![Affiche l'écran Mappage d’attributs](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Les personnalisations prises en charge sont notamment les suivantes :

* L’activation et la désactivation des mappages d’objets spécifiques, par exemple entre l’objet utilisateur Azure AD et l’objet utilisateur de l’application SaaS.
* Modification des attributs qui circulent entre l’objet utilisateur Azure AD et l’objet utilisateur de l’application. Pour plus d’informations sur le mappage d’attributs, voir [Présentation des types de mappages d’attributs](customize-application-attributes.md#understanding-attribute-mapping-types).
* Le filtrage des actions d’approvisionnement qu’effectue Azure AD sur l’application ciblée. Plutôt qu’Azure AD synchronise entièrement les objets, vous pouvez limiter les actions exécutées.

  Par exemple, si vous sélectionnez uniquement **Mettre à jour**, Azure AD met à jour les comptes d’utilisateur d’une application et n’en crée pas de nouveaux. Si vous sélectionnez uniquement **Créer**, Azure crée des comptes d’utilisateur, mais ne met pas à jour les comptes existants. Cette fonctionnalité permet aux administrateurs de créer des mappages différents pour les flux de travail de création et de mise à jour de comptes.

* Ajout d’un nouveau mappage d’attribut. Sélectionnez **Ajouer un nouveau mappage** en bas du volet **Mappage d’attribut**. Remplissez le formulaire **Modifier l’attribut** et sélectionnez **OK** pour ajouter le nouveau mappage à la liste.

### <a name="settings"></a>Paramètres

Vous pouvez démarrer et arrêter le service d’approvisionnement d’Azure AD pour l’application sélectionnée dans la zone **Paramètres** de l’écran **Approvisionnement**. Vous pouvez aussi choisir de vider le cache d’approvisionnement et de redémarrer le service.

Si c’est la première fois que l’approvisionnement est activé pour une application, activez le service en définissant le paramètre **État de la configuration** sur **Activé**. Avec cette modification, le service de provisionnement d’Azure AD doit exécuter un cycle initial. Il lit les utilisateurs affectés dans la section **Utilisateurs et groupes**, interroge l’application cible à leur sujet, puis effectue les actions d’approvisionnement définies dans la section **Mappages** d’Azure AD. Au cours de ce processus, le service d’approvisionnement stocke des données en cache sur les comptes d’utilisateur qu’il gère, de sorte que les opérations d’annulation de l’approvisionnement n’ont aucun effet sur les comptes non gérés des applications cibles qui n’ont jamais été concernés par l’affectation. Après le cycle initial, le service de provisionnement synchronise automatiquement les objets utilisateur et groupe toutes les 40 minutes.

Changez **État de la configuration** pour **Désactivé** pour suspendre le service d’approvisionnement. Dans cet état, Azure ne crée, met à jour ou supprime aucun objet utilisateur ou groupe dans l’application. Changez de nouveau l’état pour **Activé** et le service reprend où il s’était arrêté.

**Effacer l’état en cours et redémarrer la synchronisation** déclenche un cycle initial. Le service réévalue ensuite tous les utilisateurs du système source et détermine s’ils sont dans l’étendue de provisionnement. Cela peut être utile quand votre application est placée sous contrôle, ou que vous devez apporter un changement à vos mappages d’attributs. Notez que le cycle initial met plus de temps à s’effectuer que le cycle incrémentiel classique en raison du nombre d’objets à évaluer. Pour en savoir plus sur les performances des cycles initiaux et incrémentiels, cliquez [ici](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user). 
