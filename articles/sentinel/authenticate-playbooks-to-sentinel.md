---
title: Authentifier les playbooks auprès d’Azure Sentinel | Microsoft Docs
description: Découvrez comment permettre à vos playbooks d’accéder à Azure Sentinel et leur fournir l’autorisation de prendre des mesures correctives.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.openlocfilehash: 5fbe518e894cf6b1dad1407edcc241dc141ef546
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284174"
---
# <a name="authenticate-playbooks-to-azure-sentinel"></a>Authentifier les playbooks auprès d’Azure Sentinel

De la façon dont Logic Apps fonctionne, il doit se connecter séparément et s’authentifier indépendamment auprès de chaque ressource de chaque type avec lequel il interagit, dont Azure Sentinel lui-même. Logic Apps utilise des [connecteurs spécialisés](/connectors/connector-reference/) à cet effet, chaque type de ressource ayant son propre connecteur. Ce document décrit les types de connexion et d’authentification dans le [connecteur Logic Apps Azure Sentinel](/connectors/azuresentinel/), que les playbooks peuvent utiliser pour interagir avec Azure Sentinel afin d’accéder aux informations contenues dans les tables de votre espace de travail.

Ce document, de même que notre guide d’[utilisation des déclencheurs et des actions dans les playbooks](playbook-triggers-actions.md), vient compléter notre autre documentation sur les playbooks, [Tutoriel : Utiliser des playbooks avec des règles d’automatisation dans Azure Sentinel](tutorial-respond-threats-playbook.md).

Pour obtenir une présentation des playbooks, consultez [Automatiser la réponse aux menaces à l’aide de playbooks dans Azure Sentinel](automate-responses-with-playbooks.md).

Pour connaître la spécification complète du connecteur Azure Sentinel, consultez la [documentation du connecteur Logic Apps](/connectors/azuresentinel/).

## <a name="authentication"></a>Authentication

Le connecteur Azure Sentinel dans Logic Apps, ainsi que les déclencheurs et actions de ses composants, peuvent fonctionner pour le compte d’une identité disposant des autorisations nécessaires (lecture et/ou écriture) dans l’espace de travail approprié. Le connecteur prend en charge plusieurs types d’identités :

- [Identité managée (préversion)](#authenticate-with-managed-identity)
- [Utilisateur Azure AD](#authenticate-as-an-azure-ad-user)
- [Principal de service (application Azure AD)](#authenticate-as-a-service-principal-azure-ad-application)

    ![Options d’authentification](media/authenticate-playbooks-to-sentinel/auth-methods.png)

### <a name="permissions-required"></a>Autorisations requises

| Rôles/Composants du connecteur | Déclencheurs | Actions « Get » | Mettre à jour l’incident,<br>ajouter un commentaire |
| ------------- | :-----------: | :------------: | :-----------: |
| **[Lecteur Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)** | &#10003; | &#10003; | &#10007; |
| **[Répondeur](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)/[Contributeur](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) Azure Sentinel** | &#10003; | &#10003; | &#10003; |
| 

[En savoir plus sur les autorisations dans Azure Sentinel.](./roles.md)

### <a name="authenticate-with-managed-identity"></a>Authentifier avec l’identité gérée

Cette méthode d’authentification vous permet de fournir des autorisations directement au playbook (ressource de workflow d’application logique), afin que les actions du connecteur Azure Sentinel prises par le playbook fonctionnent au nom du playbook, comme s’il s’agissait d’un objet indépendant avec ses propres autorisations sur Azure Sentinel. L’utilisation de cette méthode réduit le nombre d’identités que vous devez gérer. 

Pour s’authentifier avec une identité managée :

1. [Activez l’identité managée](../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal) sur la ressource de workflow Logic Apps. Pour récapituler :

    - Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Identité**. Sélectionnez **Attribuée par le système > Activée > Enregistrer**. Quand Azure vous invite à confirmer l’opération, sélectionnez **Oui**.

    - Votre application logique peut désormais utiliser l’identité affectée par le système qui est inscrite auprès d’Azure AD et représentée par un ID d’objet.

1. [Accordez à cette identité l’accès](../logic-apps/create-managed-service-identity.md#give-identity-access-to-resources) à l’espace de travail Azure Sentinel en lui attribuant le rôle [Contributeur Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor).

    En savoir plus sur les [rôles disponibles dans Azure Sentinel](./roles.md).

1. Activez la méthode d’authentification d’identité managée dans le connecteur Logic Apps Azure Sentinel :

    1. Dans le Concepteur Logic Apps, ajoutez une étape de connecteur Logic Apps Azure Sentinel. Si le connecteur est déjà activé pour une connexion existante, cliquez sur le lien **Modifier la connexion**.

        ![Modifier la connexion](media/authenticate-playbooks-to-sentinel/change-connection.png)

    1. Dans la liste de connexions qui en résulte, sélectionnez **Ajouter nouveau** en bas. 

    1. Créez une connexion en sélectionnant **Se connecter avec une identité managée (préversion)** .

        ![Option d’identité managée](media/authenticate-playbooks-to-sentinel/auth-methods-msi-choice.png)

    1. Entrez un nom pour cette connexion, sélectionnez **Identité managée affectée par le système**, puis **Créer**.

        ![Se connecter avec une identité managée](media/authenticate-playbooks-to-sentinel/auth-methods-msi.png)

### <a name="authenticate-as-an-azure-ad-user"></a>S’authentifier en tant qu’utilisateur Azure AD

Pour établir une connexion, sélectionnez **Se connecter**. Vous serez invité à fournir des informations sur votre compte. Une fois que vous avez effectué cette opération, suivez les instructions restantes à l’écran pour créer une connexion.

### <a name="authenticate-as-a-service-principal-azure-ad-application"></a>S’authentifier en tant que principal de service (application Azure AD)

Les principaux de service peuvent être créés en inscrivant une application Azure AD. Il est **préférable** d’utiliser une application inscrite comme identité du connecteur, au lieu d’utiliser un compte d’utilisateur, car vous serez mieux en mesure de contrôler les autorisations, de gérer les informations d’identification et d’activer certaines limitations sur l’utilisation du connecteur.

Pour utiliser votre propre application avec le connecteur Azure Sentinel, procédez comme suit :

1. Inscrivez l’application auprès d’Azure AD et créez un principal de service. [Découvrez comment](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).

1. Obtenez les informations d’identification (pour une authentification ultérieure).

    Dans le panneau de l’application inscrite, récupérez les informations d’identification de l’application pour la connexion :

    - **ID client** : sous **Vue d’ensemble**
    - **Secret client** : sous **Certificats et secrets**.

1. Accordez des autorisations à l’espace de travail Azure Sentinel.

    Dans cette étape, l’application obtient l’autorisation d’utiliser l’espace de travail Azure Sentinel.

    1. Dans l’espace de travail Azure Sentinel, accédez à **Paramètres** -> **Paramètres d’espace de travail** -> **Contrôle d’accès (IAM)** .

    1. Sélectionnez **Ajouter une attribution de rôle**.

    1. Sélectionnez le rôle que vous souhaitez affecter à l’application. Par exemple, pour permettre à l’application d’effectuer des actions qui apportent des modifications à l’espace de travail Sentinel, comme la mise à jour d’un incident, sélectionnez le rôle **Contributeur Azure Sentinel**. Pour les actions qui lisent uniquement les données, le rôle **Lecteur Azure Sentinel** est suffisant. [En savoir plus sur les rôles disponibles dans Azure Sentinel](./roles.md).

    1. Recherchez l’application requise et enregistrez-la. Par défaut, les applications Azure AD ne figurent pas dans les options disponibles. Pour trouver votre application, recherchez-la par son nom et sélectionnez-la.

1. Authenticate

    Dans cette étape, nous utilisons les informations d’identification de l’application pour une authentification auprès du connecteur Sentinel dans Logic Apps.

    - Sélectionnez **Se connecter avec le principal de service**.

        ![Option du principal de service](media/authenticate-playbooks-to-sentinel/auth-methods-spn-choice.png)

    - Renseignez les paramètres obligatoires (qui se trouvent dans le panneau de l’application inscrite)
        - **Locataire** : sous **Vue d’ensemble**
        - **ID client** : sous **Vue d’ensemble**
        - **Secret client** : sous **Certificats et secrets**
        
        ![Se connecter avec le principal de service](media/authenticate-playbooks-to-sentinel/auth-methods-spn.png)

### <a name="manage-your-api-connections"></a>Gérer vos connexions d’API

Chaque fois qu’une authentification est créée pour la première fois, une nouvelle ressource Azure de type Connexion d’API est créée. La même connexion d’API peut être utilisée dans tous les déclencheurs et actions Azure Sentinel dans le même groupe de ressources.

Toutes les connexions d’API se trouvent dans le panneau **Connexions d’API** (recherchez *Connexions d’API* dans le portail Azure).

Vous pouvez également les trouver en accédant au panneau **Ressources** et en filtrant l’affichage par type *Connexion d’API*. Vous pouvez ainsi sélectionner plusieurs connexions pour les opérations en bloc.

Pour modifier l’autorisation d’une connexion existante, entrez la ressource de connexion, puis sélectionnez **Modifier la connexion d’API**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris les différentes méthodes d’authentification d’un playbook basé sur Logic Apps auprès d’Azure Sentinel.
- Apprenez-en davantage sur l’[utilisation des déclencheurs et des actions dans les playbooks](playbook-triggers-actions.md).