---
title: Connecteur de gestion des services informatiques – Exportation sécurisée dans Azure Monitor
description: Cet article vous montre comment connecter vos produits/services ITSM à l’aide de l’exportation sécurisée dans Azure Monitor, afin de superviser et de gérer de manière centralisée les éléments de travail ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: ee56d65452cb8535c5197e1b3524bd4e9c9ab9ea
ms.sourcegitcommit: 697638c20ceaf51ec4ebd8f929c719c1e630f06f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857407"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Connecter Azure aux outils ITSM en utilisant l’exportation sécurisée

Cet article explique comment configurer la connexion entre votre service ou produit Gestion des services informatiques (ITSM) à l’aide de l’exportation sécurisée.

L’exportation sécurisée est une version actualisée de [Connecteur de gestion des services informatiques (connecteur ITSMC)](./itsmc-overview.md). Les deux versions vous permettent de créer des éléments de travail dans un outil ITSM lorsque Azure Monitor envoie des alertes. La fonctionnalité comprend des alertes de métriques, des alertes de journaux et des alertes du journal d’activité.

ITSMC utilise les informations d’identification de nom d’utilisateur et de mot de passe. L’exportation sécurisée dispose d’une authentification renforcée, car elle utilise Azure Active Directory (Azure AD). Azure AD est le service Microsoft basé sur le cloud qui gère les identités et les accès. Il permet aux utilisateurs de se connecter et d’accéder aux ressources internes ou externes. L’utilisation d’Azure AD avec ITSM donne la possibilité d’identifier les alertes Azure (par l’intermédiaire de l’ID d’application Azure AD) qui ont été envoyées au système externe.

> [!NOTE]
> La capacité de connecter des outils Azure à ITSM en utilisant l’exportation sécurisée est en préversion.

## <a name="secure-export-architecture"></a>Architecture de l’exportation sécurisée

L’architecture de l’exportation sécurisée présente les nouvelles fonctionnalités suivantes :

* **Nouveau groupe d’actions** : les alertes sont envoyées à l’outil ITSM via le groupe d’actions Webhook sécurisé, à la place du groupe d’actions ITSM utilisé par le connecteur ITSMC.
* **Authentification Azure AD** : l’authentification s’effectue via Azure AD, à la place des informations d’identification de nom d’utilisateur/mot de passe.

## <a name="secure-export-data-flow"></a>Flux de données de l’exportation sécurisée

Les étapes du flux de données de l’exportation sécurisée sont les suivantes :

1. Azure Monitor envoie une alerte qui est configurée pour utiliser l’exportation sécurisée.
2. La charge utile de l’alerte est envoyée par une action Webhook sécurisé à l’outil ITSM.
3. L’application ITSM vérifie avec Azure AD si l’alerte est autorisée à entrer dans l’outil ITSM.
4. Si l’alerte est autorisée, l’application :
   
   1. Crée un élément de travail (par exemple, un incident) dans l’outil ITSM.
   2. Lie l’ID de l’élément de configuration (CI) à la base de données de gestion client (CMDB).

![Diagramme montrant comment l’outil ITSM communique avec Azure AD, les alertes Azure et un groupe d’actions.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>Avantages de l’exportation sécurisée

Voici les avantages principaux de l’intégration :

* **Meilleure authentification** : Azure AD fournit une authentification plus sécurisée, sans les délais d’attente qui se produisent couramment dans le connecteur ITSMC.
* **Alertes résolues dans l’outil ITSM** : les alertes de métriques implémentent les états « déclenché » et « résolu ». Lorsque la condition est remplie, l’état de l’alerte est « déclenché ». Lorsque la condition n’est plus remplie, l’état de l’alerte est « résolu ». Dans le connecteur ITSMC, les alertes ne peuvent pas être résolues automatiquement. Avec l’exportation sécurisée, l’état résolu est transmis à l’outil ITSM, et donc mis à jour automatiquement.
* **[Schéma d’alerte courant](./alerts-common-schema.md)**  : dans le connecteur ITSMC, le schéma de la charge utile de l’alerte diffère selon le type d’alerte. Dans l’exportation sécurisée, il existe un schéma commun pour tous les types d’alerte. Ce schéma commun contient l’élément de configuration pour tous les types d’alerte. Tous les types d’alerte peuvent lier leur élément de configuration à la base de données de gestion client.

## <a name="next-steps"></a>Étapes suivantes

* [Créer des éléments de travail ITSM à partir des alertes Azure](./itsmc-overview.md)
