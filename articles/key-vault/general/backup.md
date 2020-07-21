---
title: Sauvegarde Azure Key Vault | Microsoft Docs
description: Utilisez ce document pour sauvegarder un secret, une clé ou un certificat stocké dans Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: 8a152e2771f0b207e81f42c6ecae2e4d14605051
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156303"
---
# <a name="azure-key-vault-backup"></a>Sauvegarde Azure Key Vault

Ce document vous montre comment effectuer une sauvegarde de secrets, de clés et de certificats individuels stockés dans votre coffre de clés. Cette sauvegarde est destinée à vous fournir une copie hors connexion de tous vos secrets dans le cas improbable où vous perdriez l’accès à votre coffre de clés.

## <a name="overview"></a>Vue d’ensemble

Key Vault fournit automatiquement plusieurs fonctionnalités permettant de garantir la disponibilité et empêcher toute perte de données. Cette sauvegarde ne doit être tentée que s’il existe une justification métier stratégique de tenir à jour une sauvegarde de vos secrets. La sauvegarde de secrets dans votre coffre de clés peut introduire des défis opérationnels supplémentaires, comme la gestion de plusieurs ensembles de journaux, d’autorisations et de sauvegardes quand des secrets expirent ou permutent.

Key Vault assure la disponibilité dans les scénarios de sinistre et bascule automatiquement les demandes vers une région associée sans qu’aucune intervention ne soit nécessaire de la part de l’utilisateur. Pour plus d’informations, consultez le lien suivant. [Reprise d’activité après sinistre Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)

Key Vault protège contre la suppression accidentelle et malveillante de vos secrets par le biais de la suppression réversible et de la protection contre le vidage. Si vous voulez une protection contre la suppression accidentelle ou malveillante de vos secrets, configurez les fonctionnalités de suppression réversible et de protection contre le vidage sur votre coffre de clés. Pour plus d’informations, consultez le document suivant. [Récupération d’un coffre de clés Azure](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)

## <a name="limitations"></a>Limites

Actuellement, Azure Key Vault ne prend en charge aucun moyen de sauvegarder l’ensemble d’un coffre de clés en une seule opération. Toute tentative d’utiliser les commandes listées dans ce document pour effectuer une sauvegarde automatisée d’un coffre de clés n’est pas prise en charge par Microsoft ou l’équipe Azure Key Vault.

Toute tentative d’utiliser les commandes présentées dans le document ci-dessous pour créer une automatisation personnalisée peut entraîner des erreurs.

* La sauvegarde de secrets avec plusieurs versions peut causer des erreurs de délai d’expiration.
* La sauvegarde crée une capture instantanée à un point dans le temps. Les secrets peuvent être renouvelés pendant une sauvegarde, provoquant une incohérence entre les clés de chiffrement.
* Le dépassement des limites du service de coffre de clés pour les demandes par seconde entraîne la limitation de votre coffre de clés et l’échec de la sauvegarde.

## <a name="design-considerations"></a>Remarques relatives à la conception

Quand vous sauvegardez un objet stocké dans un coffre de clés (secret, clé ou certificat), l’opération de sauvegarde télécharge l’objet sous la forme d’un objet blob chiffré. Cet objet blob ne peut pas être déchiffré en dehors d’Azure. Pour obtenir des données utilisables à partir de cet objet blob, vous devez restaurer l’objet blob dans un coffre de clés dans le même abonnement Azure et la même zone géographique Azure.
[Zones géographiques Azure](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>Prérequis

* Autorisations de niveau contributeur ou supérieur sur un abonnement Azure
* Un coffre de clés principal contenant les secrets que vous voulez sauvegarder
* Un coffre de clés secondaire où les secrets seront restaurés

## <a name="back-up-and-restore-with-azure-portal"></a>Sauvegarder et restaurer avec le portail Azure

### <a name="back-up"></a>Sauvegarder

1. Accédez au portail Azure.
2. Sélectionnez votre coffre de clés.
3. Accédez à l’objet (secret, clé ou certificat) que vous voulez sauvegarder.

    ![Image](../media/backup-1.png)

4. Sélectionnez l’objet.
5. Sélectionner « Télécharger la sauvegarde »

    ![Image](../media/backup-2.png)
    
6. Cliquez sur le bouton « Télécharger ».

    ![Image](../media/backup-3.png)
    
7. Stockez l’objet blob chiffré dans un emplacement sécurisé.

### <a name="restore"></a>Restaurer

1. Accédez au portail Azure.
2. Sélectionnez votre coffre de clés.
3. Accédez au type d’objet (secret, clé ou certificat) que vous voulez restaurer.
4. Sélectionner « Restaurer la sauvegarde »

    ![Image](../media/backup-4.png)
    
5. Accédez à l’emplacement où vous avez stocké l’objet blob chiffré.
6. Sélectionnez « OK ».

## <a name="back-up-and-restore-with-the-azure-cli"></a>Sauvegarder et restaurer avec l’interface Azure CLI

```azurecli
## Login To Azure
az login

## Set your Subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a Provider
az provider register -n Microsoft.KeyVault

## Backup a Certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>Étapes suivantes

Activez la journalisation et la supervision de votre coffre de clés Azure. [Journalisation d’Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/logging)
