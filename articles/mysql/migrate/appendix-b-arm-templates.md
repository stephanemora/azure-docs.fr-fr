---
title: 'Guide de migration de MySQL local vers Azure Database pour MySQL - Annexe B : Modèles ARM'
description: Ce modèle déploie toutes les ressources avec des points de terminaison privés.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: 07e689b8458650ca39a0aa949504dd762621bac1
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110656712"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-appendix-b-arm-templates"></a>Guide de migration de MySQL local vers Azure Database pour MySQL - Annexe B : Modèles ARM

### <a name="secure"></a>Sécuriser

Ce modèle déploie toutes les ressources avec des points de terminaison privés. Cela supprime de fait tout accès aux services PaaS à partir d’Internet.

[Modèle ARM ](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigration)

### <a name="non-secure"></a>Non sécurisé

Ce modèle déploie des ressources à l’aide d’un déploiement standard, où toutes les ressources sont disponibles sur Internet.

[Modèle ARM ](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigrationSecure)