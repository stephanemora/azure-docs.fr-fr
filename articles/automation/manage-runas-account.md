---
title: Gérer un compte d'identification Azure Automation
description: Cet article décrit comment gérer votre compte d’identification avec PowerShell ou à partir du portail Azure.
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.openlocfilehash: f9e99318c526bb935d0e035fdbf59874249390da
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050937"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Gérer un compte d'identification Azure Automation

Les comptes d’identification d’Azure Automation assurent l’authentification pour la gestion des ressources sur le modèle de déploiement Azure Resource Manager ou Azure Classic à l’aide de runbooks Automation et d’autres fonctionnalités Automation. Cet article aide à gérer un compte d’identification ou un compte d’identification Classic.

Pour plus d’informations sur l’authentification des comptes Azure Automation et des conseils relatifs aux scénarios d’automatisation des processus, consultez [Vue d’ensemble de l’authentification des comptes Automation](automation-security-overview.md).

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Renouveler un certificat auto-signé

Le certificat auto-signé que vous avez créé pour le compte d’identification expire au bout d’un an après la date de création. Avant que votre compte d’identification n’expire, vous devez renouveler le certificat. Vous pouvez le renouveler à tout moment avant qu’il n’expire.

Lorsque vous renouvelez le certificat auto-signé, le certificat valide en cours est conservé afin de garantir que les Runbooks en file d’attente ou en cours d’exécution, qui s’authentifient avec le compte d’identification, ne sont pas affectés. Le certificat reste valide jusqu’à sa date d’expiration.

>[!NOTE]
>Si vous pensez que le compte d’identification a été compromis, vous pouvez supprimer et recréer le certificat auto-signé.

>[!NOTE]
>Si vous avez configuré votre compte d’identification pour utiliser un certificat émis par votre autorité de certification d’entreprise, et que vous utilisez l’option permettant de renouveler un certificat auto-signé, le certificat d’entreprise est remplacé par un certificat auto-signé.

Effectuez les étapes suivantes pour renouveler le certificat auto-signé.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Accédez à votre compte Automation, puis sélectionnez **Comptes d’identification** dans la section des paramètres de compte.

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Panneau de propriétés du compte Automation.":::

1. Sur la page de propriétés des **Comptes d’identification**, sélectionnez **Compte d’identification** ou **Compte d’identification Classic** en fonction du compte pour lequel vous devez renouveler le certificat.

1. Sur la page **Propriétés** du compte sélectionné, sélectionnez **Renouveler le certificat**.

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="Renouvellement du certificat pour le compte d’identification.":::

1. Pour suivre la progression du renouvellement du certificat, accédez à l’onglet **Notifications** du menu.

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>Octroi des autorisations de compte d’identification dans d’autres abonnements

Azure Automation gère l’utilisation d’un compte Automation unique à partir d’un seul abonnement et l’exécution de runbooks sur des ressources Azure Resource Manager provenant de plusieurs abonnements. Cette configuration ne prend pas en charge le modèle de déploiement Azure Classic.

Vous attribuez au principal de service du compte d’identification le rôle [Contributeur](../role-based-access-control/built-in-roles.md#contributor) dans l’autre abonnement ou des autorisations plus restrictives. Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle](automation-role-based-access-control.md) dans Azure Automation. Pour attribuer le rôle au compte d’identification dans l’autre abonnement, le compte d’utilisateur qui effectue cette tâche doit être membre du rôle **Propriétaire** dans cet abonnement.

> [!NOTE]
> Cette configuration ne prend en charge plusieurs abonnements d’une organisation qu’à l’aide d’un locataire Azure AD commun.

Avant d’accorder les autorisations du compte d’identification, vous devez d’abord noter le nom complet du principal de service à affecter.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Depuis votre compte Automation, sélectionnez **Comptes d'identification** sous **Paramètres du compte**.
1. Sélectionnez **Compte d'identification Azure**.
1. Copiez ou notez la valeur de **Nom complet** sur la page **Compte d’identification Azure**.

Pour savoir en détail comment ajouter des attributions de rôles, consultez les articles suivants en fonction de la méthode que vous souhaitez utiliser.

* [Ajout d’une attribution de rôle Azure sur le Portail Azure](../role-based-access-control/role-assignments-portal.md)
* [Ajout d’une attribution de rôle Azure avec Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
* [Ajout d’une attribution de rôle Azure avec Azure CLI](../role-based-access-control/role-assignments-cli.md)
* [Ajout d’une attribution de rôle Azure avec l’API REST](..//role-based-access-control/role-assignments-rest.md)

Après avoir attribué le rôle au compte d’identification, spécifiez `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` dans votre runbook pour définir le contexte de l’abonnement à utiliser. Pour plus d’informations, consultez [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

## <a name="limit-run-as-account-permissions"></a>Limiter les autorisations d’un compte d’identification

Pour contrôler le ciblage de l’automatisation sur des ressources dans Azure, vous pouvez exécuter le script [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8). Ce script modifie votre principal du service du compte d’identification existant pour créer et utiliser une définition de rôle personnalisée. Ce rôle dispose d’autorisations pour toutes les ressources, à l’exception de [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Après l’exécution du script **Update-AutomationRunAsAccountRoleAssignments.ps1**, les runbooks qui accèdent à Key Vault par le biais de l’utilisation de comptes d’identification ne fonctionnent plus. Avant d’exécuter le script, vous devez examiner les runbooks de votre compte pour repérer les appels à Azure Key Vault. Pour activer l’accès à Key Vault à partir des runbooks Azure Automation, vous devez [ajouter le compte d’identification aux autorisations de Key Vault](#add-permissions-to-key-vault).

Si vous devez restreindre davantage les actions possibles du principal de service du compte d’identification, vous pouvez ajouter d’autres types de ressources à l’élément `NotActions` de la définition de rôle personnalisé. L’exemple suivant restreint l’accès à `Microsoft.Compute/*`. Si vous ajoutez ce type de ressource à l’élément `NotActions` de la définition de rôle, le rôle ne sera pas en mesure d’accéder à une ressource de calcul. Pour en savoir plus sur les définitions de rôle, consultez [Comprendre les définitions de rôle relatives aux ressources Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Vous pouvez déterminer quel rôle a été attribué au principal de service utilisé par votre compte d’identification : le rôle **Contributeur** ou un rôle personnalisé.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à votre compte Automation, puis sélectionnez **Comptes d’identification** dans la section des paramètres de compte.
1. Sélectionnez **Compte d'identification Azure**.
1. Sélectionnez **Rôle** pour rechercher la définition de rôle en cours d’utilisation.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Vérifiez le rôle du compte d’identification." lightbox="media/manage-runas-account/verify-role-expanded.png":::

Vous pouvez également déterminer la définition de rôle utilisée par les comptes d’identification pour plusieurs abonnements ou comptes Automation. Pour ce faire, utilisez le script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) dans PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Ajouter des autorisations à Key Vault

Vous pouvez autoriser Azure Automation à vérifier si Key Vault et votre principal du service du compte d’identification utilisent une définition de rôle personnalisée. Vous devez respecter les consignes suivantes :

* accorder les autorisations à Key Vault ;
* définir la stratégie d’accès.

Vous pouvez utiliser le script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) dans PowerShell Gallery pour accorder à votre compte d’identification des autorisations sur Key Vault. Pour plus d’informations sur la définition des autorisations sur Key Vault, consultez [Attribuer une stratégie d'accès Key Vault](../key-vault/general/assign-access-policy-powershell.md).

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Résoudre des problèmes de configuration incorrecte pour les comptes d’identification

Certains éléments de configuration nécessaires à un compte d’identification standard ou à un compte d’identification Classic ont peut-être été supprimés, ou n’ont pas été créés correctement lors de l’installation initiale. Les cas possibles de configuration incorrecte sont les suivants :

* Ressource de certificat
* Ressource de connexion
* Compte d’identification supprimé du rôle Contributeur
* Principal du service ou application dans Azure AD

Pour de tels cas de configuration incorrecte, le compte Automation détecte les modifications et affiche pour le compte l’état *Incomplet* dans le volet des propriétés des comptes d’identification.

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="Configuration incomplète du compte d’identification.":::

Lorsque vous sélectionnez le compte d’identification, le volet des propriétés du compte affiche le message d’erreur suivant :

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Pour résoudre rapidement ces problèmes, [supprimez](delete-run-as-account.md) et [recréez](create-run-as-account.md) le compte d’identification.

## <a name="next-steps"></a>Étapes suivantes

* [Objets principal de service et d’application](../active-directory/develop/app-objects-and-service-principals.md).
* [Vue d’ensemble des certificats pour Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
* Pour créer ou recréer un compte d’identification, consultez [Création d’un compte d’identification](create-run-as-account.md).
* Si vous n’avez plus besoin d’utiliser un compte d’identification, consultez [Suppression d’un compte d’identification](delete-run-as-account.md).