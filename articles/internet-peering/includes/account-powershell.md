---
title: Fichier Include
titleSuffix: Azure
description: Fichier Include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773640"
---
Avant de commencer la configuration, installez et importez les modules requis. Vous aurez besoin de privilèges d’administrateur pour installer les modules dans PowerShell.

1. Installer et importer un module Az
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Installer et importer un module Az.Peering
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Vérifiez que les modules sont importés avec succès à l’aide de la commande ci-dessous.
    ```powershell
    Get-Module
    ```
1. Connectez-vous à votre compte Azure à l’aide de la commande suivante.
    ```powershell
    Connect-AzAccount
    ```
1. Vérifiez les abonnements pour le compte et sélectionnez l’abonnement dans lequel vous souhaitez créer un peering.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Si vous n’avez pas déjà un groupe de ressources, vous devez en créer un avant de créer un peering. Pour cela, vous pouvez exécuter la commande suivante :

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Si vous n’avez pas encore associé votre ASN et votre abonnement, suivez les étapes de la procédure [Associer le numéro ASN de l’homologue](../howto-subscription-association-powershell.md). Cela est requis pour demander un peering.

> [!NOTE]
> L’emplacement du groupe de ressources est indépendant de l’emplacement où vous choisissez de configurer un peering.
&nbsp;
