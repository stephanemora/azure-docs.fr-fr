---
title: Sécuriser l’accès à un coffre de clés - Azure Key Vault | Microsoft Docs
description: Gérez les autorisations d’accès à Azure Key Vault, aux clés et aux secrets. Couvre le modèle d’authentification et d’autorisation de Key Vault, et explique comment sécuriser votre coffre de clés.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: eac3850cfa0684bd1751cf7b88b4ff8e92667293
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197434"
---
# <a name="secure-access-to-a-key-vault"></a>Sécuriser l’accès à un coffre de clés

Azure Key Vault est un service cloud qui protège les clés et secrets de chiffrement comme les certificats, chaînes de connexion et mots de passe. Comme il s’agit de données sensibles et critiques, vous devez sécuriser l’accès à vos coffres de clés en acceptant seulement les applications et les utilisateurs autorisés. Cet article fournit une vue d’ensemble du modèle d’accès aux coffres de clés. Il décrit l’authentification et l’autorisation, puis explique comment sécuriser l’accès à vos coffres de clés.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Vue d’ensemble du modèle d’accès

L’accès à un coffre de clés est contrôlé par le biais de deux interfaces : le **plan de gestion** et le **plan de données**. Le plan de gestion vous permet de gérer le coffre de clés. Dans ce plan, vous pouvez notamment créer et supprimer des coffres de clés, récupérer des propriétés Key Vault et mettre à jour des stratégies d’accès. Le plan de données vous permet d’utiliser les données stockées dans un coffre de clés. Vous pouvez ajouter, supprimer et modifier des clés, des secrets et des certificats.

Pour accéder à un coffre de clés dans l’un ou l’autre de ces plans, tout appelant (utilisateur ou application) doit être authentifié et autorisé. L’authentification établit l’identité de l’appelant. L’autorisation détermine les opérations que l’appelant peut exécuter.

Les deux plans utilisent Azure Active Directory (Azure AD) pour l’authentification. Pour l’autorisation, le plan de gestion utilise le contrôle d’accès en fonction du rôle (RBAC), tandis que le plan de données s’appuie sur une stratégie d’accès Key Vault.

## <a name="active-directory-authentication"></a>Authentification Active Directory

Quand vous créez un coffre de clés dans un abonnement Azure, il est automatiquement associé au locataire Azure AD de l’abonnement. Tous les appelants dans les deux plans doivent s’inscrire auprès de ce locataire et s’authentifier pour accéder au coffre de clés. Dans les deux cas, les applications peuvent accéder au coffre de clés de deux façons :

- **Accès utilisateur plus application** : l’application accède au coffre de clés pour le compte d’un utilisateur connecté. Azure PowerShell et le portail Azure sont des exemples de ce type d’accès. L’accès utilisateur est accordé de deux manières. Les utilisateurs peuvent accéder au coffre de clés à partir de n’importe quelle application ou ils doivent utiliser une application spécifique (appelée _identité composée_).
- **Accès application uniquement** : L’application s’exécute comme service de démon ou travail en arrière-plan. L’identité de l’application se voit octroyer l’accès au coffre de clés.

Pour les deux types d’accès, l’application s’authentifie auprès d’Azure AD. L’application utilise une [méthode d’authentification prise en charge](../active-directory/develop/authentication-scenarios.md) en fonction du type d’application. L’application acquiert un jeton pour une ressource dans le plan pour accorder l’accès. La ressource est un point de terminaison dans le plan de gestion ou de données, en fonction de l’environnement Azure. L’application utilise le jeton et envoie une demande d’API REST à Key Vault. Pour en savoir plus, passez en revue le [flux d’authentification intégral](../active-directory/develop/v2-oauth2-auth-code-flow.md).

Le modèle d’un mécanisme d’authentification unique auprès des deux plans présente plusieurs avantages :

- Les organisations peuvent contrôler de manière centralisée l’accès à tous leurs coffres de clés.
- Si un utilisateur part, il perd instantanément l’accès à tous les coffres de clés de l’organisation.
- Les organisations peuvent personnaliser l’authentification à l’aide des options dans Azure AD, par exemple pour activer l’authentification multifacteur afin de renforcer la sécurité.

## <a name="resource-endpoints"></a>Points de terminaison de ressource

Les applications accèdent aux plans par le biais de points de terminaison. Les contrôles d’accès pour les deux plans fonctionnent indépendamment. Pour permettre à une application d’utiliser des clés dans un coffre de clés, vous accordez l’accès au plan de données à l’aide d’une stratégie d’accès Key Vault. Pour permettre à un utilisateur de lire les propriétés et les étiquettes d’un coffre de clés, mais pas d’accéder aux données (clés, secrets ou certificats), vous accordez l’accès au plan de gestion avec RBAC.

Le tableau suivant présente les points de terminaison pour les plans de gestion et de données.

| Plan&nbsp;d’accès | Points de terminaison d’accès | Opérations | Mécanisme de contrôle&nbsp;d’accès |
| --- | --- | --- | --- |
| Plan de gestion | **Mondial :**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government :**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany :**<br> management.microsoftazure.de:443 | Créer, lire, mettre à jour et supprimer des coffres de clés<br><br>Définir des stratégies d’accès Key Vault<br><br>Définir des étiquettes Key Vault | RBAC Azure Resource Manager |
| Plan de données | **Mondial :**<br> &lt;nom du coffre&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;nom du coffre&gt;.vault.azure.cn:443<br><br> **Azure US Government :**<br> &lt;nom du coffre&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany :**<br> &lt;nom du coffre&gt;.vault.microsoftazure.de:443 | Clés : déchiffrer, chiffrer,<br> unwrapper, wrapper, vérifier, signer,<br> obtenir, lister, mettre à jour, créer,<br> importer, supprimer, sauvegarder, restaurer<br><br> Secrets : obtenir, lister, définir, supprimer | Stratégie d’accès au coffre de clés |

## <a name="management-plane-and-rbac"></a>Plan de gestion et RBAC

Dans le plan de gestion, vous utilisez le contrôle d’accès en fonction du rôle (RBAC) pour autoriser les opérations qu’un appelant peut exécuter. Dans le modèle RBAC, chaque abonnement Azure a une instance d’Azure AD. Vous accordez l’accès aux utilisateurs, groupes et applications de ce répertoire. L’accès accordé permet de gérer les ressources de l’abonnement Azure qui reposent sur le modèle de déploiement Azure Resource Manager. Pour accorder l’accès, utilisez le [portail Azure](https://portal.azure.com/), l’interface [Azure CLI](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou les [API REST Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Vous créez un coffre de clés dans un groupe de ressources et gérez l’accès à l’aide d’Azure AD. Vous autorisez des utilisateurs ou des groupes à gérer les coffres de clés dans un groupe de ressources. Vous accordez l’accès à un niveau d’étendue spécifique en attribuant les rôles RBAC appropriés. Pour permettre à un utilisateur de gérer des coffres de clés, vous attribuez un rôle `key vault Contributor` prédéfini à l’utilisateur dans une étendue spécifique. Les niveaux d’étendue suivants peuvent être attribués à un rôle RBAC :

- **Abonnement**: un rôle RBAC attribué au niveau d’un abonnement s’applique à tous les groupes de ressources et à toutes les ressources au sein de cet abonnement.
- **Groupe de ressources** : un rôle RBAC attribué au niveau d’un groupe de ressources s’applique à toutes les ressources de ce groupe de ressources.
- **Ressource spécifique** : un rôle RBAC attribué pour une ressource spécifique s’applique à cette ressource. Dans ce cas, la ressource est un coffre de clés spécifique.

Il existe plusieurs rôles prédéfinis. Si un rôle prédéfini ne répond pas à vos besoins, vous pouvez définir votre propre rôle. Pour plus d’informations, consultez [RBAC : pour les ressources Azure](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Si un utilisateur dispose d’autorisations `Contributor` sur un plan de gestion de coffre de clés, il peut s’accorder lui-même l’accès au plan de données en définissant une stratégie d’accès Key Vault. Vous devez contrôler étroitement qui dispose d’un accès accordé par le rôle `Contributor` à vos coffres de clés. Vérifiez que seules les personnes autorisées peuvent accéder à et gérer vos coffres de clés, vos clés, vos secrets et vos certificats.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Plan de données et stratégies d’accès

Vous accordez l’accès au plan de données en définissant des stratégies d’accès Key Vault pour un coffre de clés. Pour définir ces stratégies d’accès, un utilisateur, un groupe ou une application doit disposer d’autorisations `Contributor` pour le plan de gestion de ce coffre de clés.

Vous accordez l’accès à un utilisateur, un groupe ou une application afin d’exécuter des opérations spécifiques sur les clés ou secrets d’un coffre de clés. Key Vault prend en charge jusqu’à 1 024 entrées de stratégie d’accès pour un coffre de clés. Pour accorder l’accès au plan de données à plusieurs utilisateurs, créez un groupe de sécurité Azure AD et ajoutez des utilisateurs à ce groupe.

<a id="key-vault-access-policies"></a> Les stratégies d’accès Key Vault accordent des autorisations distinctement aux clés, secrets et certificats. Vous pouvez uniquement accorder un accès utilisateur à des clés (pas à des secrets). Les autorisations d’accès aux clés, secrets ou certificats sont définies au niveau du coffre. Les stratégies d’accès Key Vault ne prennent pas en charge les autorisations granulaires au niveau des objets, comme une clé, un secret ou un certificat spécifique. Pour définir des stratégies d’accès pour un coffre de clés, utilisez le [portail Azure](https://portal.azure.com/), l’interface [Azure CLI](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou les [API REST de gestion Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Les stratégies d’accès Key Vault s’appliquent au niveau du coffre. Quand un utilisateur est autorisé à créer et à supprimer des clés, il peut effectuer ces opérations sur toutes les clés de ce coffre de clés.
>

Vous pouvez limiter l’accès au plan de données en utilisant des [points de terminaison de service de réseau virtuel pour Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Vous pouvez configurer [des pare-feu et des règles de réseau virtuel](key-vault-network-security.md) pour mettre en place une couche de sécurité supplémentaire.

## <a name="example"></a>Exemple

Dans cet exemple, nous développons une application qui utilise un certificat pour TLS/SSL, Stockage Azure pour stocker les données et une clé RSA 2 048 bits pour les opérations de signature. Notre application s’exécute dans une machine virtuelle Azure (ou un groupe de machines virtuelles identiques). Nous pouvons utiliser un coffre de clés pour stocker les secrets de l’application. Nous pouvons stocker le certificat de démarrage utilisé par l’application pour s’authentifier auprès d’Azure AD.

Nous avons besoin d’accéder aux clés et aux secrets stockés suivants :
- **Certificat TLS/SSL** : Utilisé pour TLS/SSL.
- **Clé de stockage** : utilisée pour accéder au compte de stockage.
- **Clé RSA 2 048 bits** : utilisée pour les opérations de signature.
- **Certificat Bootstrap** : utilisé pour s’authentifier auprès d’Azure AD. Une fois l’accès accordé, vous pouvez récupérer la clé de stockage et utiliser la clé RSA pour la signature.

Nous devons définir les rôles suivants pour spécifier qui peut gérer, déployer et auditer notre application :
- **Équipe de sécurité** : personnel informatique du bureau du chef de la sécurité ou contributeurs équivalents. L’équipe de sécurité est chargée de protéger les secrets. Les secrets peuvent inclure des certificats TLS/SSL, des clés RSA pour la signature, des chaînes de connexion et des clés de compte de stockage.
- **Développeurs et opérateurs** : équipe qui développe l’application et la déploie dans Azure. Les membres de cette équipe ne font pas partie de l’équipe de sécurité. Ils ne doivent pas avoir accès aux données sensibles, notamment les certificats TLS/SSL et les clés RSA. Seule l’application qu’ils déploient doit avoir accès à ces données sensibles.
- **Auditeurs** : ce rôle s’applique aux contributeurs qui ne sont pas membres de l’équipe de développement ou du personnel informatique général. Ils passent en revue l’utilisation et la maintenance des certificats, clés et secrets pour assurer la conformité aux normes de sécurité.

Il existe un autre rôle qui dépasse le cadre de notre application : l’administrateur d’abonnement (ou groupe de ressources). L’administrateur d’abonnement configure les autorisations d’accès initiales pour l’équipe de sécurité. Il accorde l’accès à l’équipe de sécurité à l’aide d’un groupe de ressources comprenant les ressources requises par l’application.

Nous devons autoriser les opérations suivantes pour nos rôles :

**Équipe de sécurité**
- Créer des coffres de clés
- Activer la journalisation Key Vault
- Ajouter des clés et des secrets
- Créer des sauvegardes des clés pour la reprise d’activité
- Définir des stratégies d’accès Key Vault pour autoriser des utilisateurs et des applications à effectuer des opérations spécifiques
- Regénérer périodiquement les clés et les secrets

**Développeurs et opérateurs**
- Obtenir auprès de l’équipe de sécurité des références aux certificats TLS/SSL et de démarrage (empreintes numériques), à la clé de stockage (URI de secret) et à la clé RSA (URI de clé) pour la signature.
- Développer et déployer l’application pour accéder par programmation aux clés et aux secrets

**Auditeurs**
- Examiner les journaux d’activité Key Vault pour confirmer l’utilisation correcte des clés et des secrets et la conformité aux normes en matière de sécurité des données

Le tableau suivant récapitule les autorisations d’accès pour nos rôles et notre application.

| Role | Autorisations de plan de gestion | Autorisations de plan de données |
| --- | --- | --- |
| Équipe de sécurité | Contributeur Key Vault | Clés : sauvegarde, création, suppression, obtention, importation, énumération, restauration<br>Secrets : toutes les opérations |
| Développeurs et&nbsp;opérateurs | Autorisation de déploiement Key Vault<br><br> **Remarque** : Cette autorisation permet aux machines virtuelles déployées de récupérer les secrets d’un coffre de clés. | None |
| Auditeurs | None | Clés : énumération<br>Secrets : énumération<br><br> **Remarque** : Cette autorisation permet aux auditeurs d’inspecter les attributs (étiquettes, dates d’activation, dates d’expiration) pour les clés et secrets non émis dans les journaux d’activité. |
| Application | None | Clés : énumération<br>Secrets : obtention |

Une fois les autorisations Key Vault définies, vous devez accorder aux trois rôles d’équipe l’accès à d’autres ressources. Pour déployer des machines virtuelles (ou la fonctionnalité Web Apps d’Azure App Service), les développeurs et opérateurs ont besoin d’un accès `Contributor` à ces types de ressources. Les auditeurs ont besoin d’un accès en lecture au compte de stockage où les journaux d’activité Key Vault sont stockés.

Pour plus d’informations sur le déploiement des certificats, clés d’accès et secrets par programmation, consultez ces ressources :
- Découvrez comment [déployer des certificats sur des machines virtuelles à partir d’un coffre de clés géré par un client](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (billet de blog).
- Téléchargez les [exemples de client Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343). Ce contenu montre comment utiliser un certificat de démarrage pour s’authentifier auprès d’Azure AD afin d’accéder à un coffre de clés.

Vous pouvez accorder la plupart des autorisations d’accès à l’aide du portail Azure. Pour accorder des autorisations granulaires, vous pouvez utiliser Azure PowerShell ou l’interface Azure CLI.

Les extraits PowerShell présentés dans cette section sont générés avec les hypothèses suivantes :
- L’administrateur Azure AD a créé des groupes de sécurité pour représenter les trois rôles : Contoso Security Team, Contoso App DevOps et Contoso App Auditors. L’administrateur a ajouté des utilisateurs à leurs groupes respectifs.
- Toutes les ressources se trouvent dans le groupe de ressources **ContosoAppRG**.
- Les journaux d’activité Key Vault sont stockés dans le compte de stockage **contosologstorage**.
- Le coffre de clés **ContosoKeyVault** et le compte de stockage **contosologstorage** sont dans le même emplacement Azure.

L’administrateur d’abonnement attribue les rôles `key vault Contributor` et `User Access Administrator` à l’équipe de sécurité. Ces rôles permettent à l’équipe de sécurité de gérer l’accès à d’autres ressources et coffres de clés dans le groupe de ressources **ContosoAppRG**.

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

L’équipe de sécurité crée un coffre de clés et configure la journalisation et les autorisations d’accès. Pour obtenir des détails sur les autorisations des stratégies d’accès Key Vault, consultez [À propos des clés, des secrets et des certificats Azure Key Vault](about-keys-secrets-and-certificates.md).

```powershell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Nos rôles personnalisés définis peuvent uniquement être attribués à l’abonnement où le groupe de ressources **ContosoAppRG** est créé. Pour utiliser un rôle personnalisé pour d’autres projets dans d’autres abonnements, ajoutez les autres abonnements à l’étendue du rôle.

Pour notre équipe DevOps, l’attribution de rôles personnalisés pour l’autorisation `deploy/action` de coffre de clés est limitée au groupe de ressources. Seules les machines virtuelles créées dans le groupe de ressources **ContosoAppRG** sont autorisées à accéder aux secrets (certificats TLS/SSL et de démarrage). Les machines virtuelles créées dans d’autres groupes de ressources par un membre DevOps ne peuvent pas accéder à ces secrets, même si la machine virtuelle a les URI des secrets.

Notre exemple décrit un scénario simple. Les scénarios réels peuvent être plus complexes. Vous pouvez ajuster les autorisations à votre coffre de clés selon vos besoins. Nous partons du principe que l’équipe de sécurité fournit les références aux clés et aux secrets (URI et empreintes) dont se sert l’équipe DevOps dans ses applications. Les développeurs et les opérateurs n’ont besoin d’aucun accès au plan de données. Nous avons vu comment sécuriser votre coffre de clés. Une considération similaire doit être accordée pour sécuriser [vos machines virtuelles](https://azure.microsoft.com/services/virtual-machines/security/), vos [comptes de stockage](../storage/blobs/security-recommendations.md) et d’autres ressources Azure.

> [!NOTE]
> Cet exemple montre comment l’accès au coffre de clés est verrouillé en production. Les développeurs doivent avoir leur propre abonnement ou groupe de ressources avec des autorisations complètes pour gérer leurs coffres de clés, les machines virtuelles et le compte de stockage où ils développent l’application.

Nous vous recommandons vivement de renforcer la sécurité de l’accès à votre coffre de clés en [configurant des pare-feu et des réseaux virtuels Key Vault](key-vault-network-security.md).

## <a name="resources"></a>Ressources

* [Contrôle d’accès en fonction du rôle (RBAC) Azure AD](../role-based-access-control/role-assignments-portal.md)

* [Contrôle d’accès en fonction du rôle (RBAC) : Rôles intégrés](../role-based-access-control/built-in-roles.md)

* [Comprendre les déploiements Resource Manager et classiques](../azure-resource-manager/management/deployment-models.md)

* [Gérer RBAC avec Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)

* [Gérer RBAC avec l’API REST](../role-based-access-control/role-assignments-rest.md)

* [RBAC pour Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Cette vidéo sur la conférence 2015 Microsoft Ignite parle des fonctionnalités de gestion des accès et de création de rapport dans Azure. Elle explore également les bonnes pratiques pour sécuriser l’accès aux abonnements Azure à l’aide d’Azure Active Directory.

* [Autoriser l’accès aux applications web avec OAuth 2.0 et Azure AD](../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [API REST de gestion des coffres de clés](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Ce document contient des informations de référence sur les API REST permettant de gérer votre coffre de clés par programmation, notamment la définition de la stratégie d’accès au coffre de clés.

* [API REST de Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Contrôle d’accès aux clés](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

* [Contrôle d’accès aux secrets](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

* [Définir](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) et [supprimer](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) la stratégie d’accès Key Vault avec PowerShell

## <a name="next-steps"></a>Étapes suivantes

Configurez [des pare-feu et des réseaux virtuels Azure Key Vault](key-vault-network-security.md).

Pour obtenir un tutoriel destiné aux administrateurs, consultez [Présentation d’Azure Key Vault](key-vault-overview.md).

Pour plus d’informations sur l’utilisation de la journalisation du coffre de clés, consultez [journalisation d’Azure Key Vault](key-vault-logging.md).

Pour plus d’informations sur l’utilisation des clés et des secrets avec Azure Key Vault, consultez [À propos des clés et des secrets](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Pour toute question concernant Key Vault, rendez-vous sur les [forums](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
