---
title: Suppression du fournisseur de ressources SQL sur Azure Stack | Microsoft Docs
description: Découvrez comment supprimer le fournisseur de ressources SQL de votre déploiement Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 150d1c40463aa04527bdd6e356a4c24ef68b02ef
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301896"
---
# <a name="remove-the-sql-resource-provider"></a>Supprimer le fournisseur de ressources SQL

Avant de supprimer le fournisseur de ressources SQL, vous devez supprimer toutes les dépendances de fournisseurs. Vous aurez également besoin d’une copie du package de déploiement qui a été utilisé pour installer le fournisseur de ressources.

## <a name="to-remove-the-sql-resource-provider"></a>Pour supprimer le fournisseur de ressources SQL

1. Vérifiez que vous avez supprimé toutes les dépendances existantes du fournisseur de ressources SQL.

   > [!NOTE]
   > La désinstallation du fournisseur de ressources SQL est effectuée même si des ressources dépendantes utilisent le fournisseur de ressources.
  
2. Obtenez une copie du binaire du fournisseur de ressources SQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire.

3. Ouvrez une nouvelle fenêtre de console PowerShell avec élévation de privilèges et basculez vers le répertoire où vous avez extrait les fichiers binaires du fournisseur de ressources SQL.

4. Exécutez le script DeploySqlProvider.ps1 à l’aide des paramètres suivants :

    - **Désinstaller**. Supprime le fournisseur de ressources et toutes les ressources associées.
    - **PrivilegedEndpoint**. Adresse IP ou nom DNS du point de terminaison privilégié.
    - **CloudAdminCredential**. Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié.
    - **AzCredential**. Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack.

## <a name="next-steps"></a>Étapes suivantes

[Offrir App Services en tant que PaaS](azure-stack-app-service-overview.md)
