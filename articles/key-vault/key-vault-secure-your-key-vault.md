---
title: Sécuriser votre coffre de clés Azure - Azure Key Vault | Microsoft Docs
description: Gérez les autorisations d’accès à Azure Key Vault, aux clés et aux secrets. Couvre le modèle d’authentification et d’autorisation de Key Vault, et explique comment sécuriser votre coffre de clés.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 9877698c8c6af68c5ffd88dab37150274ce87b37
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077332"
---
# <a name="secure-your-key-vault"></a>Sécuriser votre coffre de clés

Azure Key Vault est un service cloud qui protège les clés et secrets de chiffrement (comme les certificats, les chaînes de connexion et les mots de passe). Comme il s’agit de données sensibles et critiques, vous devez sécuriser l’accès à vos coffres de clés en acceptant seulement les applications et les utilisateurs autorisés. Cet article fournit une vue d’ensemble du modèle d’accès aux coffres de clés. Il décrit l’authentification et l’autorisation, puis explique comment sécuriser l’accès.

## <a name="overview"></a>Vue d’ensemble

L’accès à un coffre de clés est contrôlé via deux interfaces distinctes : le plan de gestion et le plan de données. 
Le **plan de gestion** porte sur la gestion du coffre, par exemple la création d’un coffre, sa mise à jour et sa suppression. 
Le **plan de données** traite des secrets à l’intérieur d’un coffre : créer, mettre à jour, supprimer et lire un secret dans le coffre. Pour ces deux plans, une autorisation et une authentification correctes sont obligatoires avant qu’un appelant (un utilisateur ou une application) puisse accéder au coffre de clés. L’authentification établit l’identité de l’appelant, tandis que l’autorisation détermine les opérations que ce dernier est autorisé à effectuer.

Pour l’authentification, le plan de gestion et le plan de données font tous deux appel à Azure Active Directory. Pour l’autorisation, le plan de gestion utilise le contrôle d’accès en fonction du rôle (RBAC), tandis que le plan de données s’appuie sur la stratégie d’accès au coffre de clés.

Voici un aperçu des sujets traités :

[Authentification à l’aide d’Azure Active Directory](#authentication-using-azure-active-directory) : cette section explique comment un appelant s’authentifie auprès d’Azure Active Directory pour accéder à un coffre de clés via le plan de gestion et le plan de données. 

Pour l’authentification, les deux plans utilisent Azure Active Directory (Azure AD). Pour l’autorisation, le plan de gestion utilise le contrôle d’accès en fonction du rôle (RBAC), tandis que le plan de données s’appuie sur la stratégie d’accès à Key Vault.

## <a name="authenticate-by-using-azure-active-directory"></a>S’authentifier avec Azure Active Directory

Quand vous créez un coffre de clés dans un abonnement Azure, il est automatiquement associé au locataire Azure AD de cet abonnement. Tous les appelants doivent être inscrits dans ce locataire et doivent s’authentifier pour accéder au coffre de clés. C’est obligatoire pour l’accès au plan de gestion et au plan de données. Dans les deux cas, une application peut accéder à Key Vault de deux manières :

* **Accès de l’utilisateur et de l’application** : utilisé avec les applications qui accèdent à Key Vault pour le compte d’un utilisateur connecté. Azure PowerShell et le portail Azure sont des exemples de ce type d’accès. Vous pouvez accorder l’accès aux utilisateurs de deux manières :

  - Accès à Key Vault à partir d’une application.
  - Accès à Key Vault seulement quand ils utilisent une application spécifique (appelée « identité composée »).

* **Accès de l’application seule** : utilisé avec les applications qui exécutent des services démons ou des travaux en arrière-plan. L’identité de l’application se voit octroyer l’accès au coffre de clés.

Dans les deux types d’applications, l’application s’authentifie auprès d’Azure Active Directory en utilisant une des [méthodes d’authentification prises en charge](../active-directory/develop/authentication-scenarios.md) et obtient un jeton. La méthode d’authentification utilisée dépend du type d’application. L’application utilise alors ce jeton, puis envoie une demande d’API REST à Key Vault. Les demandes de plan de gestion sont acheminées via un point de terminaison Azure Resource Manager. Quand elle accède au plan de données, l’application s’adresse directement à un point de terminaison Key Vault. Pour plus d’informations, reportez-vous au [flux complet de l’authentification](../active-directory/develop/v1-protocols-oauth-code.md). 

Le nom de la ressource pour laquelle l’application demande un jeton varie selon le plan auquel l’application accède. Le nom de la ressource est un point de terminaison du plan de gestion ou un point de terminaison du plan de données, en fonction de l’environnement Azure. Pour plus d’informations, consultez le tableau plus loin dans cet article.

Avoir un même mécanisme pour l’authentification sur les deux plans présente certains avantages :

* Les organisations peuvent contrôler de manière centralisée l’accès à tous leurs coffres de clés.
* Si un utilisateur s’en va, il perd instantanément l’accès à tous les coffres de clés de l’organisation.
* Les organisations peuvent personnaliser l’authentification à l’aide des options offertes par Azure Active Directory (par exemple en activant l’authentification multifacteur pour renforcer la sécurité).

## <a name="the-management-plane-and-the-data-plane"></a>Plan de gestion et plan de données

Utilisez le plan de gestion pour gérer le coffre de clés lui-même. Cela inclut des opérations comme la gestion des attributs et la définition de stratégies d’accès du plan de données. Utilisez le plan de données pour ajouter, supprimer, modifier et utiliser les clés, les secrets et les certificats stockés dans le coffre de clés.

Accédez aux interfaces du plan de gestion et du plan de données via les différents points de terminaison listés dans le tableau suivant. La deuxième colonne du tableau indique les noms DNS de ces points de terminaison dans les différents environnements Azure. La troisième colonne décrit les opérations que vous pouvez effectuer depuis chaque plan d’accès. Chaque plan d’accès a également son propre mécanisme de contrôle d’accès. Le contrôle d’accès du plan de gestion est défini en utilisant le contrôle d’accès en fonction du rôle Azure Resource Manager (RBAC). Le contrôle d’accès du plan de données est défini à l’aide d’une stratégie d’accès à Key Vault.

| Plan d’accès | Points de terminaison d’accès | Opérations | Mécanisme de contrôle d’accès |
| --- | --- | --- | --- |
| Plan de gestion |**Mondial :**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government :**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany :**<br>  management.microsoftazure.de:443 |Créer/lire/mettre à jour/supprimer un coffre de clés <br> Définir des stratégies d’accès pour un coffre de clés<br>Définir des étiquettes pour un coffre de clés |RBAC Azure Resource Manager |
| Plan de données |**Mondial :**<br> &lt;nom du coffre&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;nom du coffre&gt;.vault.azure.cn:443<br><br> **Azure US Government :**<br> &lt;nom du coffre&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany :**<br> &lt;nom du coffre&gt;.vault.microsoftazure.de:443 |Pour les clés : Decrypt, Encrypt, UnwrapKey, WrapKey, Verify, Sign, Get, List, Update, Create, Import, Delete, Backup, Restore<br><br> Pour les secrets : Get, List, Set, Delete |Stratégie d’accès au coffre de clés |

Les contrôles d’accès au plan de gestion et au plan de données fonctionnent indépendamment. Par exemple, si vous souhaitez accorder un accès à une application pour utiliser des clés dans un coffre de clés, vous devez accorder un accès au plan de données seulement. Vous accordez l’accès via des stratégies d’accès au coffre de clés. À l’inverse, un utilisateur qui a besoin de lire les propriétés et les étiquettes du coffre, mais pas d’accéder aux données (clés, secrets ou certificats), nécessite seulement un accès au plan de gestion. Vous accordez l’accès en attribuant l’accès en lecture à l’utilisateur avec RBAC.

## <a name="management-plane-access-control"></a>Contrôle d’accès au plan de gestion

Le plan de gestion se compose d’opérations qui affectent le coffre de clés proprement dit, comme :

- Création ou suppression d’un coffre de clés.
- Obtention de la liste de coffres d’un abonnement.
- Récupération des propriétés du coffre de clés (comme les références SKU et les étiquettes).
- Définition de stratégies d’accès au coffre de clés qui contrôlent l’accès des utilisateurs et des applications aux clés et aux secrets.

Le contrôle d’accès au plan de gestion utilise la fonctionnalité RBAC.  

### <a name="role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle

Chaque abonnement Azure a une instance Azure AD. Vous accordez l’accès aux utilisateurs, aux groupes et aux applications de cet annuaire pour gérer les ressources de l’abonnement Azure qui utilisent le modèle de déploiement Azure Resource Manager. Ce type de contrôle d’accès est appelé RBAC. Pour gérer cet accès, vous pouvez utiliser le [Portail Azure](https://portal.azure.com/), les [outils de l’interface de ligne de commande Azure](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) ou les [API REST Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Vous créez un coffre de clés dans un groupe de ressources et vous contrôlez l’accès au plan de gestion avec Azure AD. Par exemple, vous pouvez autoriser des utilisateurs ou un groupe à gérer les coffres de clés d’un groupe de ressources.

Vous pouvez accorder l’accès aux utilisateurs, groupes et applications d’une étendue donnée en attribuant les rôles RBAC appropriés. Par exemple, pour accorder un accès à un utilisateur pour gérer des coffres de clés, vous attribuez un rôle « Contributeur Key Vault » prédéfini à cet utilisateur dans une étendue spécifique. L’étendue dans ce cas correspond à un abonnement, à un groupe de ressources ou à un coffre de clés spécifique. Un rôle affecté au niveau d’un abonnement s’applique à tous les groupes de ressources et à toutes les ressources au sein de cet abonnement. Un rôle attribué au niveau d’un groupe de ressources s’applique à toutes les ressources de ce groupe de ressources. Un rôle attribué pour une ressource spécifique s’applique à cette ressource. Il existe plusieurs rôles prédéfinis (consultez [RBAC : rôles intégrés](../role-based-access-control/built-in-roles.md)). Si un rôle prédéfini ne répond pas à vos besoins, vous pouvez définir votre propre rôle.

> [!IMPORTANT]
> Si un utilisateur dispose des autorisations Contributeur pour le plan de gestion d’un coffre de clés, il peut s’accorder à lui-même l’accès au plan de données en définissant une stratégie d’accès au coffre de clés. Par conséquent, vous devez contrôler étroitement qui a un accès Contributeur à vos coffres de clés. Vérifiez que seules les personnes autorisées peuvent accéder à et gérer vos coffres de clés, vos clés, vos secrets et vos certificats.
>

## <a name="data-plane-access-control"></a>Contrôle d’accès au plan de données

Les opérations du plan de données des coffres de clés s’appliquent aux objets stockés, comme les clés, les secrets et les certificats. Les principales opérations sont : créer, importer, mettre à jour, lister, sauvegarder et restaurer des clés. Les opérations de chiffrement sont : signer, vérifier, chiffrer, déchiffrer, encapsuler (wrap), désencapsuler (unwrap), définir des étiquettes et d’autres attributs pour les clés. De même, les opérations sur les secrets incluent obtenir, définir, lister et supprimer.

Vous accordez l’accès au plan de données en définissant des stratégies d’accès pour un coffre de clés. Un utilisateur, un groupe ou une application doit disposer des autorisations Contributeur pour le plan de gestion d’un coffre de clés afin de pouvoir définir des stratégies d’accès pour ce coffre de clés. Vous accordez l’accès à un utilisateur, un groupe ou une application afin d’effectuer des opérations spécifiques pour les clés ou les secrets d’un coffre de clés. Key Vault prend en charge jusqu’à 1 024 entrées de stratégie d’accès pour un coffre de clés. Pour accorder l’accès au plan de données à plusieurs utilisateurs, créez un groupe de sécurité Azure AD et ajoutez les utilisateurs à ce groupe.

### <a name="key-vault-access-policies"></a>Stratégies d’accès Key Vault

Les stratégies d’accès Key Vault accordent des autorisations distinctement aux clés, aux secrets et aux certificats. Par exemple, vous pouvez donner un accès uniquement aux clés à un utilisateur et aucune autorisation pour les secrets. Les autorisations d’accès aux clés, aux secrets ou aux certificats sont définies au niveau du coffre. Une stratégie d’accès à Key Vault ne prend pas en charge les autorisations granulaires au niveau des objets, comme une clé, un secret ou un certificat spécifique. Vous pouvez utiliser le [portail Azure](https://portal.azure.com/), les [outils Azure CLI](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) ou les [API REST de gestion de Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx) pour définir des stratégies d’accès pour un coffre de clés.

> [!IMPORTANT]
> Les stratégies d’accès Key Vault s’appliquent au niveau du coffre. Par exemple, lorsqu’un utilisateur est autorisé à créer et à supprimer des clés, il peut effectuer ces opérations sur toutes les clés de ce coffre de clés.

Outre l’utilisation de stratégies d’accès, vous pouvez également limiter les accès au plan de données en utilisant des [points de terminaison de service de réseau virtuel pour Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Vous configurez [des pare-feux et des règles de réseau virtuel](key-vault-network-security.md) pour une couche supplémentaire de sécurité.

## <a name="example"></a>Exemples

Supposons que vous développez une application qui utilise un certificat pour SSL, le Stockage Azure pour stocker les données et une clé RSA 2 048 bits pour les opérations de signature. Supposons également que cette application s’exécute sur une machine virtuelle Azure (ou un groupe de machines virtuelles identiques). Vous pouvez utiliser un coffre de clés pour stocker tous les secrets de l’application et stocker le certificat Bootstrap utilisé par l’application pour s’authentifier auprès d’Azure AD.

Voici un récapitulatif des types de clés et de secrets stockés :

* **Certificat SSL** : Utilisé pour SSL.
* **Clé de stockage** : utilisée pour accéder au compte de stockage.
* **Clé RSA de 2048 bits** : utilisée pour les opérations de signature.
* **Certificat Bootstrap** : utilisé pour s’authentifier auprès d’Azure AD. Une fois l’accès accordé, vous pouvez extraire la clé de stockage et utiliser la clé RSA pour la signature.

Découvrons à présent les personnes qui gèrent, déploient et auditent cette application. Nous utiliserons trois rôles dans cet exemple.

* **Équipe de sécurité** : il s’agit généralement du personnel informatique du responsable de la sécurité (CSO, Chief Security Officer) ou équivalent. Cette équipe est responsable de garder les secrets en sécurité. Par exemple, les certificats SSL, les clés RSA utilisées pour la signature, les chaînes de connexion et les clés de compte de stockage.
* **Développeurs/opérateurs** : il s’agit des personnes qui développent l’application et la déploient dans Azure. En règle générale, ils ne font pas partie de l’équipe de sécurité et donc ne doivent pas avoir accès aux données sensibles, telles que les certificats SSL et les clés RSA. Seule l’application qu’ils déploient doit avoir accès à ces objets.
* **Auditeurs** : il s’agit le plus souvent d’un autre groupe de personnes qui ne font pas partie des développeurs et du personnel informatique général. Ils sont chargés de vérifier l’utilisation et la maintenance des certificats, des clés et des secrets pour assurer la conformité aux normes de sécurité. 

Il existe un autre rôle qui ne rentre pas dans le cadre de cette application, mais qui mérite quand même d’être mentionné. Il s’agit de l’administrateur d’abonnement (ou de groupe de ressources). L’administrateur d’abonnement définit les premières autorisations d’accès pour l’équipe de sécurité. L’administrateur de l’abonnement accorde l’accès à l’équipe de sécurité en utilisant un groupe de ressources qui contient les ressources nécessaires à cette application.

Voyons maintenant quelles actions sont effectuées par chaque rôle dans le contexte de cette application.

* **Équipe de sécurité**
  * Crée des coffres de clés.
  * Active la journalisation Key Vault.
  * Ajoute des clés/secrets.
  * Crée une sauvegarde des clés pour la reprise d’activité.
  * Définit une stratégie d’accès Key Vault pour autoriser des utilisateurs et des applications à effectuer des opérations spécifiques.
  * Déploie périodiquement des clés/secrets.
* **Développeurs/opérateurs**
  * Obtiennent des références aux certificats Bootstrap et SSL (empreintes), à la clé de stockage (URI de secret) et à la clé de signature (URI de clé) auprès de l’équipe de sécurité.
  * Développent et déploient une application qui accède par programmation aux clés et aux secrets.
* **Auditeurs**
  * Examinent les journaux d’utilisation pour vérifier l’utilisation correcte des clés/secrets et la conformité aux normes en matière de sécurité des données.

Voyons maintenant quelles autorisations d’accès sont requises pour chaque rôle et l’application pour effectuer les tâches qui leur sont affectées. 

| Rôle utilisateur | Autorisations de plan de gestion | Autorisations de plan de données |
| --- | --- | --- |
| Équipe de sécurité |Contributeur Key Vault |Clés : sauvegarde, création, suppression, obtention, importation, énumération, restauration <br> Secrets : toutes |
| Développeurs/opérateurs |Autorisation de déploiement Key Vault, de sorte que les machines virtuelles qu’ils déploient puissent récupérer les secrets dans le coffre de clés |Aucun |
| Auditeurs |Aucun |Clés : énumération<br>Secrets : énumération |
| Application |Aucun |Clés : énumération<br>Secrets : obtention |

> [!NOTE]
> Les auditeurs ont besoins d’une autorisation d’énumération pour les clés et les secrets, afin de pouvoir inspecter les attributs des clés et des secrets qui ne sont pas inclus dans les journaux. Ces attributs incluent des étiquettes, et des dates d’activation et d’expiration.
> 
> 

En plus des autorisations Key Vault, les trois rôles ont aussi besoin d’un accès à d’autres ressources. Par exemple, pour être en mesure de déployer des machines virtuelles (ou la fonctionnalité Web Apps d’Azure App Service), les développeurs et les opérateurs ont également besoin d’un accès Contributeur à ces types de ressources. Les auditeurs ont besoin de l’accès en lecture au compte de stockage où les journaux Key Vault sont stockés.

Comme le sujet de cet article est la sécurisation de l’accès à votre coffre de clés, nous illustrons seulement les concepts qui s’y rapportent. Les détails concernant le déploiement des certificats, ou l’accès par programmation aux clés et aux secrets, sont abordés ailleurs. Exemple :

- Le déploiement des certificats stockés dans Key Vault sur des machines virtuelles est couvert dans le billet de blog [Deploy Certificates to VMs from customer-managed Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).
- La page de [téléchargement d’exemples de clients Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) montre comment utiliser un certificat Bootstrap pour s’authentifier auprès d’Azure AD pour accéder à un coffre de clés.

Vous pouvez accorder la plupart des autorisations d’accès à l’aide du portail Azure. Pour accorder des autorisations granulaires, il peut être nécessaire d’utiliser Azure PowerShell ou l’interface CLI pour obtenir le résultat souhaité. 

Les extraits de code PowerShell suivants supposent que :

* L’administrateur Active Directory Azure a créé des groupes de sécurité qui représentent les trois rôles (Contoso Security Team, Contoso App Devops et Contoso App Auditors). L’administrateur a également ajouté les utilisateurs aux groupes dont ils font partie.
* **ContosoAppRG** est le groupe de ressources où toutes les ressources se trouvent. **contosologstorage** est l’emplacement de stockage des journaux. 
* Le coffre de clés **ContosoKeyVault** et le compte de stockage utilisé pour les journaux Key Vault **contosologstorage** doivent se trouver dans le même emplacement Azure.

L’administrateur de l’abonnement attribue d’abord les rôles `key vault Contributor` et `User Access Administrator` à l’équipe de sécurité. Ces rôles permettent à l’équipe de sécurité de gérer l’accès aux autres ressources et de gérer les coffres de clés du groupe de ressources ContosoAppRG.

```PowerShell
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Le script suivant montre comment l’équipe de sécurité peut créer un coffre de clés et définir des autorisations d’accès et de journalisation. Pour des détails sur les autorisations des stratégies d’accès Key Vault, consultez [Présentation des clés, des secrets et des certificats Azure Key Vault](about-keys-secrets-and-certificates.md).

```PowerShell
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Le rôle personnalisé défini peut être attribué seulement à l’abonnement où le groupe de ressources `ContosoAppRG` a été créé. Si les mêmes rôles personnalisés sont utilisés pour d’autres projets dans d’autres abonnements, des abonnements supplémentaires peuvent être ajoutés.

L’attribution de rôles personnalisés aux développeurs/opérateurs pour l’autorisation « déploiement/action » se limite au groupe de ressources. Ainsi, seules les machines virtuelles créées dans le groupe de ressources `ContosoAppRG` peuvent avoir accès aux secrets (le certificat SSL et le certificat Bootstrap). Les machines virtuelles créées dans un autre groupe de ressources par un membre de l’équipe des développeurs et des opérateurs n’ont pas accès à ces secrets, même si elles ont les URI des secrets.

Cet exemple illustre un scénario simple. Dans la pratique, vous pouvez rencontrer des scénarios plus complexes, et vous pouvez ajuster les autorisations sur votre coffre de clés selon vos besoins. Dans notre exemple, nous partons du principe que l’équipe de sécurité fournit les références de clé et de secret (URI et empreintes) que les développeurs/opérateurs doivent référencer dans leurs applications. Les développeurs et les opérateurs n’ont besoin d’aucun accès au plan de données. Cet exemple se concentre sur la sécurisation de votre coffre de clés. Une considération similaire doit être accordée pour sécuriser [vos machines virtuelles](https://azure.microsoft.com/services/virtual-machines/security/), vos [comptes de stockage](../storage/common/storage-security-guide.md) et d’autres ressources Azure.

> [!NOTE]
> Cet exemple montre comment l’accès au coffre de clés sera verrouillé en production. Les développeurs doivent avoir leur propre abonnement ou groupe de ressources avec des autorisations complètes pour gérer leurs coffres de clés, les machines virtuelles et le compte de stockage où ils développent l’application.

Il est fortement recommandé de sécuriser l’accès à votre coffre de clés en [configurant des pare-feux et des réseaux virtuels Key Vault](key-vault-network-security.md).

## <a name="resources"></a>Ressources

* [Contrôle d’accès en fonction du rôle Azure Active Directory](../role-based-access-control/role-assignments-portal.md)
  
* [Contrôle d’accès en fonction du rôle (RBAC) : rôles intégrés](../role-based-access-control/built-in-roles.md).
  
* [Présentation du déploiement de Resource Manager et du déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md)
  
* [Gérer le contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
  
* [Gestion du contrôle d’accès basé sur les rôles avec l’API REST](../role-based-access-control/role-assignments-rest.md)
  
* [Role-based access control for Microsoft Azure from Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Cette vidéo sur la conférence 2015 Microsoft Ignite parle des fonctionnalités de gestion des accès et de création de rapport dans Azure. Elle explore également les bonnes pratiques pour sécuriser l’accès aux abonnements Azure à l’aide d’Azure Active Directory.
* [Autoriser l’accès aux applications web avec OAuth 2.0 et Azure AD](../active-directory/develop/v1-protocols-oauth-code.md)
  
* [API REST de gestion des coffres de clés](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Ce document contient des informations de référence sur les API REST permettant de gérer votre coffre de clés par programmation, notamment la définition de la stratégie d’accès au coffre de clés.
* [API REST de Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
* [Contrôle d’accès aux clés](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Contrôle d’accès aux secrets](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* [Définition](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) et [suppression](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy) de la stratégie d’accès Key Vault avec PowerShell
  
## <a name="next-steps"></a>Étapes suivantes

[Configurer des pare-feu et des réseaux virtuels Key Vault](key-vault-network-security.md)

Pour un didacticiel de prise en main d’un administrateur, consultez la page [Prise en main d’Azure Key Vault](key-vault-get-started.md).

Pour plus d’informations sur l’utilisation de la journalisation du coffre de clés, consultez [journalisation d’Azure Key Vault](key-vault-logging.md).

Pour plus d’informations sur l’utilisation des clés et des secrets avec Azure Key Vault, consultez [À propos des clés et des secrets](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Pour toute question concernant Key Vault, rendez-vous sur les [forums](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

