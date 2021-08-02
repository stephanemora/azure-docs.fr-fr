---
title: Guide de migration de MySQL local vers Azure Database pour MySQL – Annexe
description: Téléchargez la documentation supplémentaire que nous avons créée pour ce guide de migration et apprenez à configurer.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/11/2021
ms.openlocfilehash: feb02ded8e597f38c941b96eb6027061180a0177
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112082808"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-appendix"></a>Guide de migration de MySQL local vers Azure Database pour MySQL – Annexe

## <a name="prerequisites"></a>Prérequis

[Résumé](14-summary.md)

## <a name="overview"></a>Vue d’ensemble

Cet article explique comment déployer un exemple d’application avec un guide de migration MySQL de bout en bout et comment vérifier les paramètres de serveur disponibles.

## <a name="environment-setup"></a>Configuration de l’environnement

[Téléchargez la documentation supplémentaire](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1%20Appendix%20A.pdf) que nous avons créée pour ce guide de migration et apprenez à configurer un environnement pour exécuter les étapes de migration du guide pour l’exemple d’[application de conférence de démonstration](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/sample-app).

## <a name="azure-resource-manager-arm-templates"></a>Modèles Azure Resource Manager (ARM)

### <a name="secure"></a>Sécuriser

Le modèle ARM déploie toutes les ressources avec des points de terminaison privés. Le modèle ARM supprime de fait tout accès aux services PaaS à partir d’Internet.

[Modèle ARM sécurisé](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigration)

### <a name="non-secure"></a>Non sécurisé

Le modèle ARM déploie des ressources à l’aide d’un déploiement standard, où toutes les ressources sont disponibles sur Internet.

[Modèle ARM non sécurisé](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigrationSecure)

## <a name="default-server-parameters-mysql-55-and-azure-database-for-mysql"></a>Paramètres de serveur par défaut MySQL 5.5 et Azure Database pour MySQL

Vous pouvez trouver la [liste complète des paramètres de serveur par défaut de MySQL 5.5 et de la base de données Azure pour MySQL](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1%20Appendix%20C.pdf) dans notre référentiel GitHub.