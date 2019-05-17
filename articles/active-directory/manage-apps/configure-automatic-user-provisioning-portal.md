---
title: Gestion de l’approvisionnement d’utilisateurs pour les applications d’entreprise dans Azure Active Directory | Microsoft Docs
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
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7319a108cb52c603396f8c654697e16438fa7d6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780978"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise dans le portail Azure

Cet article explique comment utiliser le [portail Azure](https://portal.azure.com) pour gérer l’approvisionnement et l’annulation de l’approvisionnement automatiques de comptes d’utilisateur pour les applications qui les prennent en charge. Pour en savoir plus sur l’approvisionnement automatique de comptes d’utilisateur, consultez [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Recherche de vos applications dans le portail

Utilisez le portail Azure Active Directory pour afficher et gérer toutes les applications qui sont configurées pour l’authentification unique dans un répertoire. Les applications d’entreprise sont des applications qui sont déployées et utilisées au sein de votre organisation. Suivez ces étapes pour afficher et gérer vos applications d’entreprise :

1. Ouvrez le [portail Azure Active Directory](https://aad.portal.azure.com).

1. Sélectionnez **applications d’entreprise** dans le volet gauche. Une liste de toutes les applications configurées est indiquée, y compris les applications qui ont été ajoutées à partir de la galerie.

1. Sélectionnez n’importe quelle application pour charger son volet de ressources, où vous pouvez afficher des rapports et gérer les paramètres de l’application.

1. Sélectionnez **approvisionnement** pour gérer les paramètres de l’application sélectionnée d’approvisionnement de comptes utilisateur.

   ![Volet de ressources d’application](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Modes d’approvisionnement

Le **approvisionnement** volet commence par un **Mode** menu, qui montre les modes d’approvisionnement pris en charge pour une application d’entreprise et vous permet de les configurer. Les options disponibles incluent :

* **Automatique** -cette option est affichée si Azure AD prend en charge l’approvisionnement automatique basée sur les API ou l’annulation de l’approvisionnement de comptes d’utilisateurs à cette application. Sélectionnez ce mode pour afficher une interface qui permet aux administrateurs :

  * Configurer Azure AD pour se connecter à la gestion des utilisateurs de l’application API
  * Créer des mappages de compte et les workflows qui définissent le flux de données de compte d’utilisateur entre Azure AD et l’application
  * Gérer le service d’approvisionnement AD Azure

* **Manuel** -cette option est affichée si Azure AD ne prend en charge l’approvisionnement automatique de comptes d’utilisateurs à cette application. Dans ce cas, les enregistrements stockés dans l’application compte d’utilisateur doit être géré à l’aide d’un processus externe, basé sur les fonctionnalités de gestion et le provisionnement utilisateur fournies par l’application (qui peut inclure l’approvisionnement juste à temps SAML).

## <a name="configuring-automatic-user-account-provisioning"></a>Configuration de l’approvisionnement automatique de comptes d’utilisateur

Sélectionnez le **automatique** option pour spécifier les paramètres pour les informations d’identification administrateur, mappages, démarrage et l’arrêt et la synchronisation.

### <a name="admin-credentials"></a>Informations d’identification de l’administrateur

Développez **informations d’identification administrateur** à entrer les informations d’identification requises pour Azure AD pour se connecter à l’API de gestion de l’application utilisateur. Les entrées requises dépendent de l’application. Pour en savoir plus sur les types d’informations d’identification requis pour une application spécifique, consultez le [didacticiel de configuration de cette application](user-provisioning.md).

Sélectionnez **tester la connexion** pour tester les informations d’identification en demandant à Azure AD tente de se connecter à l’application d’approvisionnement d’application en utilisant les informations d’identification fournies.

### <a name="mappings"></a>Mappages

Développez **mappages** pour afficher et modifier les attributs utilisateur qui circulent entre Azure AD et l’application cible lorsque les comptes d’utilisateur sont configurés ou mis à jour.

Il existe un ensemble préconfiguré de mappages entre les objets utilisateur Azure AD et les objets d’utilisateur de chaque application SaaS. Certaines applications gèrent d’autres types d’objets, tels que des groupes ou des contacts. Sélectionnez un mappage dans la table pour ouvrir l’éditeur de mappage vers la droite, où vous pouvez afficher et les personnaliser.

![Volet de ressources d’application](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Les personnalisations prises en charge sont notamment les suivantes :

* L’activation et la désactivation des mappages d’objets spécifiques, par exemple entre l’objet utilisateur Azure AD et l’objet utilisateur de l’application SaaS.
* Modification des attributs qui circulent entre l’objet utilisateur Azure AD et l’objet utilisateur de l’application. Pour plus d’informations sur le mappage d’attributs, voir [Présentation des types de mappages d’attributs](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrage des actions d’approvisionnement Azure AD s’exécute sur l’application cible. Au lieu d’avoir à Azure AD de synchroniser des objets, vous pouvez limiter les actions à exécuter. 

  Par exemple, sélectionnez uniquement **mise à jour** et Azure AD uniquement les mises à jour utilisateur existant de comptes dans une application, mais ne crée pas de nouveaux. Sélectionnez uniquement **créer** et Azure seulement crée de nouveaux comptes d’utilisateur, mais ne met pas à jour existantes. Cette fonctionnalité permet aux administrateurs de créer des mappages différents pour la création du compte et de mettre à jour de flux de travail.

* Ajout d’un nouveau mappage d’attribut. Sélectionnez **ajouter un nouveau mappage** en bas de la **mappage d’attributs** volet. Remplissez le **modifier l’attribut** forment et sélectionnez **Ok** pour ajouter un nouveau mappage à la liste. 

### <a name="settings"></a>Paramètres

Vous pouvez démarrer et arrêter l’approvisionnement de service pour l’application sélectionnée dans Azure AD le **paramètres** zone de la **approvisionnement** écran. Vous pouvez également choisir effacer le cache d’approvisionnement et de redémarrer le service.

Si c’est la première fois que l’approvisionnement est activé pour une application, activez le service en définissant le paramètre **État de la configuration** sur **Activé**. Cette modification entraîne l’approvisionnement Azure AD service exécuter une synchronisation initiale. Il lit les utilisateurs affectés dans le **utilisateurs et groupes** section, interroge l’application cible pour eux, puis exécute les actions d’approvisionnement définies dans Azure AD **mappages** section. Pendant ce processus, le service d’approvisionnement stocke les données mises en cache sur les comptes d’utilisateur qu’il gère, donc les comptes non gérés à l’intérieur d’applications cibles qui n’ont jamais étaient étendue pour l’attribution ne sont pas affectés par la mise hors service des opérations. Après la synchronisation initiale, le service d’approvisionnement synchronise automatiquement les objets utilisateur et groupe toutes les dix minutes.

Modifier le **état d’approvisionnement** à **hors** pour suspendre le service d’approvisionnement. Dans cet état, Azure ne créer, mettre à jour ou supprimer des objets utilisateur ou groupe dans l’application. Revenez à l’état **sur** et le service reprend là où il était arrêté.

Sélectionnez le **effacer l’état en cours et redémarrer la synchronisation** case à cocher et sélectionnez **enregistrer** à :

* Arrêter le service d’approvisionnement
* Vider les données mises en cache sur les comptes de gestion d’Azure AD
* Redémarrez les services et l’exécuter à nouveau la synchronisation initiale

Cette option permet aux administrateurs de recommencer le processus de déploiement de configuration à nouveau.

### <a name="synchronization-details"></a>Détails de la synchronisation

Cette section fournit des détails supplémentaires sur l’opération de service de provisionnement, y compris les heures de prénom et que le service d’approvisionnement exécuté sur l’application, et comment de nombreux objets utilisateur et groupe qu’il gère.

Un lien est fourni à la **rapports d’activité d’approvisionnement**, qui fournit un enregistrement de tous les utilisateurs et groupes créé, mis à jour et supprimé entre Azure AD et l’application cible. Un lien est également fourni pour le **mise en service de rapport d’erreurs**, qui fournit plus de messages d’erreur pour les objets utilisateur et groupe qui n’a pas pu être lue, créé, mis à jour ou supprimé.
