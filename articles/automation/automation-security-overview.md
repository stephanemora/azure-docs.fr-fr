---
title: Vue d’ensemble de l’authentification de compte Azure Automation
description: Cet article fournit une vue d’ensemble de l’authentification de compte Azure Automation.
keywords: sécurité de l’automatisation ; automation sécurisée ; authentification d’automatisation
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 61aec3936ffef488b989f54894568d206c759b12
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87014981"
---
# <a name="automation-account-authentication-overview"></a>Vue d’ensemble de l’authentification des comptes Automation

Azure Automation vous permet d’automatiser des tâches sur des ressources dans Azure, en local et avec d’autres fournisseurs de cloud comme Amazon Web Services (AWS). Vous pouvez utiliser des runbooks pour automatiser vos tâches, ou un Runbook Worker hybride si vous avez des tâches non Azure à gérer. Chaque environnement nécessite des autorisations pour accéder en toute sécurité aux ressources avec les droits minimaux requis dans l’abonnement Azure.

Cet article couvre différents scénarios d’authentification pris en charge par le service Azure Automation, et montre comment prendre celui-ci en main en fonction des environnements que vous devez gérer.

## <a name="automation-account"></a>Compte Automation 

Lorsque vous démarrez Azure Automation pour la première fois, vous devez créer au moins un compte Automation. Les comptes Automation vous permettent d’isoler vos ressources Automation (runbooks, ressources, configurations) des ressources d’autres comptes. Vous pouvez utiliser des comptes Automation pour séparer les ressources dans des environnements logiques distincts. Par exemple, vous pouvez utiliser un compte pour le développement, un autre pour la production et un autre pour l’environnement local. Un compte Azure Automation est différent de votre compte Microsoft ou des comptes créés dans votre abonnement Azure. Pour obtenir une présentation de la création d’un compte Automation, consultez [Créer un compte Automation](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Ressources Automation

Les ressources Automation de chaque compte Automation sont associées à une seule région Azure, mais chaque compte peut gérer toutes les ressources dans votre abonnement Azure. L’existence de stratégies qui requièrent l’isolation des données et des ressources dans une région spécifique constitue la raison principale de création de comptes Automation dans différentes régions.

Toutes les tâches que vous créez sur les ressources à l’aide d’Azure Resource Manager et des cmdlets PowerShell dans Azure Automation doivent s’authentifier sur Azure à l’aide de l’authentification basée sur les informations d’identification d’organisation Azure Active Directory (Azure AD). 

## <a name="run-as-account"></a>compte d'identification

Les comptes d’identification dans Azure Automation fournissent une authentification permettant de gérer des ressources Azure au moyen de cmdlets PowerShell. Quand vous créez un compte d’identification, un nouvel utilisateur du principal du service dans Azure AD est créé, auquel est attribué le rôle de contributeur au niveau de l’abonnement. Pour les runbooks qui utilisent des Runbook Workers hybrides sur des machines virtuelles Azure, vous pouvez utiliser [l’authentification des runbooks avec les identités managées](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) plutôt que des comptes d’identification pour l’authentification auprès de vos ressources Azure.

## <a name="service-principal-for-run-as-account"></a>Principal du service pour le compte d’identification

Le principal du service d’un compte d’identification ne dispose pas d’autorisations pour lire Azure AD par défaut. Si vous souhaitez ajouter des autorisations pour lire ou gérer Azure AD, vous devez accorder ces autorisations sur le principal du service, sous **Autorisations des API**. Pour en savoir plus, consultez [Ajouter des autorisations pour accéder aux API web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Le contrôle d’accès en fonction du rôle est disponible avec Azure Resource Manager pour attribuer des actions autorisées à un compte d’utilisateur Azure AD et à un compte d’identification, et pour authentifier le principal du service. Pour obtenir plus d’informations susceptibles de vous aider à développer votre modèle de gestion des autorisations Automation, consultez l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).  

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Authentification des runbooks avec Runbook Worker hybride 

Les runbooks exécutés sur un Runbook Worker hybride dans votre centre de données ou sur des services informatiques dans d’autres environnements cloud tels qu’AWS ne peuvent pas utiliser la même méthode que celle généralement utilisée pour l’authentification des runbooks auprès des ressources Azure. Cela est dû au fait que ces ressources sont en cours d’exécution en dehors d’Azure et, par conséquent, elles nécessitent leurs propres informations d’identification de sécurité définies dans Automation pour s’authentifier auprès des ressources auxquelles elles accèdent localement. Pour plus d’informations sur l’authentification des runbooks auprès des Runbook Workers, consultez [Exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>Étapes suivantes

* Pour créer un compte Automation à partir du portail Azure, consultez [Créer un compte Azure Automation autonome](automation-create-standalone-account.md).
* Si vous préférez créer votre compte à l’aide d’un modèle, consultez [Créer un compte Automation à l’aide d’un modèle Azure Resource Manager](quickstart-create-automation-account-template.md).
* Pour l’authentification à l’aide de Amazon Web Services, consultez [Authentifier des runbooks avec Amazon Web Services](automation-config-aws-account.md).