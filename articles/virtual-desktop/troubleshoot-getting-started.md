---
title: Dépannage de la fonctionnalité de prise en main d’Azure Virtual Desktop
description: Comment résoudre les problèmes rencontrés avec la fonctionnalité de prise en main d’Azure Virtual Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 21cdccf5a025db4516d53c506c070fcf6f580fb1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532477"
---
# <a name="troubleshoot-the-azure-virtual-desktop-getting-started-feature"></a>Dépanner la fonctionnalité de prise en main Azure Virtual Desktop

La fonctionnalité de prise en main d’Azure Virtual Desktop utilise des modèles imbriqués pour déployer des ressources Azure en vue de la validation et de l’automatisation dans Azure Virtual Desktop. La fonctionnalité de prise en main crée deux ou trois groupes de ressources selon que l’abonnement sur lequel elle s’exécute dispose ou non des services existants suivants : Active Directory Domain Services (AD DS) or Azure Active Directory Domain Services (Azure AD DS). Tous les groupes de ressources commencent par le même préfixe défini par l’utilisateur.

Lorsque vous exécutez les modèles imbriqués, ils créent trois groupes de ressources et un modèle qui approvisionne des ressources Azure Resource Manager. Les listes suivantes affichent chaque groupe de ressources et les modèles qu’ils exécutent.

Le groupe de ressources qui se termine par « -deployment » exécute les modèles suivants :

- easy-button-roleassignment-job-linked-template
- easy-button-prerequisitecompletion-job-linked-template
- easy-button-prerequisite-job-linked-template
- easy-button-inputvalidation-job-linked-template
- easy-button-deploymentResources-linked-template
- easy-button-prerequisite-user-setup-linked-template

>[!NOTE]
>Le modèle easy-button-prerequisite-user-setup-linked-template est facultatif et ne s’affiche que si vous avez créé un utilisateur de validation.

Le groupe de ressources qui se termine par « -wvd » exécute les modèles suivants :

- NSG-linkedTemplate
- vmCreation-linkedTemplate
- Workspace-linkedTemplate
- wvd-resources-linked-template
- easy-button-wvdsetup-linked-template

Le groupe de ressources qui se termine par « -prerequisite » exécute les modèles suivants :

- easy-button-prerequisite-resources-linked-template

>[!NOTE]
>Ce groupe de ressources est facultatif et ne s’affiche que si votre abonnement n’a pas Azure AD DS ou AD DS.

## <a name="no-subscriptions"></a>Aucun abonnement

Dans ce numéro, vous voyez un message d’erreur indiquant « aucun abonnement » lors de l’ouverture de la fonctionnalité de prise en main. Cela se produit lorsque vous essayez d’ouvrir la fonctionnalité sans abonnement Azure actif.

Pour résoudre ce problème, vérifiez si votre abonnement ou l’utilisateur affecté dispose d’un abonnement Azure actif. Si ce n’est pas le cas, attribuez à l’utilisateur le rôle de Access Control en fonction du rôle Owner (RBAC) sur son abonnement.

## <a name="you-dont-have-permissions"></a>Vous n’avez pas les autorisations

Ce problème se produit lorsque vous ouvrez la fonctionnalité de mise en route et que vous recevez un message d’erreur indiquant « Vous n’avez pas les autorisations ». Ce message s’affiche lorsque l’utilisateur qui exécute la fonctionnalité ne dispose pas des autorisations de propriétaire sur son abonnement Azure actif.

Pour résoudre ce problème, connectez-vous avec un compte Azure disposant des autorisations de propriétaire, puis attribuez le rôle RBAC de propriétaire au compte affecté.

## <a name="fields-under-virtual-machine-tab-are-grayed-out"></a>Les champs sous l’onglet Machine virtuelle sont grisés

Ce problème se produit lorsque vous ouvrez l’onglet **Machine virtuelle** et que vous voyez que les champs sous « Voulez-vous que les utilisateurs partagent cet ordinateur ? ». sont grisés. Ce problème vous empêche ensuite de modifier le type d’image, de sélectionner une image à utiliser ou de modifier la taille de la machine virtuelle.

Ce problème se produit lorsque vous exécutez la fonctionnalité avec un préfixe déjà utilisé pour démarrer un déploiement. Lorsque la fonctionnalité crée un déploiement, elle crée un objet pour représenter le déploiement dans Azure. Certaines valeurs de l’objet, comme l’image, sont attachées à cet objet pour empêcher plusieurs objets d’utiliser les mêmes images.

Pour résoudre ce problème, vous pouvez soit supprimer tous les groupes de ressources avec le préfixe existant, soit utiliser un nouveau préfixe.

## <a name="username-must-not-include-reserved-words"></a>Le nom d’utilisateur ne doit pas inclure de mots réservés

Ce problème se produit lorsque la fonctionnalité de prise en main n’accepte pas le nouveau nom d’utilisateur que vous entrez dans le champ.

Ce message d’erreur s’affiche car Azure n’autorise pas certains mots dans les noms d’utilisateur pour les points de terminaison publics. Pour la liste des mots bloqués, consultez [Résoudre les erreurs de nom de ressource réservé](../azure-resource-manager/templates/error-reserved-resource-name.md).

Pour résoudre ce problème, essayez un nouveau mot ou ajoutez des lettres au mot bloqué pour le rendre unique. Par exemple, si le mot « admin » est bloqué, essayez d’utiliser « AVDadmin » à la place.

## <a name="the-value-must-be-between-12-and-72-characters-long"></a>La valeur doit avoir une longueur comprise entre 12 et 72 caractères.

Ce message d’erreur s’affiche lorsque vous entrez un mot de passe qui est trop long ou trop petit pour respecter les exigences de longueur de caractère. Les exigences en matière de longueur et de complexité des mots de passe dans Azure s’appliquent même aux champs que vous utiliserez plus tard dans Windows, qui a des exigences moins strictes.

Pour résoudre ce problème, assurez-vous d’utiliser un compte qui respecte les [instructions relatives aux mots de passe de Microsoft](https://www.microsoft.com/research/publication/password-guidance) ou qui utilise la [protection par mot de passe Azure AD](../active-directory/authentication/concept-password-ban-bad.md).

## <a name="error-messages-for-easy-button-prerequisite-user-setup-linked-template"></a>Messages d’erreur pour easy-button-prerequisite-user-setup-linked-template

Si la machine virtuelle AD DS que vous utilisez a déjà une extension nommée Microsoft.PowerShell.DSC qui lui est associée, un message d’erreur similaire à celui-ci s’affiche :

```azure
"error": {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.",
        "details": [
            {
                "code": "Conflict",
                "message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \\\"DSC Configuration 'AddADDSUser' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: Some error occurred in DSC CreateUser SetScript: \\r\\n\\r\\nException             : Microsoft.ActiveDirectory.Management.ADIdentityNotFoundException: Cannot find an object with \\r\\n                        identity: 'Adam S' under: 'DC=GT090617,DC=onmicrosoft,DC=com'.\\r\\n                           at Microsoft.ActiveDirectory.Management.Commands.ADFactoryUtil.GetObjectFromIdentitySearcher(\\r\\n                        ADObjectSearcher searcher, ADEntity identityObj, String searchRoot, AttributeSetRequest attrs, \\r\\n                        CmdletSessionInfo cmdletSessionInfo, String[]& warningMessages)\\r\\n                           at \\r\\n                        Microsoft.ActiveDirectory.Management.Commands.ADFactory`1.GetDirectoryObjectFromIdentity(T \\r\\n                        identityObj, String searchRoot, Boolean showDeleted)\\r\\n                           at \\r\\n                        Microsoft.ActiveDirectory.Management.Commands.SetADGroupMember`1.ValidateMembersParameter()\\r\\nTargetObject          : Adam S\\r\\nCategoryInfo          : ObjectNotFound: (Adam S:ADPrincipal) [Add-ADGroupMember], ADIdentityNotFoundException\\r\\nFullyQualifiedErrorId : SetADGroupMember.ValidateMembersParameter,Microsoft.ActiveDirectory.Management.Commands.AddADGro\\r\\n                        upMember\\r\\nErrorDetails          : \\r\\nInvocationInfo        : System.Management.Automation.InvocationInfo\\r\\nScriptStackTrace      : at <ScriptBlock>, C:\\\\Packages\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.83.1.0\\\\DSCWork\\\\DSCADUserCreatio\\r\\n                        nScripts_2020-04-28.2\\\\Script-CreateADDSUser.ps1: line 98\\r\\n                        at <ScriptBlock>, <No file>: line 8\\r\\n                        at ScriptExecutionHelper, C:\\\\Windows\\\\system32\\\\WindowsPowerShell\\\\v1.0\\\\Modules\\\\PSDesiredStateConfi\\r\\n                        guration\\\\DscResources\\\\MSFT_ScriptResource\\\\MSFT_ScriptResource.psm1: line 270\\r\\n                        at Set-TargetResource, C:\\\\Windows\\\\system32\\\\WindowsPowerShell\\\\v1.0\\\\Modules\\\\PSDesiredStateConfigur\\r\\n                        ation\\\\DscResources\\\\MSFT_ScriptResource\\\\MSFT_ScriptResource.psm1: line 144\\r\\nPipelineIterationInfo : {}\\r\\nPSMessageDetails      : \\r\\n\\r\\n\\r\\n\\r\\n  The SendConfigurationApply function did not succeed.\\\"\\r\\n\\r\\nMore information on troubleshooting is available at https://aka.ms/VMExtensionDSCWindowsTroubleshoot \"\r\n      }\r\n    ]\r\n  }\r\n}"
            }
        ]
    }

```

Pour résoudre ce problème, désinstallez l’extension Microsoft.PowerShell DSC, puis exécutez à nouveau la fonctionnalité de prise en main.

## <a name="error-messages-for-easy-button-prerequisite-job-linked-template"></a>Messages d’erreur pour easy-button-prerequisite-job-linked-template

Si un message d’erreur semblable à celui-ci s’affiche, cela signifie que l’opération de ressource pour le modèle easy-button-prerequisite-job-linked-template ne s’est pas terminée correctement :

```azure
{
    "status": "Failed",
    "error": {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.",
        "details": [
            {
                "code": "Conflict",
                "message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\"\r\n  }\r\n}"
            }
        ]
    }
}

```

Pour vous assurer qu’il s’agit du problème que vous êtes en train de traiter :

1. Sélectionnez **easy-button-prerequisite-job-linked-template**, puis sélectionnez **OK** dans la fenêtre de message d’erreur qui s’affiche.

2. Accédez au **\<prefix\>groupe de ressources -deployment** et sélectionnez **resourceSetupRunbook**.

3. Sélectionnez l’état, qui doit indiquer **Échec**.

4. Sélectionnez l’onglet **Exception**. Un message d’erreur semblable à celui-ci doit s’afficher :
   
   ```azure
   The running command stopped because the preference variable "ErrorActionPreference" or common parameter is set to Stop: Error while creating and adding validation user <your-username-here> to group <your-resource-group-here>
   ```

Il n’existe actuellement aucun moyen de résoudre ce problème de manière permanente. Pour résoudre ce problème, exécutez à nouveau la fonctionnalité de prise en main d’Azure Virtual Desktop, mais cette fois-ci, ne créez pas d’utilisateur de validation. Après cela, créez vos nouveaux utilisateurs avec le processus manuel uniquement.

### <a name="validate-that-the-domain-administrator-upn-exists-for-a-new-profile"></a>Valider l’existence de l’UPN de l’administrateur de domaine pour un nouveau profil

Pour vérifier si l’adresse UPN est à l’origine du problème avec le modèle :

1. Sélectionnez **easy-button-prerequisite-job-linked-template**, puis dans l’étape qui a échoué. Confirmez le message d’erreur.

2. Accédez au **\<prefix\>groupe de ressources -deployment** et cliquez sur le **resourceSetupRunbook**.

3. Sélectionnez l’état, qui doit indiquer **Échec**.

4. Sélectionnez l’onglet **Sortie**.

Si l’UPN existe sur votre nouvel abonnement, il existe deux causes possibles pour le problème :

- La fonctionnalité de prise en main n’a pas créé le profil d’administrateur de domaine, car l’utilisateur existe déjà. Pour résoudre ce problème, exécutez à nouveau la fonctionnalité de prise en main, mais cette fois-ci, entrez un nom d’utilisateur qui n’existe pas encore dans votre fournisseur d’identité.
- La fonctionnalité de prise en main n’a pas créé le profil utilisateur de validation. Pour résoudre ce problème, exécutez à nouveau la fonctionnalité de prise en main, mais cette fois-ci, ne créez aucun utilisateur de validation. Après cela, créez vos nouveaux utilisateurs avec le processus manuel uniquement.

## <a name="error-messages-for-easy-button-inputvalidation-job-linked-template"></a>Messages d’erreur pour easy-button-inputvalidation-job-linked-template

En cas de problème avec le modèle easy-button-inputvalidation-job-linked-template, un message d’erreur similaire à celui-ci s’affiche :

```azure
{
    "status": "Failed",
    "error": {
        "code": "ResourceDeploymentFailure",
        "message": "The resource operation completed with terminal provisioning state 'Failed'."
    }
}
```

Pour vous assurer qu’il s’agit du problème que vous avez rencontré :

1. Ouvrez le \<prefix\>groupe de ressources -deployment et recherchez **inputValidationRunbook.**

2. Dans les tâches récentes, il y aura une tâche dont l’état est Échec. Cliquez sur **Échec**.

3. Dans la fenêtre **détails de la tâche**, sélectionnez **Exception**.

Cette erreur se produit lorsque l’UPN de l’administrateur Azure que vous avez entré n’est pas correct. Pour résoudre ce problème, assurez-vous que vous entrez le nom d’utilisateur et le mot de passe corrects, puis réessayez.

## <a name="multiple-vmextensions-per-handler-not-supported"></a>Plusieurs VMExtensions par gestionnaire non prises en charge

Quand vous exécutez la fonctionnalité de prise en main sur un abonnement qui a Azure AD DS ou AD DS, la fonctionnalité utilise une extension Microsoft.PowerShell.DSC pour créer des utilisateurs de validation et configurer FSLogix. Toutefois, les machines virtuelles Windows dans Azure ne peuvent pas exécuter plus d’un même type d’extension en même temps.

Si vous essayez d’exécuter plusieurs versions de Microsoft.PowerShell.DSC, vous obtiendrez un message d’erreur similaire à celui-ci :

```azure
{
    "status": "Failed",
    "error": {
        "code": "BadRequest",
        "message": "Multiple VMExtensions per handler not supported for OS type 'Windows'. VMExtension 'Microsoft.Powershell.DSC' with handler 'Microsoft.Powershell.DSC' already added or specified in input."
    }
}
```

Pour résoudre ce problème, avant d’exécuter la fonctionnalité de prise en main, assurez-vous de supprimer toute instance en cours d’exécution de Microsoft.PowerShell.DSC de la machine virtuelle du contrôleur de domaine.

## <a name="failure-in-easy-button-prerequisitecompletion-job-linked-template"></a>Échec de easy-button-prerequisitecompletion-job-linked-template

Le groupe d’utilisateurs pour les utilisateurs de validation se trouve dans le conteneur « USERS ». Toutefois, le groupe d’utilisateurs doit être synchronisé avec Azure AD pour fonctionner correctement. Si ce n’est pas le cas, vous obtiendrez un message d’erreur similaire à celui-ci :

```azure
{
    "status": "Failed",
    "error": {
        "code": "ResourceDeploymentFailure",
        "message": "The resource operation completed with terminal provisioning state ‘Failed’."
    }
}
```

Pour vous assurer que le problème est dû au fait que le groupe d’utilisateurs de validation ne se synchronise pas, ouvrez le \<prefix\>groupe de ressources prerequisites et recherchez un fichier nommé **prerequisiteSetupCompletionRunbook**. Sélectionnez le runbook, puis sélectionnez **Tous les journaux**.

Pour résoudre ce problème :

1. Activez la synchronisation avec Azure AD pour le conteneur « USERS ».

2. Créez le groupe AVDValidationUsers dans une unité d’organisation qui est en cours de synchronisation avec Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la fonctionnalité de prise en main, consultez [Déployer Azure Virtual Desktop avec la fonctionnalité de prise en main](getting-started-feature.md).
