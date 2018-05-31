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
ms.openlocfilehash: b1cc1fad6b0831bcf0bab5ba4f37b753c3cf33ca
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206164"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Utiliser des bases de données SQL sur Microsoft Azure Stack
Utiliser l’adaptateur de fournisseur de ressources SQL Server pour exposer des bases de données SQL en tant que service d’[Azure Stack](azure-stack-poc.md). Une fois le fournisseur de ressources installé et connecté à une ou plusieurs instances de SQL Server, vous et vos utilisateurs pouvez créer :
- des bases de données pour les applications cloud natives ;
- des sites web basés sur SQL ;
- des charges de travail basées sur SQL.
Vous n’êtes pas obligé d’approvisionner à chaque fois une machine virtuelle qui héberge SQL Server.

Le fournisseur de ressources ne prend pas en charge toutes les fonctionnalités de gestion de base de données d’[Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Par exemple, les pools de bases de données élastiques et la possibilité de ralentir ou d’augmenter automatiquement les performances de la base de données ne sont pas disponibles. Toutefois, le fournisseur de ressources prend en charge des opérations CRUD (créer, lire, mettre à jour et supprimer) similaires. L’API n’est pas compatible avec SQL Database.

## <a name="sql-resource-provider-adapter-architecture"></a>Architecture de l’adaptateur de fournisseur de ressources SQL
Le fournisseur de ressources est constitué de trois composants :

- **La machine virtuelle de l’adaptateur de fournisseur de ressources SQL**, qui est une machine virtuelle Windows exécutant les services de fournisseur.
- **Le fournisseur de ressources proprement dit**, qui traite les demandes d’approvisionnement et expose les ressources de base de données.
- **Les serveurs qui hébergent SQL Server**, qui offrent de la capacité pour les bases de données, et qui sont appelés serveurs d’hébergement.

Vous devez créer une (ou plusieurs) instances de SQL Server et/ou fournir un accès aux instances SQL Server externes.

> [!NOTE]
> Les serveurs d’hébergement installés sur des systèmes intégrés Azure Stack doivent être créés à partir d’un abonnement de locataire. Ils ne peuvent pas être créés à partir de l’abonnement du fournisseur par défaut. Ils doivent être créés à partir du portail du locataire ou à partir d’une session PowerShell avec un nom de connexion approprié. Tous les serveurs d’hébergement sont des machines virtuelles facturables et doivent disposer des licences appropriées. L’administrateur de service peut être le propriétaire de l’abonnement locataire.


## <a name="next-steps"></a>Étapes suivantes

[Déployer le fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md)
