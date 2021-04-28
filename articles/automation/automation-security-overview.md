---
title: Vue d’ensemble de l’authentification de compte Azure Automation
description: Cet article fournit une vue d’ensemble de l’authentification de compte Azure Automation.
keywords: sécurité de l’automatisation ; automation sécurisée ; authentification d’automatisation
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 546407ce7286cebc04d3c86422f6242051d1dbf3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830427"
---
# <a name="azure-automation-account-authentication-overview"></a>Vue d’ensemble de l’authentification de compte Azure Automation

Azure Automation vous permet d’automatiser des tâches sur des ressources dans Azure, en local et avec d’autres fournisseurs de cloud comme Amazon Web Services (AWS). Vous pouvez utiliser des runbooks pour automatiser vos tâches, ou un Runbook Worker hybride si vous avez des processus métier ou opérationnels à gérer en dehors d’Azure. Travailler dans l’un de ces environnements nécessite des autorisations pour accéder de manière sécurisée aux ressources avec les droits minimaux requis.

Cet article couvre différents scénarios d’authentification pris en charge par le service Azure Automation, et montre comment prendre celui-ci en main en fonction des environnements que vous devez gérer.

## <a name="automation-account"></a>Compte Automation

Lorsque vous démarrez Azure Automation pour la première fois, vous devez créer au moins un compte Automation. Les comptes Automation vous permettent d’isoler vos ressources, runbooks, et configurations Automation des ressources d’autres comptes. Vous pouvez utiliser des comptes Automation pour séparer les ressources dans des environnements logiques distincts ou dans des responsabilités déléguées. Par exemple, vous pouvez utiliser un compte pour le développement, un autre pour la production et un autre pour l’environnement local. Vous pouvez également dédier un compte Automation à la gestion des mises à jour du système d’exploitation sur l’ensemble de vos ordinateurs grâce à [Update Management](update-management/overview.md). 

Un compte Azure Automation est différent de votre compte Microsoft ou des comptes créés dans votre abonnement Azure. Pour obtenir une présentation de la création d’un compte Automation, consultez [Créer un compte Automation](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Ressources Automation

Les ressources Automation de chaque compte Automation sont associées à une seule région Azure, mais chaque compte peut gérer toutes les ressources dans votre abonnement Azure. L’existence de stratégies qui requièrent l’isolation des données et des ressources dans une région spécifique constitue la raison principale de création de comptes Automation dans différentes régions.

Toutes les tâches que vous créez sur les ressources à l’aide d’Azure Resource Manager et des cmdlets PowerShell dans Azure Automation doivent s’authentifier sur Azure à l’aide de l’authentification basée sur les informations d’identification d’organisation Azure Active Directory (Azure AD).

## <a name="managed-identities-preview"></a>Identités managées (préversion)

Une identité managée issue d’Azure Active Directory (Azure AD) permet à votre runbook d’accéder facilement aux autres ressources protégées par Azure AD. Managée par la plateforme Azure, l’identité ne nécessite pas que vous approvisionniez ou permutiez de secrets. Pour plus d’informations sur les identités managées dans Azure AD, consultez [Identités managées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/overview).

Voici quelques-uns des avantages de l’utilisation des identités managées :

- Vous pouvez utiliser des identités managées pour vous authentifier auprès d’un service Azure qui prend en charge l’authentification Azure AD. Elles peuvent être utilisées pour les tâches cloud et hybrides. Les tâches hybrides peuvent utiliser des identités managées lorsqu’elles sont exécutées sur un runbook Worker hybride qui s’exécute sur une machine virtuelle Azure ou non Azure.

- Les identités managées peuvent être utilisées sans coût supplémentaire.

- Vous n’avez pas à renouveler le certificat utilisé par le compte d’identification Automation.

- Vous n’avez pas besoin de spécifier l’objet de connexion d’identification dans le code de votre runbook. Vous pouvez accéder aux ressources à l’aide de l’identité managée de votre compte Automation à partir d’un runbook sans créer de certificats, de connexions, de comptes d’identification, etc.

Deux types d’identités peuvent être accordés à un compte Automation :

- Une identité attribuée par le système est liée à votre application et est supprimée si votre application est supprimée. Une application ne peut avoir qu’une seule identité attribuée par le système.

- Une identité attribuée par l’utilisateur est une ressource Azure autonome qui peut être assignée à votre application. Une application peut avoir plusieurs identités attribuées par l’utilisateur.

>[!NOTE]
> Les identités affectées par l’utilisateur ne sont pas encore prises en charge.

Pour plus d’informations sur l’utilisation des identités gérées, consultez [Activer une identité managée pour Azure Automation (préversion)](enable-managed-identity-for-automation.md).

## <a name="run-as-accounts"></a>Comptes d'identification

Les comptes d’identification dans Azure Automation fournissent une authentification pour la gestion des ressources Azure Resource Manager ou des ressources déployées sur le modèle de déploiement Classic. Il existe deux types de comptes d’identification dans Azure Automation :

* Compte d’identification Azure : Vous permet de gérer les ressources Azure en fonction du déploiement Azure Resource Manager et du service de gestion pour Azure.
* Compte d’identification Azure Classic : Vous permet de gérer les ressources classiques d’Azure en fonction du modèle de déploiement Classic.

Pour en savoir plus sur les modèles de déploiement Azure Resource Manager et Classic, consultez [Déploiement Resource Manager et déploiement classique](../azure-resource-manager/management/deployment-models.md).

>[!NOTE]
>Les abonnements Fournisseur de solutions Azure Cloud (CSP) prennent uniquement en charge le modèle Azure Resource Manager. Les services non-Azure Resource Manager ne sont pas disponibles dans le programme. Lorsque vous utilisez un abonnement CSP, le compte d’identification Azure Classic n’est pas créé, c’est le compte d’identification Azure qui l’est. Pour en savoir plus sur les abonnements CSP, consultez [Services disponibles dans les abonnements CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services).

Lorsque vous créez un compte Automation, le compte d’identification est créé par défaut en même temps. Si vous avez choisi de ne pas le créer avec le compte Automation, vous pouvez le créer à un moment ultérieur. Un compte d’identification Azure Classic est facultatif et est créé séparément si vous devez gérer des ressources classiques.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>compte d'identification

Lorsque vous créez un compte d’identification, il effectue les tâches suivantes :

* Crée une application Azure AD avec un certificat auto-signé, crée un compte de principal de service pour cette application dans Azure AD et affecte le rôle [Contributeur](../role-based-access-control/built-in-roles.md#contributor) au compte dans votre abonnement actuel. Vous pouvez remplacer le paramètre de certificat par un rôle [Lecteur](../role-based-access-control/built-in-roles.md#reader) ou tout autre rôle. Pour plus d’informations, voir [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).

* Crée une ressource de certificat Automation nommée `AzureRunAsCertificate` dans le compte Automation spécifié. La ressource de certificat conserve la clé privée du certificat que l’application Azure AD utilise.

* Crée une ressource de connexion Automation nommée `AzureRunAsConnection` dans le compte Automation spécifié. La ressource de connexion contient l'ID de l'application, l'ID du locataire, l'ID de l'abonnement et l'empreinte du certificat.

### <a name="azure-classic-run-as-account"></a>Compte d’identification Azure Classic

Lorsque vous créez un compte d’identification Azure Classic, il effectue les tâches suivantes :

> [!NOTE]
> Vous devez être coadministrateur sur l’abonnement pour créer ou renouveler ce type de compte d’identification.

* Crée un certificat de gestion dans l’abonnement.

* Crée une ressource de certificat Automation nommée `AzureClassicRunAsCertificate` dans le compte Automation spécifié. La ressource de certificat conserve la clé privée du certificat utilisée par le certificat de gestion.

* Crée une ressource de connexion Automation nommée `AzureClassicRunAsConnection` dans le compte Automation spécifié. La ressource de connexion conserve le nom de l’abonnement, l’ID d’abonnement et le nom de la ressource de certificat.

## <a name="service-principal-for-run-as-account"></a>Principal du service pour le compte d’identification

Le principal du service d’un compte d’identification ne dispose pas d’autorisations pour lire Azure AD par défaut. Si vous souhaitez ajouter des autorisations pour lire ou gérer Azure AD, vous devez accorder ces autorisations sur le principal du service, sous **Autorisations des API**. Pour en savoir plus, consultez [Ajouter des autorisations pour accéder à vos API web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Autorisations de compte d’identification

Cette section définit les autorisations pour les comptes d’identification standard et les comptes d’identification Classic.

* Pour créer ou mettre à jour un compte d’identification, un administrateur d’application dans Azure Active Directory et un propriétaire dans un abonnement peuvent effectuer toutes les tâches.
* Pour configurer ou renouveler des comptes d’identification Classic, vous devez disposer du rôle Coadministrateur au niveau de l’abonnement. Pour en savoir plus sur les autorisations d’abonnement Classic, consultez [Administrateurs d’abonnement Azure Classic](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

Dans une situation où les responsabilités sont partagées, le tableau suivant donne une liste des tâches, avec les applets de commande équivalentes et les autorisations nécessaires :

|Tâche|Applet de commande  |Autorisations minimales  |Où vous définissez les autorisations|
|---|---------|---------|---|
|Créer une application Azure AD|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Rôle de développeur d’applications<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Accueil > Azure AD > Inscriptions d’applications |
|Ajoutez les informations d’identification à l’application.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Administrateur d’application ou administrateur général<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Accueil > Azure AD > Inscriptions d’applications|
|Créer et obtenir un principal du service Azure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Administrateur d’application ou administrateur général<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Accueil > Azure AD > Inscriptions d’applications|
|Attribuer ou obtenir le rôle Azure pour le principal spécifié|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrateur ou propriétaire de l’accès utilisateur, ou détenteur des autorisations suivantes :</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Abonnement](../role-based-access-control/role-assignments-portal.md)</br>Accueil > Abonnements > \<subscription name\> - Contrôle d’accès (IAM)|
|Créer ou supprimer un certificat Automation|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Contributeur sur le groupe de ressources         |Groupe de ressources du compte Automation|
|Créer ou supprimer une connexion Automation|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Contributeur sur le groupe de ressources |Groupe de ressources du compte Automation|

<sup>1</sup> Les utilisateurs non administrateurs dans votre locataire Azure AD peuvent [inscrire des applications AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) si l’option **Les utilisateurs peuvent inscrire des applications** du locataire Azure AD dans la page **Paramètres utilisateur** a la valeur **Oui**. Si le paramètre d’inscription des applications est **Non**, l’utilisateur qui effectue cette action doit être tel que défini dans ce tableau.

Si vous n’êtes pas membre de l’instance Active Directory de l’abonnement avant d’être ajouté au rôle Administrateur général de l’abonnement, vous êtes ajouté en tant qu’invité. Dans ce cas, vous recevez un avertissement `You do not have permissions to create…` dans la page **Ajouter un compte Automation**.

Pour vérifier que la situation produisant le message d’erreur a été corrigée :

1. Dans le volet Azure Active Directory du portail Azure, sélectionnez **Utilisateurs et groupes**.
2. Sélectionnez **Tous les utilisateurs**.
3. Choisissez votre nom, puis sélectionnez **Profil**.
4. Vérifiez que la valeur de l’attribut **Usertype** sous votre profil d’utilisateur n’est pas définie sur **Invité**.

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Le contrôle d’accès en fonction du rôle est disponible avec Azure Resource Manager pour attribuer des actions autorisées à un compte d’utilisateur Azure AD et à un compte d’identification, et pour authentifier le principal du service. Pour obtenir plus d’informations susceptibles de vous aider à développer votre modèle de gestion des autorisations Automation, consultez l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).

Si vous avez des contrôles de sécurité stricts pour l’attribution d’autorisations dans des groupes de ressources, vous devez affecter l’appartenance au compte d’identification au rôle **Contributeur** dans le groupe de ressources.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Authentification des runbooks avec Runbook Worker hybride

Les runbooks exécutés sur un Runbook Worker hybride dans votre centre de données ou sur des services informatiques dans d’autres environnements cloud tels qu’AWS ne peuvent pas utiliser la même méthode que celle généralement utilisée pour l’authentification des runbooks auprès des ressources Azure. Cela est dû au fait que ces ressources sont en cours d’exécution en dehors d’Azure et, par conséquent, elles nécessitent leurs propres informations d’identification de sécurité définies dans Automation pour s’authentifier auprès des ressources auxquelles elles accèdent localement. Pour plus d’informations sur l’authentification des runbooks auprès des Runbook Workers, consultez [Exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md).

Pour les runbooks qui utilisent des Runbook Workers hybrides sur des machines virtuelles Azure, vous pouvez utiliser [l’authentification des runbooks avec les identités managées](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) plutôt que des comptes d’identification pour l’authentification auprès de vos ressources Azure.

## <a name="next-steps"></a>Étapes suivantes

* Pour créer un compte Automation à partir du portail Azure, consultez [Créer un compte Azure Automation autonome](automation-create-standalone-account.md).
* Si vous préférez créer votre compte à l’aide d’un modèle, consultez [Créer un compte Automation à l’aide d’un modèle Azure Resource Manager](quickstart-create-automation-account-template.md).
* Pour l’authentification à l’aide de Amazon Web Services, consultez [Authentifier des runbooks avec Amazon Web Services](automation-config-aws-account.md).
* Pour obtenir la liste des services Azure qui prennent en charge la fonctionnalité d’identités managées pour les ressources Azure, consultez [Services qui prennent en charge les identités managées pour les ressources Azure](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).
