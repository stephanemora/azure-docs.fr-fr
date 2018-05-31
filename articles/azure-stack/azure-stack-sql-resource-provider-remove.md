---
title: Utilisation de bases de données SQL sur Azure Stack | Microsoft Docs
description: Découvrez comment déployer des bases de données SQL en tant que service sur Azure Stack et les étapes rapides à suivre pour déployer l’adaptateur de fournisseur de ressources SQL Server.
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: c2686a2d5241af46e70263d1827028aa7e9b2138
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206161"
---
# <a name="remove-the-sql-resource-provider"></a>Supprimer le fournisseur de ressources SQL

Pour supprimer le fournisseur de ressources SQL, il est essentiel de supprimer d’abord toutes les dépendances :

1. Vérifiez que vous disposez du package de déploiement d’origine que vous avez téléchargé pour cette version de l’adaptateur du fournisseur de ressources SQL.

2. Toutes les bases de données utilisateur doivent être supprimées du fournisseur de ressources. (La suppression des bases de données utilisateur ne supprime pas les données.) Cette tâche doit être effectuée par les utilisateurs eux-mêmes.

3. L’administrateur doit supprimer les serveurs d’hébergement de l’adaptateur du fournisseur de ressources SQL.

4. L’administrateur doit supprimer tous les plans qui référencent l’adaptateur du fournisseur de ressources SQL.

5. L’administrateur doit supprimer toutes les références SKU et tous les quotas associés à l’adaptateur du fournisseur de ressources SQL.

6. Réexécutez le script de déploiement avec les éléments suivants :
    - Le paramètre -Désinstaller
    - Les points de terminaison Azure Resource Manager
    - Le DirectoryTenantID
    - Les informations d’identification du compte d’administrateur local du service

