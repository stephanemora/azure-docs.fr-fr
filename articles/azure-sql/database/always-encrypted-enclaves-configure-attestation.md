---
title: Configurer Azure Attestation pour votre serveur logique Azure SQL
description: Configurez Azure Attestation pour Always Encrypted avec enclaves sécurisées dans Azure SQL Database.
keywords: chiffrer les données, chiffrement sql, chiffrement de base de données, données sensibles, Always Encrypted, enclaves sécurisées, SGX, attestation
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: fb42a0428f0439053375027481d38977b068e356
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122576"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>Configurer Azure Attestation pour votre serveur logique Azure SQL

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted avec enclaves sécurisées pour Azure SQL Database est actuellement en **préversion publique**.

[Microsoft Azure Attestation](../../attestation/overview.md) est une solution pour l’attestation des environnements d’exécution de confiance (TEE), notamment les enclaves Intel Software Guard Extensions (Intel SGX). 

Afin d’utiliser Azure Attestation pour l’attestation des enclaves Intel SGX utilisées pour [Always Encrypted avec enclaves sécurisées](/sql/relational-databases/security/encryption/always-encrypted-enclaves) dans Azure SQL Database, vous devez :

1. Créer un [fournisseur d’attestation](../../attestation/basic-concepts.md#attestation-provider) et le configurer selon la stratégie d’attestation recommandée.

2. Accorder à votre serveur logique Azure SQL l’accès à votre fournisseur d’attestation.

> [!NOTE]
> La configuration de l’attestation est la responsabilité de l’administrateur d’attestation. Consultez [Rôles et responsabilités lors de la configuration des enclaves SGX et de l’attestation](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

## <a name="requirements"></a>Conditions requises

Le serveur logique Azure SQL et le fournisseur d’attestation doivent appartenir au même locataire Azure Active Directory. Les interactions entre locataires ne sont pas prises en charge. 

Une identité Azure AD doit être assignée au serveur logique Azure SQL. En tant qu’administrateur d’attestation, vous devez obtenir l’identité Azure AD du serveur auprès de l’administrateur Azure SQL Database pour ce serveur. Vous utiliserez l’identité pour accorder l’accès au serveur au fournisseur d’attestation. 

Pour obtenir des instructions sur la création d’un serveur avec une identité ou l’attribution d’une identité à un serveur existant à l’aide de PowerShell et Azure CLI, consultez [Attribuer une identité Azure AD à votre serveur](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server).

## <a name="create-and-configure-an-attestation-provider"></a>Créer et configurer un fournisseur d’attestation

Un [fournisseur d’attestation](../../attestation/basic-concepts.md#attestation-provider) est une ressource dans Azure Attestation qui évalue les [demandes d’attestation](../../attestation/basic-concepts.md#attestation-request) par rapport aux [stratégies d’attestation](../../attestation/basic-concepts.md#attestation-request) et émet les [jetons d’attestation](../../attestation/basic-concepts.md#attestation-token). 

Les stratégies d’attestation sont spécifiées à l’aide de la [grammaire des règles de revendication](../../attestation/claim-rule-grammar.md).

Microsoft recommande la stratégie suivante pour l’attestation des enclaves Intel SGX utilisées pour Always Encrypted dans Azure SQL Database :

```output
version= 1.0;
authorizationrules 
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
};
```

La stratégie ci-dessus vérifie les éléments suivants :

- L’enclave dans Azure SQL Database ne prend pas en charge le débogage. 
  > Il est possible de charger des enclaves quand le débogage est désactivé ou activé. La prise en charge du débogage est conçue pour permettre aux développeurs de résoudre des problèmes liés au code s’exécutant dans une enclave. Dans un système de production, le débogage peut permettre à un administrateur d’examiner le contenu de l’enclave, ce qui réduirait le niveau de protection qu’offre l’enclave. La stratégie recommandée désactive le débogage pour s’assurer que, si un administrateur malveillant tente d’activer la prise en charge du débogage en prenant le contrôle de l’ordinateur enclave, l’attestation échoue. 
- L’ID produit de l’enclave correspond à celui attribué à la fonctionnalité Always Encrypted avec des enclaves sécurisées.
  > Chaque enclave a un ID produit unique qui différencie l’enclave des autres enclaves. L’ID produit attribué à l’enclave Always Encrypted est 4639. 
- Le numéro de version de sécurité de la bibliothèque est supérieur à 0.
  > Il permet à Microsoft de répondre à des bogues de sécurité potentiels identifiés dans le code de l’enclave. En cas de détection et de correction d’un problème de sécurité, Microsoft déploie une nouvelle version de l’enclave avec un nouveau numéro de version de sécurité (incrémenté). La stratégie recommandée ci-dessus sera mise à jour pour refléter le nouveau numéro de version de sécurité. En mettant à jour votre stratégie pour qu’elle corresponde à la stratégie recommandée, vous pouvez vous assurer que, si un administrateur malveillant tente de charger une enclave plus ancienne et non sécurisée, l’attestation échouera.
- La bibliothèque de l’enclave a été signée à l’aide de la clé de signature Microsoft (la valeur de la revendication x-ms-sgx-mrsigner est le code de hachage de la clé de signature).
  > L’un des principaux objectifs de l’attestation est de convaincre les clients que le fichier binaire qui s’exécute dans l’enclave est celui qui est supposé s’exécuter. Les stratégies d’attestation fournissent deux mécanismes à cet effet. L’un est la revendication **mrenclave** qui est le hachage du fichier binaire supposé s’exécuter dans une enclave. Le problème avec la revendication **mrenclave** est que le hachage du fichier binaire change même avec des modifications banales du code, ce qui rend difficile la révision du code s’exécutant dans l’enclave. Par conséquent, nous vous recommandons d’utiliser la revendication **mrsigner**, qui est un hachage d’une clé utilisée pour signer le fichier binaire de l’enclave. Quand Microsoft révise l’enclave, la revendication **mrsigner** reste la même tant que la clé de signature ne change pas. Ainsi, il devient possible de déployer des fichiers binaires mis à jour sans interrompre les applications des clients. 

> [!IMPORTANT]
> Un fournisseur d’attestation est créé avec la stratégie par défaut pour les enclaves Intel SGX ce qui ne valide pas le code qui s’exécute à l’intérieur de l’enclave. Microsoft vous conseille vivement de définir la stratégie recommandée ci-dessus et de ne pas utiliser la stratégie par défaut pour Always Encrypted avec enclaves sécurisées.

Pour obtenir des instructions sur la création d’un fournisseur d’attestation et la configuration avec une stratégie d’attestation, consultez :

- [Démarrage rapide : Configurer Azure Attestation avec le portail Azure](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > Quand vous configurez votre stratégie d’attestation avec le portail Azure, définissez Type d’attestation sur `SGX-IntelSDK`.
- [Démarrage rapide : Configurer Azure Attestation avec Azure PowerShell](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > Quand vous configurez votre stratégie d’attestation avec Azure PowerShell, définissez le paramètre `Tee` sur `SgxEnclave`.
- [Démarrage rapide : Configurer Azure Attestation avec Azure CLI](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > Quand vous configurez votre stratégie d’attestation avec Azure CLI, définissez le paramètre `attestation-type` sur `SGX-IntelSDK`.

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>Déterminer l’URL d’attestation pour votre stratégie d’attestation

Une fois que vous avez configuré une stratégie d’attestation, vous devez partager l’URL d’attestation, en référençant la stratégie, avec les administrateurs d’applications qui utilisent Always Encrypted avec enclaves sécurisées dans Azure SQL Database. Les administrateurs d’applications ou/et les utilisateurs d’applications devront configurer leurs applications avec l’URL d’attestation afin de pouvoir exécuter des instructions qui utilisent des enclaves sécurisées.

### <a name="use-powershell-to-determine-the-attestation-url"></a>Utiliser PowerShell pour déterminer l’URL d’attestation

Utilisez le script suivant pour déterminer votre URL d’attestation :

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>Utiliser le portail Azure pour déterminer l’URL d’attestation

1. Dans le volet de vue d’ensemble de votre fournisseur d’attestation, copiez la valeur de la propriété de l’URI d’attestation dans le presse-papiers. Un URI d’attestation doit ressembler à ceci : `https://MyAttestationProvider.us.attest.azure.net`.

2. Ajoutez le code suivant à l’URI d’attestation : `/attest/SgxEnclave`. 

L’URL d’attestation obtenue doit ressembler à ceci : `https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>Accorder à votre serveur logique Azure SQL l’accès à votre fournisseur d’attestation

Pendant le flux de travail d’attestation, le serveur logique Azure SQL contenant votre base de données appelle le fournisseur d’attestation pour soumettre une demande d’attestation. Pour que le serveur logique Azure SQL puisse envoyer des demandes d’attestation, le serveur doit disposer d’une autorisation pour l’action `Microsoft.Attestation/attestationProviders/attestation/read` sur le fournisseur d’attestation. La méthode recommandée pour accorder l’autorisation consiste à ce que l’administrateur du fournisseur d’attestation attribue l’identité Azure AD du serveur au rôle Lecteur d’attestation pour le fournisseur d’attestation ou le groupe de ressources qui le contient.

### <a name="use-azure-portal-to-assign-permission"></a>Utiliser le portail Azure pour attribuer l’autorisation

Pour attribuer l’identité d’un serveur Azure SQL au rôle Lecteur d’attestation pour un fournisseur d’attestation, suivez les instructions générales dans [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md). Quand vous êtes dans le volet **Ajouter une attribution de rôle** :

1. Dans la liste déroulante **Rôle**, sélectionnez le rôle **Lecteur d’attestation**.
1. Dans le champ **Sélectionner**, entrez le nom de votre serveur Azure SQL pour le rechercher.

Voir l’exemple dans la capture d’écran ci-dessous.

![attribution de rôle de lecteur d’attestation](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> Pour qu’un serveur s’affiche dans le volet **Ajouter une attribution de rôle**, le serveur doit avoir une identité Azure AD attribuée, consultez [Spécifications](#requirements).

### <a name="use-powershell-to-assign-permission"></a>Utiliser PowerShell pour attribuer une autorisation

1. Recherchez votre serveur logique Azure SQL.

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName
```
 
2. Attribuez au serveur le rôle Lecteur d’attestation pour le groupe de ressources contenant votre fournisseur d’attestation.

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

Pour plus d’informations, consultez [Attribuer des rôles Azure en utilisant Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md#assign-role-examples).

## <a name="next-steps"></a>Étapes suivantes

- [Gérer des clés pour Always Encrypted avec enclaves sécurisées](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>Voir aussi

- [Tutoriel : Bien démarrer avec Always Encrypted avec enclaves sécurisées dans Azure SQL Database](always-encrypted-enclaves-getting-started.md)
