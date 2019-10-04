---
title: Migrer des ressources d’identité Azure d’Azure Germany vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources de gestion des identités Azure d’Azure Germany vers Azure global.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 8/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: 07ecaa564f2fda21967ab6f0c30c06fa876e4171
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033526"
---
# <a name="migrate-identity-resources-to-global-azure"></a>Migrer des ressources d’identité vers Azure global

Cet article contient des informations qui peuvent vous aider à migrer des ressources d’identité Azure d’Azure Germany vers Azure global.

Les conseils sur l’identité/les locataires sont destinées aux clients Azure uniquement. Si vous utilisez des locataires Azure Active Directory (Azure AD) courants pour Azure et Office 365 (ou d’autres produits Microsoft), il existe des complexités lors de la migration de l’identité et vous devez d’abord contacter votre responsable de compte avant d’utiliser ce guide de migration.

## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD dans Azure Germany est distinct d’Azure AD dans Azure global. Actuellement, vous ne pouvez pas déplacer des utilisateurs Azure AD d’Azure Germany vers Azure global.

Les noms de locataire par défaut dans Azure Germany et Azure global sont toujours différents, car Azure ajoute automatiquement un suffixe en fonction de l’environnement. Par exemple, supposons que le nom d’utilisateur pour un membre du locataire **contoso** Azure global est **user1\@contoso.microsoftazure.com**. Dans Azure Germany, ce serait **user1\@contoso.microsoftazure.de**.

Lorsque vous utilisez des noms de domaine personnalisés (comme **contoso.com**) dans Azure AD, vous devez inscrire le nom de domaine dans Azure. Vous pouvez définir des noms de domaine personnalisés dans *un seul* environnement cloud à la fois. La validation de domaine échoue lorsque le domaine est déjà inscrit dans *n’importe quelle* instance d’Azure Active Directory. Par exemple, l’utilisateur **user1\@contoso.com** qui existe dans Azure Germany ne peut pas également exister dans Azure global sous le même nom en même temps. L’inscription pour **contoso.com** échouerait.

Une migration « progressive », dans laquelle certains utilisateurs sont déjà dans le nouvel environnement et d’autres toujours dans l’ancien environnement requiert différents noms de connexion pour les différents environnements cloud.

Nous ne traitons pas chaque scénario de migration possible dans cet article. Une recommandation dépendrait, par exemple, de la façon dont vous configurez les utilisateurs, les options dont vous disposez pour l’utilisation de différents noms d’utilisateur ou UserPrincipalNames, et d’autres dépendances. Toutefois, nous avons rassemblé quelques conseils pour vous aider à répertorier les utilisateurs et groupes de votre environnement actuel.

Pour obtenir une liste de toutes les applets de commande associées à Azure AD, exécutez :

```powershell
Get-Help Get-AzureAD*
```

### <a name="inventory-users"></a>Utilisateurs de l’inventaire

Pour obtenir une vue d’ensemble de tous les utilisateurs et groupes qui existent dans votre instance Azure AD :

```powershell
Get-AzureADUser -All $true
```

Pour répertorier uniquement les comptes activés, ajoutez le filtre suivant :

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true}
```

Pour effectuer un vidage complet de tous les attributs, au cas où vous avez oublié quelque chose :

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | Format-List *
```

Pour sélectionner les attributs dont vous avez besoin pour recréer les utilisateurs :

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | select UserPrincipalName,DisplayName,GivenName,Surname
```

Pour exporter la liste vers Excel, utilisez l’applet de commande **Export-Csv** à la fin de cette liste. Une exportation complète peut se présenter comme dans cet exemple :

```powershell
Get-AzureADUser -All $true | Where-Object {$_.AccountEnabled -eq $true} | select UserPrincipalName,DisplayName,GivenName,Surname | Export-Csv -Path c:\temp\alluserUTF8.csv -Delimiter ";" -Encoding UTF8
```

> [!NOTE]
> Vous ne pouvez pas migrer les mots de passe. Au lieu de cela, vous devez attribuer de nouveaux mots de passe ou utiliser un mécanisme de libre-service, en fonction de votre scénario.
>
>En outre, selon votre environnement, vous devrez peut-être collecter d’autres informations, comme les valeurs pour **Extensions**, **DirectReport** ou **LicenseDetail**.

Formatez votre fichier CSV en fonction de vos besoins. Ensuite, suivez les étapes décrites dans [Importer des données à partir de CSV](/powershell/azure/active-directory/importing-data) pour recréer les utilisateurs dans votre nouvel environnement.

### <a name="inventory-groups"></a>Répertorier les groupes

Pour documenter les appartenances au groupe :

```powershell
Get-AzureADGroup
```

Pour obtenir la liste des membres de chaque groupe :

```powershell
Get-AzureADGroup | ForEach-Object {$_.DisplayName; Get-AzureADGroupMember -ObjectId $_.ObjectId}
```

### <a name="inventory-service-principals-and-applications"></a>Répertorier les applications et principaux de service

Bien que vous devez recréer l’ensemble des applications et principaux du service, une bonne pratique consiste à documenter l’état des principaux de service et des applications. Vous pouvez utiliser les applets de commande suivantes pour obtenir une liste complète des principaux de service :

```powershell
Get-AzureADServicePrincipal |Format-List *
```

```powershell
Get-AzureADApplication |Format-List *
```

Vous pouvez obtenir plus d’informations à l’aide des autres applets de commande qui commencent par `Get-AzureADServicePrincipal*` ou `Get-AzureADApplication*`. 

### <a name="inventory-directory-roles"></a>Répertorier les rôles d’annuaire

Pour documenter l’attribution actuelle des rôles :

```powershell
Get-AzureADDirectoryRole
```

Passez en revue chaque rôle pour rechercher les utilisateurs ou applications associés au rôle :

```powershell
Get-AzureADDirectoryRole | ForEach-Object {$_.DisplayName; Get-AzureADDirectoryRoleMember -ObjectId
$_.ObjectId | Format-Table}
```
Pour plus d'informations :

- En savoir plus sur les [Solutions d'identité hybride](../active-directory/choose-hybrid-identity-solution.md).
- Lisez le billet de blog [Utiliser Azure AD Connect avec plusieurs clouds](https://blogs.technet.microsoft.com/ralfwi/2017/01/24/using-adconnect-with-multiple-clouds/) pour en savoir plus sur les méthodes que vous utiliser pour synchroniser différents environnements cloud.
- En savoir plus sur [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).
- En savoir plus sur [noms de domaine personnalisés](../active-directory/fundamentals/add-custom-domain.md).
- Découvrez comment [importer des données CSV dans Azure AD](/powershell/azure/active-directory/importing-data).

## <a name="azure-ad-connect"></a>Azure AD Connect

Azure AD Connect est un outil qui synchronise vos données d’identité entre une instance Active Directory locale et Azure Active Directory (Azure AD). La version actuelle d’Azure AD Connect fonctionne à la fois pour Azure Germany et Azure global. Azure AD Connect ne peut synchroniser qu’une seule instance d’Azure AD à la fois. Si vous souhaitez vous synchroniser avec Azure Germany et Azure global en même temps, considérez ces options :

- Utilisez un serveur supplémentaire pour une deuxième instance Azure AD Connect. Vous ne pouvez pas avoir plusieurs instances Azure AD Connect sur le même serveur.
- Définissez un nouveau nom de connexion pour vos utilisateurs. La partie domaine (après **\@** ) du nom de connexion doit être différente dans chaque environnement.
- Définissez une « source de vérité » claire lorsque vous synchronisez aussi dans l’autre sens (d’Azure AD vers l’instance Active Directory locale).

Si vous utilisez déjà Azure AD Connect pour synchroniser de et vers Azure Germany, assurez-vous que vous migrez tous les utilisateurs créés manuellement. L’applet de commande PowerShell suivante répertorie tous les utilisateurs qui ne sont pas synchronisés avec Azure AD Connect :

```powershell
Get-AzureADUser -All $true |Where-Object {$_.DirSyncEnabled -ne "True"}
```

Pour plus d'informations :

- En savoir plus sur [Azure AD Connect](../active-directory/hybrid/reference-connect-dirsync-deprecated.md).

## <a name="multi-factor-authentication"></a>Azure Multi-Factor Authentication

Vous devez recréer les utilisateurs et redéfinir votre instance Azure Multi-Factor Authentication dans votre nouvel environnement. 

Pour obtenir une liste des comptes utilisateur pour lesquels l’authentification multifacteur est activée ou appliquée :

1. Connectez-vous au portail Azure.
1. Sélectionnez **Utilisateurs** > **Tous les utilisateurs** > **Authentication multifacteur**.
1. Lorsque vous êtes redirigé vers la page du service d’authentification multifacteur, définissez les filtres appropriés pour obtenir la liste des utilisateurs.

Pour plus d'informations :

- En savoir plus sur [Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les outils, techniques et recommandations pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Mise en réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [web](./germany-migration-web.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Sécurité](./germany-migration-security.md)
- [Outils de gestion](./germany-migration-management-tools.md)
- [Média](./germany-migration-media.md)
