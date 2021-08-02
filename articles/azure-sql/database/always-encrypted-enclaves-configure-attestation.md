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
ms.date: 05/01/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0e2e6bc57a830b5257d246a4229e174cf8612d3c
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110662519"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>Configurer Azure Attestation pour votre serveur logique Azure SQL

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted avec enclaves sécurisées pour Azure SQL Database est actuellement en **préversion publique**.

[Microsoft Azure Attestation](../../attestation/overview.md) est une solution pour l’attestation des environnements d’exécution de confiance (TEE), notamment les enclaves Intel Software Guard Extensions (Intel SGX). 

Afin d’utiliser Azure Attestation pour l’attestation des enclaves Intel SGX utilisées pour [Always Encrypted avec enclaves sécurisées](/sql/relational-databases/security/encryption/always-encrypted-enclaves) dans Azure SQL Database, vous devez :

1. Créer un [fournisseur d’attestation](../../attestation/basic-concepts.md#attestation-provider) et le configurer selon la stratégie d’attestation recommandée.

2. Déterminez l’URL d’attestation et partagez-la avec les administrateurs d’applications.

> [!NOTE]
> La configuration de l’attestation est la responsabilité de l’administrateur d’attestation. Consultez [Rôles et responsabilités lors de la configuration des enclaves SGX et de l’attestation](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

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

Une fois que vous avez configuré une stratégie d’attestation, vous devez partager l’URL d’attestation avec les administrateurs d’applications qui utilisent Always Encrypted avec enclaves sécurisées dans Azure SQL Database. L’URL d’attestation est le `Attest URI` du fournisseur d’attestation contenant la stratégie d’attestation, qui ressemble à ceci : `https://MyAttestationProvider.wus.attest.azure.net`.

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>Utiliser le portail Azure pour déterminer l’URL d’attestation

Dans le volet de vue d’ensemble de votre fournisseur d’attestation, copiez la valeur de la propriété `Attest URI` dans le presse-papiers. 

### <a name="use-powershell-to-determine-the-attestation-url"></a>Utiliser PowerShell pour déterminer l’URL d’attestation

Utilisez la cmdlet `Get-AzAttestation` pour récupérer les propriétés du fournisseur d’attestation, notamment AttestURI.

```powershell
Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName
```

Pour plus d’informations, consultez [Créer et gérer un fournisseur d’attestation](../../attestation/quickstart-powershell.md#create-and-manage-an-attestation-provider).

## <a name="next-steps"></a>Étapes suivantes

- [Gérer des clés pour Always Encrypted avec enclaves sécurisées](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>Voir aussi

- [Tutoriel : Bien démarrer avec Always Encrypted avec enclaves sécurisées dans Azure SQL Database](always-encrypted-enclaves-getting-started.md)
