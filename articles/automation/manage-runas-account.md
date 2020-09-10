---
title: Gérer un compte d'identification Azure Automation
description: Cet article décrit comment gérer votre compte d’identification avec PowerShell ou à partir du portail Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 06/26/2020
ms.topic: conceptual
ms.openlocfilehash: 3e5f75a5ff9c6baff9bbefea7846ffe78655c6a9
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401756"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Gérer un compte d'identification Azure Automation

Les comptes d’identification dans Azure Automation fournissent une authentification permettant de gérer des ressources dans Azure au moyen des applets de commande Azure. Quand vous créez un compte d’identification, un nouvel utilisateur du principal du service dans Azure Active Directory (AD) est créé, auquel est attribué le rôle de contributeur au niveau de l’abonnement.

## <a name="types-of-run-as-accounts"></a>Types de comptes d’identification

Azure Automation utilise deux types de comptes d’identification :

* Compte d’identification Azure
* Compte d’identification Azure Classic

>[!NOTE]
>Les abonnements Fournisseur de solutions Azure Cloud (CSP) prennent uniquement en charge le modèle Azure Resource Manager. Les services non-Azure Resource Manager ne sont pas disponibles dans le programme. Lorsque vous utilisez un abonnement CSP, le compte d’identification Azure Classic n’est pas créé, c’est le compte d’identification Azure qui l’est. Pour en savoir plus sur les abonnements CSP, consultez [Services disponibles dans les abonnements CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services).

Le principal du service d’un compte d’identification ne dispose pas d’autorisations pour lire Azure AD par défaut. Si vous souhaitez ajouter des autorisations pour lire ou gérer Azure AD, vous devrez accorder ces autorisations sur le principal du service, sous **Autorisations des API**. Pour en savoir plus, consultez [Ajouter des autorisations pour accéder aux API web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>compte d'identification

Le compte d’identification gère les ressources du [modèle de déploiement Resource Manager](../azure-resource-manager/management/deployment-models.md). Il effectue les tâches suivantes.

* Crée une application Azure AD avec un certificat auto-signé, crée un compte de principal de service pour cette application dans Azure AD et affecte le rôle Collaborateur pour le compte dans votre abonnement actuel. Vous pouvez remplacer le paramètre de certificat par un rôle de propriétaire ou tout autre rôle. Pour plus d’informations, voir [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).
  
* Crée une ressource de certificat Automation nommée `AzureRunAsCertificate` dans le compte Automation spécifié. La ressource de certificat conserve la clé privée du certificat que l’application Azure AD utilise.
  
* Crée une ressource de connexion Automation nommée `AzureRunAsConnection` dans le compte Automation spécifié. La ressource de connexion contient l'ID de l'application, l'ID du locataire, l'ID de l'abonnement et l'empreinte du certificat.

### <a name="azure-classic-run-as-account"></a>Compte d’identification Azure Classic

Le compte d’identification Azure Classic gère les ressources du [modèle de déploiement Classic](../azure-resource-manager/management/deployment-models.md). Vous devez être coadministrateur sur l’abonnement pour créer ou renouveler ce type de compte.

Le compte d’identification Azure Classic effectue les tâches suivantes.

  * Crée un certificat de gestion dans l’abonnement.

  * Crée une ressource de certificat Automation nommée `AzureClassicRunAsCertificate` dans le compte Automation spécifié. La ressource de certificat conserve la clé privée du certificat utilisée par le certificat de gestion.

  * Crée une ressource de connexion Automation nommée `AzureClassicRunAsConnection` dans le compte Automation spécifié. La ressource de connexion conserve le nom de l’abonnement, l’ID d’abonnement et le nom de la ressource de certificat.

>[!NOTE]
>Le compte d’identification Azure Classic n’est pas créé par défaut en même temps que vous créez un compte Automation. Ce compte est créé individuellement en suivant les étapes décrites plus loin dans cet article.

## <a name="obtain-run-as-account-permissions"></a><a name="permissions"></a>Obtenir des autorisations de compte d’identification

Cette section définit les autorisations pour les comptes d’identification standard et les comptes d’identification Classic.

### <a name="get-permissions-to-configure-run-as-accounts"></a>Obtenir des autorisations pour configurer des comptes d’identification

Pour créer ou mettre à jour un compte d’identification, vous devez disposer d’autorisations et de privilèges spécifiques. Un administrateur d’application dans Azure Active Directory et un propriétaire dans un abonnement peuvent effectuer toutes les tâches. Dans une situation où les responsabilités sont partagées, le tableau suivant donne une liste des tâches, avec les applets de commande équivalentes et les autorisations nécessaires :

|Tâche|Applet de commande  |Autorisations minimales  |Où vous définissez les autorisations|
|---|---------|---------|---|
|Créer une application Azure AD|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Rôle de développeur d’applications<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Accueil > Azure AD > Inscriptions d’applications |
|Ajoutez les informations d’identification à l’application.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Administrateur d’application ou administrateur général<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Accueil > Azure AD > Inscriptions d’applications|
|Créer et obtenir un principal du service Azure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Administrateur d’application ou administrateur général<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Accueil > Azure AD > Inscriptions d’applications|
|Attribuer ou obtenir le rôle Azure pour le principal spécifié|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrateur ou propriétaire de l’accès utilisateur, ou détenteur des autorisations suivantes :</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Abonnement](../role-based-access-control/role-assignments-portal.md)</br>Accueil > Abonnements > \<subscription name\> - Contrôle d’accès (IAM)|
|Créer ou supprimer un certificat Automation|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Contributeur sur le groupe de ressources         |Groupe de ressources du compte Automation|
|Créer ou supprimer une connexion Automation|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Contributeur sur le groupe de ressources |Groupe de ressources du compte Automation|

<sup>1</sup> Les utilisateurs non-administrateurs dans votre locataire Azure AD peuvent [inscrire des applications AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) si l’option **Les utilisateurs peuvent inscrire des applications** du locataire Azure AD dans la page des Paramètres utilisateur a la valeur **Oui**. Si le paramètre d’inscription des applications est **Non**, l’utilisateur qui effectue cette action doit être tel que défini dans ce tableau.

Si vous n’êtes pas membre de l’instance Active Directory de l’abonnement avant d’être ajouté au rôle Administrateur général de l’abonnement, vous êtes ajouté en tant qu’invité. Dans ce cas, vous recevez un avertissement `You do not have permissions to create…` sur la page **Ajouter un compte Automation**.

Si vous êtes membre de l’instance Active Directory de l’abonnement lorsque le rôle Administrateur général est attribué, vous pouvez également recevoir un avertissement `You do not have permissions to create…` dans la page **Ajouter un compte Automation**. Dans ce cas, vous pouvez demander la suppression à partir de l’instance Active Directory de l’abonnement, puis demander à être ajouté de nouveau, afin de devenir un utilisateur à part entière dans Active Directory.

Pour vérifier que la situation produisant le message d’erreur a été corrigée :

1. Dans le volet Azure Active Directory du portail Azure, sélectionnez **Utilisateurs et groupes**.
2. Sélectionnez **Tous les utilisateurs**.
3. Choisissez votre nom, puis sélectionnez **Profil**. 
4. Vérifiez que la valeur de l’attribut **Usertype** sous votre profil d’utilisateur n’est pas définie sur **Invité**.

### <a name="get-permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Obtenir des autorisations pour configurer des comptes d’identification Classic

Pour configurer ou renouveler des comptes d’identification Classic, vous devez disposer du rôle Coadministrateur au niveau de l’abonnement. Pour en savoir plus sur les autorisations d’abonnement Classic, consultez [Administrateurs d’abonnement Azure Classic](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Créer un compte d’identification dans le portail Azure

Effectuez les étapes suivantes pour mettre à jour votre compte Azure Automation dans le portail Azure. Créez individuellement les comptes d’identification standard et les comptes d’identification Classic. Si vous n’avez pas besoin de gérer des ressources classiques, vous pouvez simplement créer le compte d’identification Azure.

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.

2. Recherchez et sélectionnez **Comptes Automation**.

3. Dans la page Comptes Automation, sélectionnez votre compte Automation dans la liste.

4. Dans le volet gauche, sélectionnez **Comptes d’identification** dans la section des paramètres de compte.

5. Selon le compte dont vous avez besoin, sélectionnez **Compte d’identification Azure** ou **Compte d’identification Azure Classic**. 

6. En fonction du compte qui vous intéresse, utilisez le volet **Ajouter un compte d’identification Azure** ou **Ajouter un compte d’identification Azure Classic**. Après avoir passé en revue les informations générales, cliquez sur **Créer**.

7. Pour suivre la progression de la création du compte d’identification, accédez à l’onglet **Notifications** du menu. Une bannière s’affiche également indiquant que le compte est en cours de création. L’exécution de ce processus peut prendre plusieurs minutes.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Supprimer un compte d’identification standard ou Classic

Cette section décrit comment supprimer un compte d’identification standard ou Classic. Lorsque vous effectuez cette opération, le compte Automation est conservé. Après avoir supprimé le compte, vous pouvez le recréer dans le portail Azure.

1. Dans le portail Azure, ouvrez le compte Automation.

2. Dans le volet gauche, sélectionnez **Comptes d’identification** dans la section des paramètres de compte.

3. Dans la page de propriétés Comptes d’identification, sélectionnez le compte d’identification standard ou le compte d’identification Classic que vous voulez supprimer. 

4. Dans le volet Propriétés, pour le compte sélectionné, cliquez sur **Supprimer**.

   ![Supprimer un compte d’identification](media/manage-runas-account/automation-account-delete-runas.png)

5. Pour suivre la progression de la suppression du compte, accédez à l’onglet **Notifications** du menu.

6. Une fois le compte supprimé, vous pouvez le recréer dans la page de propriétés Comptes d’identification en sélectionnant l’option de création **Compte d’identification Azure**.

   ![Recréer le compte d’identification Automation](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Renouveler un certificat auto-signé

Le certificat auto-signé que vous avez créé pour le compte d’identification expire au bout d’un an après la date de création. Avant que votre compte d’identification n’expire, vous devez renouveler le certificat. Vous pouvez le renouveler à tout moment avant qu’il n’expire. 

Lorsque vous renouvelez le certificat auto-signé, le certificat valide en cours est conservé afin de garantir que les Runbooks en file d’attente ou en cours d’exécution, qui s’authentifient avec le compte d’identification, ne sont pas affectés. Le certificat reste valide jusqu’à sa date d’expiration.

>[!NOTE]
>Si vous pensez que le compte d’identification a été compromis, vous pouvez supprimer et recréer le certificat auto-signé.

>[!NOTE]
>Si vous avez configuré votre compte d’identification pour utiliser un certificat émis par votre autorité de certification d’entreprise, et que vous utilisez l’option permettant de renouveler un certificat auto-signé, le certificat d’entreprise est remplacé par un certificat auto-signé.

Effectuez les étapes suivantes pour renouveler le certificat auto-signé.

1. Dans le portail Azure, ouvrez le compte Automation.

1. Sélectionnez **Comptes d’identification** dans la section des paramètres de compte.

    ![Panneau des propriétés du compte Automation](media/manage-runas-account/automation-account-properties-pane.png)

1. Dans la page de propriétés des Comptes d’identification, sélectionnez le compte d’identification standard ou le compte d’identification Classic pour lequel renouveler le certificat.

1. Dans le volet Propriétés, pour le compte sélectionné, cliquez sur **Renouveler le certificat**.

    ![Renouveler le certificat pour le compte d’identification](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Pour suivre la progression du renouvellement du certificat, accédez à l’onglet **Notifications** du menu.

## <a name="limit-run-as-account-permissions"></a>Limiter les autorisations d’un compte d’identification

Pour contrôler le ciblage de l’automatisation sur des ressources dans Azure, vous pouvez exécuter le script [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8). Ce script modifie votre principal du service du compte d’identification existant pour créer et utiliser une définition de rôle personnalisée. Ce rôle dispose d’autorisations pour toutes les ressources, à l’exception de [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>Après l’exécution du script **Update-AutomationRunAsAccountRoleAssignments.ps1**, les runbooks qui accèdent à Key Vault par le biais de l’utilisation de comptes d’identification ne fonctionnent plus. Avant d’exécuter le script, vous devez examiner les runbooks de votre compte pour repérer les appels à Azure Key Vault. Pour activer l’accès à Key Vault à partir des runbooks Azure Automation, vous devez [ajouter le compte d’identification aux autorisations de Key Vault](#add-permissions-to-key-vault).

Si vous devez restreindre davantage ce que le principal du service du compte d’identification peut faire, vous pouvez ajouter d’autres types de ressources à l’élément `NotActions` de la définition de rôle personnalisée. L’exemple suivant restreint l’accès à `Microsoft.Compute/*`. Si vous ajoutez ce type de ressource à l’élément `NotActions` de la définition de rôle, le rôle ne sera pas en mesure d’accéder à une ressource de calcul. Pour en savoir plus sur les définitions de rôle, consultez [Comprendre les définitions de rôle relatives aux ressources Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Vous pouvez déterminer si le principal du service utilisé par votre compte d’identification se trouve dans la définition du rôle Contributeur ou dans une autre définition personnalisée. 

1. Accédez à votre compte Automation, puis sélectionnez **Comptes d’identification** dans la section des paramètres de compte.
2. Sélectionnez **Compte d'identification Azure**. 
3. Sélectionnez **Rôle** pour rechercher la définition de rôle en cours d’utilisation.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Vérifiez le rôle du compte d’identification." lightbox="media/manage-runas-account/verify-role-expanded.png":::


Vous pouvez également déterminer la définition de rôle utilisée par les comptes d’identification pour plusieurs abonnements ou comptes Automation. Pour ce faire, utilisez le script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) dans PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Ajouter des autorisations à Key Vault

Vous pouvez autoriser Azure Automation à vérifier si Key Vault et votre principal du service du compte d’identification utilisent une définition de rôle personnalisée. Vous devez respecter les consignes suivantes :

* accorder les autorisations à Key Vault ;
* définir la stratégie d’accès.

Vous pouvez utiliser le script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) dans PowerShell Gallery pour donner à votre compte d’identification des autorisations sur Key Vault. Pour plus d’informations sur la définition des autorisations sur Key Vault, consultez [Attribuer une stratégie d'accès Key Vault](/azure/key-vault/general/assign-access-policy-powershell).

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Résoudre des problèmes de configuration incorrecte pour les comptes d’identification

Certains éléments de configuration nécessaires à un compte d’identification standard ou à un compte d’identification Classic ont peut-être été supprimés, ou n’ont pas été créés correctement lors de l’installation initiale. Les cas possibles de configuration incorrecte sont les suivants :

* Ressource de certificat
* Ressource de connexion
* Compte d’identification supprimé du rôle Contributeur
* Principal du service ou application dans Azure AD

Pour de tels cas de configuration incorrecte, le compte Automation détecte les modifications et affiche pour le compte l’état *Incomplet* dans le volet des propriétés des comptes d’identification.

![État Incomplet pour la configuration du compte d’identification](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Lorsque vous sélectionnez le compte d’identification, le volet des propriétés du compte affiche le message d’erreur suivant :

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Vous pouvez rapidement résoudre ces problèmes liés au compte d’identification en supprimant et en recréant le compte.

## <a name="next-steps"></a>Étapes suivantes

* [Objets principal de service et d’application](../active-directory/develop/app-objects-and-service-principals.md).
* [Vue d’ensemble des certificats pour Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
