---
title: Configurer la séparation des tâches pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory - Azure Active Directory
description: Découvrez comment configurer la mise en œuvre de la séparation des tâches pour les demandes pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 07/2/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 171901de8f9037ee6e77f83138ec7a3065c2d71f
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730165"
---
# <a name="configure-separation-of-duties-checks-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Configurer des vérifications de séparation des tâches pour un package d’accès dans la gestion des droits d’utilisation d’Azure AD (préversion)

Dans la gestion des droits d’utilisation Azure AD, vous pouvez configurer plusieurs stratégies, avec des paramètres différents pour chaque communauté d’utilisateurs qui aura besoin d’un accès par le biais d’un package d’accès.  Par exemple, les employés peuvent uniquement avoir besoin de l’approbation du responsable pour accéder à certaines applications, mais les invités provenant d’autres organisations peuvent nécessiter l’approbation d’un sponsor et un responsable de service de l’équipe des ressources. Dans une stratégie pour les utilisateurs qui se trouvent déjà dans l’annuaire, vous pouvez spécifier un groupe d’utilisateurs particulier qui peut demander l’accès. Toutefois, il faut peut-être éviter qu’un utilisateur n’obtienne un accès excessif.  Pour répondre à cette exigence, vous souhaiterez peut-être limiter davantage les personnes autorisées à demander l’accès, en fonction de l’accès déjà effectué par le demandeur.

Avec les paramètres de séparation des tâches d’un package d’accès, vous pouvez configurer qu’un utilisateur qui est membre d’un groupe ou qui a déjà une attribution à un package d’accès ne peut pas demander un package d’accès supplémentaire.

![Expérience myaccess pour la tentative de demande d’accès incompatible](./media/entitlement-management-access-package-incompatible/request-prevented.png)


## <a name="scenarios-for-separation-of-duties-checks"></a>Scénarios de séparation des vérifications des tâches

Par exemple, vous disposez d’un package d’accès, *campagne marketing*, que les personnes de votre organisation et d’autres organisations peuvent demander à accéder à pour travailler avec le service marketing de votre organisation pendant que cette campagne se poursuivra. Étant donné que les employés du service marketing doivent avoir déjà accès à ce matériel de campagne marketing, vous ne souhaitez pas que les employés du service marketing demandent l’accès à ce package d’accès.  Ou bien, vous avez peut-être déjà un groupe dynamique, *Employés du service marketing*, qui contient tous les employés du marketing. Vous pouvez indiquer que le package d’accès est incompatible avec l’appartenance de ce groupe dynamique. Ensuite, si un employé du service marketing recherche un package d’accès à demander, il ne peut pas demander l’accès au package d’accès à la *campagne marketing*.

De même, il se peut que vous disposiez d’une application avec deux rôles : **Ventes occidentales** et **Ventes de l’est**, et que vous souhaitiez vous assurer qu’un utilisateur ne peut avoir qu’un seul secteur de vente à la fois.  Si vous disposez de deux packages d’accès, un package d’accès, **Secteur ouest**, qui donne le rôle **Ventes occidentales** et l’autre package d’accès, **Secteur est**, le rôle **Ventes de l’est**, vous pouvez configurer
 - que le package d’accès **Secteur ouest** a le package d’accès **Secteur est** comme étant incompatible et
 - que le package d’accès **Secteur est** a le package d’accès **Secteur ouest** comme étant incompatible.

Si vous avez utilisé Microsoft Identity Manager ou d’autres systèmes de gestion des identités locaux pour automatiser l’accès aux applications locales, vous pouvez également intégrer ces systèmes à la gestion des droits d’utilisation Azure AD.  Si vous souhaitez contrôler l’accès aux applications intégrées à Azure AD par le biais de la gestion des droits d’utilisation et que vous souhaitez empêcher les utilisateurs d’avoir un accès incompatible, vous pouvez configurer qu’un package d’accès est incompatible avec un groupe. Il peut s’agir d’un groupe, que votre système de gestion des identités local envoie dans Azure AD via Azure AD Connect. Cette vérification garantit qu’un utilisateur ne pourra pas demander un package d’accès, si ce package d’accès donne à cet utilisateur un accès incompatible avec l’accès de l’utilisateur aux applications locales.

## <a name="prerequisites"></a>Prérequis

Pour utiliser la gestion des droits d’utilisation Azure AD et affecter des utilisateurs aux packages d’accès, vous devez disposer d’une des licences suivantes :

- Azure AD Premium P2
- Licence Enterprise Mobility + Security (EMS) E5

## <a name="configure-another-access-package-or-group-membership-as-incompatible-for-requesting-access-to-an-access-package"></a>Configurer un autre package d’accès ou une appartenance à un groupe comme incompatible pour demander l’accès à un package d’accès

**Rôle prérequis :** administrateur général, administrateur Identity Governance, administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

Procédez comme suit pour modifier la liste des groupes incompatibles ou d’autres packages d’accès pour un package d’accès existant :

1.  Connectez-vous au [portail Azure](https://portal.azure.com).

1.  Cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1.  Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès que les utilisateurs demanderont.

1.  Dans le menu de gauche, cliquez sur **Séparation des tâches (préversion)** .

1.  Si vous souhaitez empêcher les utilisateurs qui ont déjà une autre affectation de package d’accès à demander ce package d’accès, cliquez sur **Ajouter un package d’accès** et sélectionnez le package d’accès que l’utilisateur est déjà affecté.


    ![Configuration des packages d’accès incompatibles](./media/entitlement-management-access-package-incompatible/select-incompatible-ap.png)


1.  Si vous souhaitez empêcher les utilisateurs qui ont une appartenance à un groupe existante de demander ce package d’accès, cliquez sur **Ajouter un groupe**, puis sélectionnez le groupe auquel l’utilisateur se trouvait déjà.

### <a name="configure-incompatible-access-packages-programmatically"></a>Configurer des packages d’accès incompatibles par programmation

Vous pouvez également configurer les groupes et autres packages d’accès qui sont incompatibles avec le package d’accès à l’aide de Microsoft Graph.  Un utilisateur doté d’un rôle approprié avec une application disposant de l’autorisation déléguée `EntitlementManagement.ReadWrite.All`, ou une application disposant de cette autorisation, peut appeler l’API pour ajouter, supprimer et répertorier les groupes et les packages d’accès incompatibles [d’un package d’accès](/graph/api/resources/accesspackage?view=graph-rest-beta&preserve-view=true).


## <a name="view-other-access-packages-that-are-configured-as-incompatible-with-this-one"></a>Afficher les autres packages d’accès configurés comme incompatibles avec celui-ci

**Rôle prérequis :** administrateur général, administrateur Identity Governance, administrateur d’utilisateurs, propriétaire de catalogue ou gestionnaire de package d’accès

Procédez comme suit pour afficher la liste des autres packages d’accès qui ont indiqué qu’ils ne sont pas compatibles avec un package d’accès existant :

1.  Connectez-vous au [portail Azure](https://portal.azure.com).

1.  Cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1.  Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1.  Dans le menu de gauche, cliquez sur **Séparation des tâches (préversion)** .

1. Cliquez sur **Incompatible avec**.

## <a name="monitor-and-report-on-access-assignments"></a>Surveiller et signaler les affectations d’accès

Vous pouvez utiliser les classeurs Azure Monitor pour obtenir des informations sur la façon dont les utilisateurs reçoivent leur accès.

1. Configurez Azure AD pour [envoyer des événements d’audit à Azure Monitor](entitlement-management-logs-and-reporting.md).

1. Le classeur nommé *Activité du package d’accès* affiche chaque événement lié à un package d’accès particulier.

    ![Visualiser les événements du package d’accès](./media/entitlement-management-logs-and-reporting/view-events-access-package.png)

1. Pour voir si des modifications ont été apportées aux attributions de rôle d’application pour une application qui n’ont pas été créées en raison des affectations de package d’accès, vous pouvez sélectionner le classeur nommé *Activité d’attribution de rôle d’application*.  Si vous choisissez d’omettre l’activité de droit, seules les modifications apportées aux rôles d’application qui n’ont pas été effectuées par la gestion des droits s’affichent. Par exemple, vous verriez une ligne si un administrateur général a directement affecté un utilisateur à un rôle d’application.

    ![Afficher les attributions de rôle d’application](./media/entitlement-management-access-package-incompatible/workbook-ara.png)


## <a name="next-steps"></a>Étapes suivantes

- [Afficher, ajouter et supprimer les attributions pour un package d’accès](entitlement-management-access-package-assignments.md)
- [Afficher les rapports et les journaux](entitlement-management-reports.md)
