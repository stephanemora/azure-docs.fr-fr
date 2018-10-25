---
title: Sécuriser votre coffre de clés Azure | Microsoft Docs
description: Gérez les autorisations d’accès à un coffre de clés pour la gestion des clés, des secrets et du coffre de clés A	zure. Modèle d’authentification et d’autorisation d’un coffre de clés et procédure de sécurisation de votre coffre de clés.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: ambapat
ms.openlocfilehash: 4a1de3c011f1f8cfa1ea9246efad4ebb7f9e3a76
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364521"
---
# <a name="secure-your-key-vault"></a>Sécuriser votre coffre de clés
Azure Key Vault est un service cloud qui protège les clés et secrets de chiffrement (tels que les certificats, les chaînes de connexion et les mots de passe). Comme il s’agit de données sensibles et critiques, l’accès à vos coffres de clés doit être sécurisé en permettant uniquement aux applications et aux utilisateurs autorisés d’obtenir un accès. 

Cet article fournit une vue d’ensemble du modèle d’accès aux coffres de clés. Il décrit l’authentification et l’autorisation, puis explique comment sécuriser l’accès à un coffre de clés.

## <a name="overview"></a>Vue d’ensemble
L’accès à un coffre de clés est contrôlé via deux interfaces distinctes : le plan de gestion et le plan de données. Pour ces deux plans, une autorisation et une authentification correctes sont obligatoires avant qu’un appelant (un utilisateur ou une application) puisse accéder au coffre de clés. L’authentification établit l’identité de l’appelant, tandis que l’autorisation détermine les opérations que ce dernier est autorisé à effectuer.

Pour l’authentification, le plan de gestion et le plan de données font tous deux appel à Azure Active Directory. Pour l’autorisation, le plan de gestion utilise le contrôle d’accès en fonction du rôle (RBAC), tandis que le plan de données s’appuie sur la stratégie d’accès au coffre de clés.

Voici un aperçu des sujets traités :

[Authentification à l’aide d’Azure Active Directory](#authentication-using-azure-active-directory) : cette section explique comment un appelant s’authentifie auprès d’Azure Active Directory pour accéder à un coffre de clés via le plan de gestion et le plan de données. 

[Plan de gestion et plan de données](#management-plane-and-data-plane) : le plan de gestion et le plan de données sont deux plans d’accès utilisés pour accéder à votre coffre de clés. Chaque plan d’accès prend en charge des opérations spécifiques. Cette section décrit les points de terminaison d’accès, les opérations prises en charge et la méthode de contrôle d’accès utilisée par chaque plan. 

[Contrôle d’accès au plan de gestion](#management-plane-access-control) : dans cette section, nous examinons comment autoriser l’accès aux opérations du plan de gestion à l’aide du contrôle d’accès en fonction du rôle.

[Contrôle d’accès au plan de données](#data-plane-access-control) : cette section explique comment utiliser la stratégie d’accès au coffre de clés pour contrôler l’accès au plan de données.

[Exemple](#example) : cet exemple explique comment configurer le contrôle d’accès pour votre coffre de clés afin d’autoriser trois équipes différentes (l’équipe de sécurité, les développeurs/opérateurs et les auditeurs) à effectuer des tâches spécifiques pour développer, gérer et superviser une application dans Azure.

## <a name="authentication-using-azure-active-directory"></a>Authentification à l’aide d’Azure Active Directory
Quand un coffre de clés est créé dans un abonnement Azure, il est automatiquement associé au client Azure Active Directory de cet abonnement. Tous les appelants (utilisateurs et applications) doivent être inscrits à ce locataire et s’authentifier pour accéder au coffre de clés. C’est obligatoire pour l’accès au plan de gestion et au plan de données. Dans les deux cas, une application peut accéder au coffre de clés de deux manières :

* **Accès de l’utilisateur et de l’application** : utilisé avec les applications qui accèdent au coffre de clés pour le compte d’un utilisateur connecté. Azure PowerShell et le portail Azure sont des exemples de ce type d’accès. Vous pouvez accorder l’accès aux utilisateurs de deux manières : 
- Accorder l’accès aux utilisateurs pour qu’ils puissent accéder au coffre de clés à partir de n’importe quelle application.
- Accorder l’accès aux utilisateurs au coffre de clés seulement s’ils utilisent une application spécifique (appelée identité composée).

* **Accès de l’application seule** : utilisé avec les applications qui exécutent des services démons ou des travaux en arrière-plan. L’identité de l’application se voit octroyer l’accès au coffre de clés.

Dans les deux types d’applications, l’application s’authentifie auprès d’Azure Active Directory à l’aide de n’importe laquelle des [méthodes d’authentification prises en charge](../active-directory/develop/authentication-scenarios.md) et obtient un jeton. La méthode d’authentification utilisée dépend du type d’application. L’application utilise ce jeton, puis envoie une demande d’API REST au coffre de clés. Les demandes de plan de gestion sont acheminées via un point de terminaison Azure Resource Manager. Si elle accède au plan de données, l’application s’adresse directement à un point de terminaison de coffre de clés. Consultez d’autres informations sur le [flux d’authentification intégral](../active-directory/develop/v1-protocols-oauth-code.md). 

Le nom de la ressource pour laquelle l’application demande un jeton est différent selon que l’application accède au plan de gestion ou au plan de données. Par conséquent, en fonction de l’environnement Azure, le nom de la ressource correspond soit à un point de terminaison du plan de gestion, soit à un point de terminaison du plan de données, tel que spécifié dans le tableau de la section suivante.

L’utilisation d’un mécanisme unique pour l’authentification auprès des plans de gestion et de données présente des avantages spécifiques :

* Les organisations peuvent contrôler de manière centralisée l’accès à tous leurs coffres de clés.
* Si un utilisateur part, il perd instantanément l’accès à tous les coffres de clés de l’organisation.
* Les organisations peuvent personnaliser l’authentification à l’aide des options offertes par Azure Active Directory (par exemple, activer l’authentification multifacteur pour renforcer la sécurité).

## <a name="management-plane-and-data-plane"></a>Plan de gestion et plan de données
Azure Key Vault est un service Azure disponible via le modèle de déploiement Azure Resource Manager. Lorsque vous créez un coffre de clés, vous obtenez un conteneur virtuel pour stocker des objets sensibles tels que des clés, des secrets et des certificats. Vous pouvez effectuer des opérations spécifiques dans un coffre de clés en utilisant les interfaces du plan de gestion et du plan de données. Le plan de gestion est utilisé pour gérer le coffre de clés lui-même. Cela inclut des opérations comme la gestion des attributs et la définition de stratégies d’accès du plan de données. L’interface du plan de données permet d’ajouter, de supprimer, de modifier et d’utiliser les clés, les secrets et les certificats stockés dans le coffre de clés.

Vous pouvez accéder aux interfaces du plan de gestion et du plan de données via différents points de terminaison listés ci-dessous. La deuxième colonne indique les noms DNS de ces points de terminaison dans les différents environnements Azure. La troisième colonne décrit les opérations que vous pouvez effectuer depuis chaque plan d’accès. Chaque plan d’accès a également son propre mécanisme de contrôle d’accès. Le contrôle d’accès du plan de gestion est défini en utilisant le contrôle d’accès en fonction du rôle Azure Resource Manager (RBAC). Le contrôle d’accès du plan de données est défini à l’aide d’une stratégie d’accès au coffre de clés.

| Plan d’accès | Points de terminaison d’accès | Opérations | Mécanisme de contrôle d’accès |
| --- | --- | --- | --- |
| Plan de gestion |**Mondial :**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government :**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany :**<br> management.microsoftazure.de:443 |Créer/lire/mettre à jour/supprimer un coffre de clés <br> Définir des stratégies d’accès pour un coffre de clés<br>Définir des balises pour un coffre de clés |Contrôle d’accès en fonction du rôle (RBAC) Azure Resource Manager |
| Plan de données |**Mondial :**<br> &lt;nom du coffre&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;nom du coffre&gt;.vault.azure.cn:443<br><br> **Azure US Government :**<br> &lt;nom du coffre&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany :**<br> &lt;nom du coffre&gt;.vault.microsoftazure.de:443 |Pour les clés : Decrypt, Encrypt, UnwrapKey, WrapKey, Verify, Sign, Get, List, Update, Create, Import, Delete, Backup, Restore<br><br> Pour les secrets : Get, List, Set, Delete |Stratégie d’accès au coffre de clés |

Les contrôles d’accès au plan de gestion et au plan de données fonctionnent indépendamment. Par exemple, si vous souhaitez accorder un accès à une application pour utiliser des clés dans un coffre de clés, vous devez accorder un accès au plan de données seulement. L’accès est accordé par le biais de stratégies d’accès au coffre de clés. À l’inverse, un utilisateur qui a besoin de lire les propriétés et les balises du coffre, mais pas d’accéder aux données (clés, secrets ou certificats), nécessite un accès au plan de gestion uniquement. L’accès est accordé en attribuant l’accès en lecture à l’utilisateur à l’aide de RBAC.

## <a name="management-plane-access-control"></a>Contrôle d’accès au plan de gestion
Le plan de gestion se compose d’opérations qui affectent le coffre de clés proprement dit, comme :

- Création ou suppression d’un coffre de clés.
- Obtention de la liste de coffres d’un abonnement.
- Récupération des propriétés du coffre de clés (comme les référence SKU, les balises).
- Définition de stratégies d’accès au coffre de clés qui contrôlent l’accès des utilisateurs et des applications aux clés et aux secrets.

Le contrôle d’accès au plan de gestion utilise la fonctionnalité RBAC. Vous trouverez la liste complète des opérations de coffre de clés qui peuvent être effectuées via le plan de gestion dans le tableau de la section précédente. 

### <a name="role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle (RBAC)
À chaque abonnement Azure correspond un annuaire Azure Active Directory. Les utilisateurs, les groupes et les applications de cet annuaire peuvent être autorisés à gérer les ressources de l’abonnement Azure qui reposent sur le modèle de déploiement Azure Resource Manager. Ce type de contrôle d’accès porte le nom de contrôle d’accès en fonction du rôle (RBAC). Pour gérer cet accès, vous pouvez utiliser le [Portail Azure](https://portal.azure.com/), les [outils de l’interface de ligne de commande Azure](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) ou les [API REST Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Vous créez un coffre de clés dans un groupe de ressources et contrôlez l’accès au plan de gestion à l’aide d’Azure Active Directory. Par exemple, vous pouvez autoriser des utilisateurs ou un groupe à gérer les coffres de clés d’un groupe de ressources.

Vous pouvez accorder l’accès aux utilisateurs, groupes et applications d’une étendue donnée en attribuant les rôles RBAC appropriés. Par exemple, pour autoriser un utilisateur à gérer des coffres de clés, attribuez un rôle « Contributeur Key Vault » prédéfini à cet utilisateur dans une étendue spécifique. L’étendue dans ce cas correspond à un abonnement, à un groupe de ressources ou à un coffre de clés spécifique. Un rôle attribué au niveau d’un abonnement s’applique à tous les groupes de ressources et ressources au sein de cet abonnement. Un rôle attribué au niveau d’un groupe de ressources s’applique à toutes les ressources de ce groupe de ressources. Un rôle attribué pour une ressource spécifique s’applique à cette ressource. Il existe plusieurs rôles prédéfinis (voir [RBAC : rôles intégrés](../role-based-access-control/built-in-roles.md)). Si un rôle prédéfini ne répond pas à vos besoins, vous pouvez définir votre propre rôle.

> [!IMPORTANT]
> Notez que si un utilisateur dispose d’autorisations de Contributeur (RBAC) pour le plan de gestion d’un coffre de clés, il peut s’accorder à lui-même l’accès au plan de données en définissant la stratégie d’accès au coffre de clés qui contrôle l’accès au plan de données. Par conséquent, il est recommandé de contrôler étroitement quels utilisateurs disposent d’un accès « Contributeur » à vos coffres de clés afin de vous assurer que seules les personnes autorisées peuvent gérer vos coffres de clés, clés, secrets et certificats.
> 
> 

## <a name="data-plane-access-control"></a>Contrôle d’accès au plan de données
Les opérations du plan de données de coffre de clés s’appliquent aux objets stockés comme les clés, les secrets et les certificats. Les principales opérations sont : créer, importer, mettre à jour, lister, sauvegarder et restaurer des clés. Les opérations de chiffrement sont : signer, vérifier, chiffrer, déchiffrer, wrapper, unwrapper, définir des balises et autres attributs pour les clés. De même, les opérations sur les secrets incluent obtenir, définir, lister et supprimer.

L’accès au plan de données est octroyé en définissant des stratégies d’accès pour un coffre de clés. Un utilisateur, un groupe ou une application doit posséder des autorisations de Contributeur (RBAC) pour le plan de gestion d’un coffre de clés afin de pouvoir définir des stratégies d’accès pour ce coffre de clés. Un utilisateur, un groupe ou une application peut se voir autorisée à effectuer des opérations spécifiques pour les clés ou les secrets d’un coffre de clés. Un coffre de clés prend en charge jusqu’à 1 024 entrées de stratégie d’accès. Créez un groupe de sécurité Azure Active Directory et ajoutez des utilisateurs à ce groupe afin d’accorder l’accès au plan de données à plusieurs utilisateurs pour un coffre de clés.

### <a name="key-vault-access-policies"></a>Stratégies d’accès à un coffre de clés
Les stratégies d’accès à un coffre de clés accordent des autorisations s’appliquant soit aux clés, soit aux secrets, soit aux certificats. Par exemple, vous pouvez donner un accès uniquement aux clés à un utilisateur et aucune autorisation pour les secrets. Les autorisations d’accès aux clés, aux secrets ou aux certificats sont au niveau du coffre. La stratégie d’accès au coffre de clés ne prend pas en charge les autorisations de niveau objet précises, comme sur une clé/un secret/un certificat spécifique. Vous pouvez utiliser le [portail Azure](https://portal.azure.com/), les [outils de la CLI Azure](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) ou les [API REST de gestion de coffre de clés](https://msdn.microsoft.com/library/azure/mt620024.aspx) pour définir des stratégies d’accès pour un coffre de clés.

> [!IMPORTANT]
> Notez que les stratégies d’accès à un coffre de clés s’appliquent au niveau du coffre. Par exemple, lorsqu’un utilisateur est autorisé à créer et à supprimer des clés, il peut effectuer ces opérations sur toutes les clés de ce coffre de clés.

En plus des stratégies d’accès, l’accès au plan de données peut également être limité à l’aide des [points de terminaison de service du réseau virtuel pour Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) en configurant [des pare-feux et des règles de réseau virtuel](key-vault-network-security.md) pour une couche de sécurité supplémentaire.

## <a name="example"></a>Exemples
Supposons que vous développez une application qui utilise un certificat pour SSL, le stockage Azure pour stocker les données et une clé RSA 2 048 bits pour les opérations de signature. Supposons que cette application s’exécute sur une machine virtuelle Azure (ou un groupe de machines virtuelles identiques). Vous pouvez utiliser un coffre de clés pour stocker tous les secrets de l’application et stocker le certificat Bootstrap auquel l’application fait appel pour s’authentifier auprès d’Azure Active Directory.

Voici un résumé des types de clés et de secrets stockés dans un coffre de clés :

* **Certificat SSL** : utilisé pour le protocole SSL
* **Clé de stockage** : utilisée pour accéder au compte de stockage
* **Clé RSA 2 048 bits** : utilisée pour les opérations de signature
* **Certificat Bootstrap** : utilisé pour s’authentifier auprès d’Azure Active Directory. Une fois que l’accès est accordé, vous pouvez extraire la clé de stockage et utiliser la clé RSA pour la signature.

Découvrons à présent les personnes qui gèrent, déploient et auditent cette application. Nous utiliserons trois rôles dans cet exemple.

* **Équipe de sécurité** : il s’agit généralement du personnel informatique du bureau du chef de la sécurité ou équivalent. Cette équipe est responsable de garder les secrets en sécurité. Par exemple, les certificats SSL, les clés RSA utilisées pour la signature, les chaînes de connexion et les clés de compte de stockage.
* **Développeurs/opérateurs** : il s’agit des personnes qui développent l’application et la déploient dans Azure. En règle générale, ils ne font pas partie de l’équipe de sécurité et donc ne doivent pas avoir accès aux données sensibles, telles que les certificats SSL et les clés RSA. Seule l’application qu’ils déploient doit avoir accès à ces objets.
* **Auditeurs** : il s’agit le plus souvent d’un autre groupe de personnes qui ne font pas partie des développeurs et du personnel informatique général. Ils sont chargés de vérifier l’utilisation et la maintenance des certificats, des clés et des secrets pour assurer la conformité aux normes de sécurité. 

Il existe un autre rôle qui ne rentre pas dans le cadre de cette application, mais qui mérite quand même d’être mentionné. Il s’agit de l’administrateur d’abonnement (ou de groupe de ressources). L’administrateur d’abonnement définit les premières autorisations d’accès pour l’équipe de sécurité. L’administrateur d’abonnement accorde l’accès à l’équipe de sécurité à l’aide d’un groupe de ressources qui contient les ressources requises par cette application.

Voyons maintenant quelles actions sont effectuées par chaque rôle dans le contexte de cette application.

* **Équipe de sécurité**
  * Création de coffres de clés
  * Activation de la journalisation de coffre de clés
  * Ajout de clés/secrets
  * Création d’une sauvegarde des clés pour la récupération d’urgence
  * Définition d’une stratégie d’accès à un coffre de clés pour autoriser des utilisateurs et des applications à effectuer des opérations spécifiques
  * Déploiement périodique de clés/secrets
* **Développeurs/opérateurs**
  * Obtention des références aux certificats Bootstrap et SSL (empreintes), à la clé de stockage (URI de secret) et à la clé de signature (URI de clé) auprès de l’équipe de sécurité
  * Développement et déploiement d’une application qui accède aux clés et aux secrets par programmation
* **Auditeurs**
  * Examen des journaux d’utilisation pour confirmer l’utilisation correcte des clés/secrets et la conformité aux normes en matière de sécurité des données

Voyons maintenant quelles autorisations d’accès sont requises pour chaque rôle et l’application pour effectuer les tâches qui leur sont affectées. 

| Rôle d’utilisateur | Autorisations de plan de gestion | Autorisations de plan de données |
| --- | --- | --- |
| Équipe de sécurité |Contributeur Key Vault |Clés : sauvegarde, création, suppression, obtention, importation, énumération, restauration <br> Secrets : toutes |
| Développeurs/opérateurs |Autorisation de déploiement pour le coffre de clés de sorte que les machines virtuelles qu’ils déploient puissent récupérer les secrets dans le coffre de clés |Aucun |
| Auditeurs |Aucun |Clés : énumération<br>Secrets : énumération |
| Application |Aucun |Clés : énumération<br>Secrets : obtention |

> [!NOTE]
> Les auditeurs ont besoins d’une autorisation d’énumération pour les clés et les secrets afin de pouvoir inspecter les attributs de clés et de secrets qui ne sont pas inclus dans les journaux, tels que les balises et les dates d’activation et d’expiration.
> 
> 

En plus des autorisations de coffre de clés, les trois rôles ont aussi besoin d’un accès à d’autres ressources, par exemple pour pouvoir déployer des machines virtuelles (ou des applications web, etc.). Les développeurs/opérateurs ont également besoin d’un accès « Contributeur » à ces types de ressources. Les auditeurs ont besoin de l’accès en lecture au compte de stockage où sont stockés les journaux du coffre de clés.

Étant donné que le sujet de cet article est la sécurisation de l’accès à votre coffre de clés, nous illustrons uniquement les concepts qui s’y rapportent. Les détails concernant entre autres le déploiement des certificats ou l’accès aux clés et aux secrets par programmation sont abordés ailleurs. Exemple :

- Le déploiement des certificats stockés dans le coffre de clés sur des machines virtuelles est couvert dans un [billet de blog sur le déploiement des certificats sur des machines virtuelles à partir d’un coffre de clés géré par le client](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).
- Le [téléchargement d’exemples de client Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) montre comment utiliser un certificat bootstrap pour s’authentifier auprès d’Azure AD pour accéder à un coffre de clés.

Vous pouvez accorder la plupart des autorisations d’accès dans le portail Azure. Pour accorder des autorisations précises, vous devrez peut-être utiliser Azure PowerShell ou CLI pour obtenir le résultat souhaité. 

Les extraits de code PowerShell suivants supposent que :

* L’administrateur Active Directory Azure a créé des groupes de sécurité qui représentent les trois rôles (Contoso Security Team, Contoso App Devops et Contoso App Auditors). L’administrateur a également ajouté les utilisateurs aux groupes dont ils font partie.
* **ContosoAppRG** est le groupe de ressources où toutes les ressources se trouvent. **contosologstorage** est l’emplacement de stockage des journaux. 
* Le coffre de clés **ContosoKeyVault** et le compte de stockage utilisé pour les journaux de coffre de clés **contosologstorage** doivent se trouver dans le même emplacement Azure.

Tout d’abord, l’administrateur d’abonnement attribue les rôles « Contributeur Key Vault » et « Administrateur de l’accès utilisateur » à l’équipe de sécurité. Ces rôles permettent à l’équipe de sécurité de gérer l’accès aux autres ressources et de gérer les coffres de clés du groupe de ressources ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Le script suivant montre comment l’équipe de sécurité peut créer un coffre de clés et définir des autorisations d’accès et de journalisation. Pour des détails sur les autorisations d’une stratégie d’accès à un coffre de clés, consultez [Présentation des clés, des secrets et des certificats Azure Key Vault](about-keys-secrets-and-certificates.md).

```
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

Le rôle personnalisé défini peut uniquement être assigné à l’abonnement où le groupe de ressources ContosoAppRG a été créé. Si les mêmes rôles personnalisés sont utilisés pour d’autres projets dans d’autres abonnements, des abonnements supplémentaires peuvent être ajoutés.

L’attribution de rôles personnalisés aux développeurs/opérateurs pour l’autorisation « déploiement/action » se limite au groupe de ressources. Ainsi, seules les machines virtuelles créées dans le groupe de ressources « ContosoAppRG » peuvent avoir accès aux secrets (certificat SSL et certificat Bootstrap). Les machines virtuelles créées dans un autre groupe de ressources par un membre de l’équipe Dev/Ops n’ont pas accès à ces secrets, même si elles ont les URI des secrets.

Cet exemple illustre un scénario simple. Dans la pratique, vous pouvez rencontrer des scénarios plus complexes et vous devrez peut-être modifier les autorisations sur votre coffre de clés selon vos besoins. Dans notre exemple, nous partons du principe que l’équipe de sécurité fournit les références de clé et de secret (URI et empreintes) que les développeurs/opérateurs doivent référencer dans leurs applications. Les développeurs/opérateurs n’ont besoin d’aucun accès au plan de données. Cet exemple se concentre sur la sécurisation de votre coffre de clés. Une considération similaire doit être accordée pour sécuriser [vos machines virtuelles](https://azure.microsoft.com/services/virtual-machines/security/), vos [comptes de stockage](../storage/common/storage-security-guide.md) et autres ressources Azure.

> [!NOTE]
> Remarque : cet exemple montre comment l’accès au coffre de clés sera verrouillé en production. Les développeurs doivent posséder leur propre abonnement ou groupe de ressources avec des autorisations complètes pour gérer leurs coffres de clés, les machines virtuelles et le compte de stockage où ils développent l’application.

Il est fortement recommandé de sécuriser l’accès à votre coffre de clés en [configurant des pare-feux et des réseaux virtuels Key Vault](key-vault-network-security.md).

## <a name="resources"></a>Ressources
* [Contrôle d’accès en fonction du rôle Azure Active Directory](../role-based-access-control/role-assignments-portal.md)
  
  Cet article décrit le contrôle d’accès en fonction du rôle d’Active Directory Azure et en explique le fonctionnement.
* [RBAC : rôles intégrés](../role-based-access-control/built-in-roles.md)
  
  Cet article explique en détail tous les rôles intégrés disponibles dans RBAC.
* [Présentation du déploiement de Resource Manager et du déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md)
  
  Cet article décrit le modèle de déploiement de Resource Manager et le modèle de déploiement classique et explique les avantages liés à l’utilisation de Resource Manager et des groupes de ressources.
* [Gestion du contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
  
  Cet article explique comment gérer le contrôle d’accès en fonction du rôle avec Azure PowerShell
* [Gestion du contrôle d’accès basé sur les rôles à l’aide de l’API REST](../role-based-access-control/role-assignments-rest.md)
  
  Cet article montre comment utiliser l’API REST pour gérer RBAC.
* [Role-Based Access Control for Microsoft Azure from Ignite (Contrôle d’accès en fonction du rôle pour Microsoft Azure)](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Cette vidéo sur la conférence 2015 Microsoft Ignite parle des fonctionnalités de gestion des accès et de création de rapport dans Azure. Elle explore également les bonnes pratiques pour sécuriser l’accès aux abonnements Azure à l’aide d’Azure Active Directory.
* [Autoriser l’accès aux applications web à l’aide d’OAuth 2.0 et Azure Active Directory](../active-directory/develop/v1-protocols-oauth-code.md)
  
  Cet article décrit le flux OAuth 2.0 complet pour l’authentification auprès d’Azure Active Directory.
* [API REST de gestion de coffre de clés](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Ce document contient des informations de référence sur les API REST permettant de gérer votre coffre de clés par programmation, y compris la définition de la stratégie d’accès au coffre de clés.
* [API REST de coffre de clés](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
  Lien vers la documentation de référence des API REST de coffre de clés.
* [Contrôle d’accès aux clés](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
  Lien vers la documentation de référence sur le contrôle d’accès aux secrets.
* [Contrôle d’accès aux secrets](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
  Lien vers la documentation de référence sur le contrôle d’accès aux clés.
* [Définition](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) et [suppression](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy) de la stratégie d’accès au coffre de clés à l’aide de PowerShell
  
  Liens vers la documentation de référence des applets de commande PowerShell permettant de gérer la stratégie d’accès au coffre de clés.

## <a name="next-steps"></a>Étapes suivantes
[Configurer des pare-feu et des réseaux virtuels Key Vault](key-vault-network-security.md)

Pour un didacticiel de prise en main destiné aux administrateurs, consultez la page [Prise en main d’Azure Key Vault](key-vault-get-started.md).

Pour plus d’informations sur l’utilisation de la journalisation du coffre de clés, consultez [Journalisation d’Azure Key Vault](key-vault-logging.md).

Pour plus d’informations sur l’utilisation des clés et des secrets avec un coffre de clés Azure, consultez la page [À propos des clés et des clés secrètes](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Pour toute question concernant les coffres de clés, rendez-vous sur les [forums Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

