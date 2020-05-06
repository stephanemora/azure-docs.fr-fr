---
title: Tutoriel - Mise à jour de la fréquence de rotation automatique d’un certificat dans Key Vault | Microsoft Docs
description: Tutoriel montrant comment mettre à jour la fréquence de rotation automatique d’un certificat dans Azure Key Vault à l’aide du portail Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 2e6c250a0bcb9d73e7c572dfe8138c31269993e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82107556"
---
# <a name="tutorial-configuring-certificates-auto-rotation-in-key-vault"></a>Tutoriel : Configuration de la rotation automatique d’un certificat dans Key Vault

Azure Key Vault vous permet de provisionner, gérer et déployer facilement des certificats numériques. Il peut s’agir de certificats SSL/TLS publics et privés, signés par l’autorité de certification, ou d’un certificat auto-signé. Key Vault peut également demander et renouveler des certificats via des partenariats avec des autorités de certification, en fournissant une solution robuste pour la gestion du cycle de vie des certificats. Dans ce tutoriel, vous mettez à jour les attributs d’un certificat : la période de validité, la fréquence de rotation automatique, l’autorité de certification. Pour plus d’informations sur Key Vault, consultez la [présentation](../general/overview.md).

Ce tutoriel explique les procédures suivantes :

> [!div class="checklist"]
> * Gérer un certificat avec le portail Azure
> * Ajouter un compte de fournisseur d’autorité de certification
> * Mettre à jour la période de validité d’un certificat
> * Mettre à jour la fréquence de rotation automatique d’un certificat
> * Mettre à jour les attributs d’un certificat avec Azure PowerShell


Avant de continuer, lisez les [concepts de base de Key Vault](../general/basic-concepts.md). 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-vault"></a>Création d'un coffre

Créez votre coffre de clés ou sélectionnez un coffre existant pour effectuer des opérations. [(Étapes de création d’un coffre de clés).](../quick-create-portal.md) Dans l’exemple, le nom du coffre est **Example-Vault**. 

![Sortie après la création du coffre de clés](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Créer un certificat dans Key Vault

Créez ou importez un certificat dans le coffre. [(Étapes de création d’un certificat dans Key Vault).](../quick-create-portal.md) Dans le cas présent, nous travaillons sur le certificat nommé **ExampleCertificate**.

> [!NOTE]
> Dans Azure Key Vault, les attributs de cycle de vie d’un certificat peuvent être mis à jour à la création du certificat, comme après sa création. 
## <a name="updating-certificates-life-cycle-attributes"></a>Mise à jour des attributs de cycle de vie d’un certificat

Un certificat créé dans le coffre de clés peut être 
- un certificat auto-signé ;
- un certificat créé avec une autorité de certification (AC) associée à Key Vault ;
- un certificat créé avec une autorité de certification non associée à Key Vault.

Les autorités de certification suivantes sont actuellement les fournisseurs associés à Key Vault :
- DigiCert : Key Vault propose des certificats TSL/SSL OV avec DigiCert.
- GlobalSign : Key Vault propose des certificats TSL/SSL OV avec GlobalSign.

Azure Key Vault assure la rotation automatique des certificats via des partenariats avec les autorités de certification. Par le biais du partenariat établi, Key Vault demande et renouvelle automatiquement les certificats. La **capacité de rotation automatique n’est pas donc applicable pour les certificats créés avec des autorités de certification qui ne sont pas associées à Key Vault.** 

> [!NOTE]
> Un administrateur de compte d’un fournisseur d’autorité de certification crée des informations d’identification utilisables par Key Vault pour créer, renouveler et utiliser des certificats TSL/SSL via Key Vault.
![Autorité de certification](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
> 


### <a name="updating-certificates-life-cycle-attributes-at-the-time-of-certificate-creation"></a>Mise à jour des attributs de cycle de vie d’un certificat au moment de la création du certificat

1. Dans les pages des propriétés Key Vault, sélectionnez **Certificats**.
2. Cliquez sur **Generate/Import (Générer/Importer)** .
3. Dans l’écran **Créer un certificat**, mettez à jour les valeurs suivantes :
    

    - **Période de validité** : entrez la valeur (en mois). La création de certificats à courte durée de vie est une pratique recommandée en matière de sécurité. Par défaut, la valeur de validité d’un certificat nouvellement créé est de 12 mois.
    - **Type d’action de la durée de vie** : sélectionnez l’action de renouvellement automatique ou d’alerte du certificat. Comme pour la sélection, mettez à jour le « Pourcentage de la durée de vie » ou le « Nombre de jours avant l’expiration ». Par défaut, le renouvellement automatique d’un certificat est défini à 80 % de sa durée de vie.<br> Dans le menu déroulant, sélectionnez l’option :

    |  Automatically renew at a given time (Renouveler automatiquement à un moment donné)| Envoyer un e-mail à tous les contacts à un moment donné (Email all contacts at a given time) |
    |-----------|------|
    |La sélection de cette option ACTIVE la rotation automatique | La sélection de cette option NE déclenche PAS la rotation automatique, elle alerte uniquement les contacts|
        


4. Cliquez sur **Créer**.

![Cycle de vie du certificat](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="updating-life-cycle-attributes-of-stored-certificate"></a>Mise à jour des attributs de cycle de vie d’un certificat stocké

1. Sélectionnez le coffre de clés.
2. Dans les pages des propriétés Key Vault, sélectionnez **Certificats**.
3. Sélectionnez le certificat que vous souhaitez mettre à jour. Dans le cas présent, nous allons utiliser un certificat nommé **ExampleCertificate**.
4. Dans la barre de menus supérieure, sélectionnez **Stratégie d’émission**.

![Propriétés du certificat](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)
5. Dans l’écran **Stratégie d’émission**, mettez à jour les valeurs suivantes :
- **Période de validité** : mettez à jour la valeur (en mois).
- **Type d’action de la durée de vie** : sélectionnez l’action de renouvellement automatique ou d’alerte du certificat. Comme pour la sélection, mettez à jour le « Pourcentage de la durée de vie » ou le « Nombre de jours avant l’expiration ». 

![Propriétés du certificat](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)
6. Cliquez sur **Save**(Enregistrer).

> [!IMPORTANT]
> La modification du type d’action de la durée de vie d’un certificat enregistre immédiatement les modifications pour les certificats existants.


### <a name="updating-certificates-attributes-using-powershell"></a>Mise à jour des attributs d’un certificat avec PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Pour modifier la stratégie de renouvellement d’une liste de certificats, entrez File.csv contenant VaultName,CertName <br/>
>  vault1,Cert1 <br/>
>  vault2,Cert2
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Découvrez-en plus sur les paramètres [ici](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes).

## <a name="clean-up-resources"></a>Nettoyer les ressources

D’autres démarrages rapides et didacticiels sur les coffres de clés reposent sur ce démarrage rapide. Si vous prévoyez d’utiliser d’autres démarrages rapides et didacticiels, il peut être utile de conserver ces ressources.
Si vous n’en avez plus besoin, supprimez le groupe de ressources. Ce faisant, vous supprimez le coffre de clés et les ressources associées. Pour supprimer le groupe de ressources à l’aide du portail :

1. Entrez le nom de votre groupe de ressources dans la zone Recherche en haut du portail. Lorsque vous voyez le groupe de ressources utilisé dans ce démarrage rapide dans les résultats de recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Dans le champ **TYPE THE RESOURCE GROUP NAME: (TAPER LE NOM DU GROUPE DE RESSOURCES :)** , tapez le nom du groupe de ressources et sélectionnez **Supprimer**.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez mis à jour le cycle de vie d’un certificat. Pour approfondir votre connaissance de Key Vault et la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

En savoir plus sur la [Gestion de la création de certificats dans Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)
- Consulter la [Vue d’ensemble de Key Vault](../general/overview.md)