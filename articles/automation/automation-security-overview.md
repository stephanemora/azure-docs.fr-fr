---
title: Présentation de l’authentification dans Azure Automation
description: Cet article présente une vue d’ensemble de la sécurité de l’automatisation et des différentes méthodes d’authentification disponibles pour les comptes Automation dans Azure Automation.
keywords: sécurité de l’automatisation ; automation sécurisée ; authentification d’automatisation
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 3d3dbaad18f6acbe1ddf17d81f54e4232c838dd7
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787411"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Présentation de l’authentification dans Azure Automation

Azure Automation vous permet d’automatiser des tâches sur des ressources dans Azure, en local et avec d’autres fournisseurs de cloud comme Amazon Web Services (AWS). Pour qu’un Runbook exécute les actions requises, il doit avoir les autorisations nécessaires pour accéder en toute sécurité aux ressources avec les droits minimaux requis dans l’abonnement.

Cet article couvre les différents scénarios d’authentification pris en charge par le service Azure Automation, et montre comment prendre celui-ci en main en fonction des environnements que vous devez gérer.  

## <a name="automation-account-overview"></a>Présentation du compte Automation

Lorsque vous démarrez Azure Automation pour la première fois, vous devez créer au moins un compte Automation. Les comptes Automation vous permettent d’isoler vos ressources Automation (Runbooks, ressources, configurations) des ressources contenues dans d’autres comptes Automation. Vous pouvez utiliser des comptes Automation pour séparer les ressources dans des environnements logiques distincts. Par exemple, vous pouvez utiliser un compte pour le développement, un autre pour la production et un autre pour l’environnement local. Un compte Azure Automation est différent de votre compte Microsoft ou des comptes créés dans votre abonnement Azure.

Les ressources Automation de chaque compte Automation sont associées à une seule région Azure, mais les comptes Automation peuvent gérer toutes les ressources dans votre abonnement. L’existence de stratégies qui requièrent l’isolation des données et des ressources dans une région spécifique constitue la raison principale de création de comptes Automation dans différentes régions.

Toutes les tâches que vous effectuez sur les ressources à l’aide d’Azure Resource Manager et des applets de commande Azure dans Azure Automation doivent s’authentifier sur Azure à l’aide de l’authentification basée sur les informations d’identification d’organisation Azure Active Directory. Les comptes d’identification dans Azure Automation fournissent une authentification permettant de gérer des ressources dans Azure au moyen des applets de commande Azure. Quand vous créez un compte d’identification, un nouvel utilisateur du principal du service dans Azure Active Directory (AD) est créé, auquel est attribué le rôle de contributeur au niveau de l’abonnement. Pour les runbooks qui utilisent des runbooks Workers hybrides sur des machines virtuelles Azure, vous pouvez utiliser [l’authentification des runbooks avec les identités managées](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) plutôt que des comptes d’identification pour s’authentifier auprès de vos ressources Azure.

Le principal du service d’un compte d’identification ne dispose pas d’autorisations pour lire Azure AD par défaut. Si vous souhaitez ajouter des autorisations pour lire ou gérer Azure AD, vous devrez accorder ces autorisations sur le principal du service, sous **Autorisations des API**. Pour en savoir plus, consultez [Ajouter des autorisations pour accéder aux API web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

Le contrôle d’accès en fonction du rôle est disponible avec Azure Resource Manager pour attribuer des actions autorisées à un compte d’utilisateur Azure AD et à un compte d’identification, et pour authentifier ce principal du service. Pour obtenir plus d’informations susceptibles de vous aider à développer votre modèle de gestion des autorisations Automation, consultez l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).  

Les runbooks exécutés sur un Runbook Worker hybride dans votre centre de données ou sur des services informatiques dans d’autres environnements cloud tels qu’AWS ne peuvent pas utiliser la même méthode que celle généralement utilisée pour l’authentification des runbooks auprès des ressources Azure. Cela est dû au fait que ces ressources sont en cours d’exécution en dehors d’Azure et, par conséquent, elles nécessitent leurs propres informations d’identification de sécurité définies dans Automation pour s’authentifier auprès des ressources auxquelles elles accèdent localement. Pour plus d’informations sur l’authentification des runbooks auprès des Runbook Workers, consultez [Authentifier des Runbooks pour des Runbook Workers hybrides](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>Étapes suivantes

* [Créer un compte Automation à partir du portail Azure](automation-create-standalone-account.md).

* [Créer un compte Automation à l’aide du modèle Azure Resource Manager](automation-create-account-template.md).

* [Authentifier avec Amazon Web Services (AWS)](automation-config-aws-account.md).
