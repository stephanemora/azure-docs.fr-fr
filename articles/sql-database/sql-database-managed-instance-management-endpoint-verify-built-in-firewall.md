---
title: Découvrir le pare-feu intégré Azure SQL Database Managed Instance | Microsoft Docs
description: Découvrez comment vérifier la protection par pare-feu intégré dans Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: c98b0fd5669140559b4840e157394c2e8c6086ae
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567440"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Vérification du pare-feu intégré Managed Instance

Les [règles de sécurité entrante obligatoires](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) de Managed Instance nécessitent l’ouverture des ports de gestion 9000, 9003, 1438, 1440 et 1452 à partir de **n’importe quelle source** sur le groupe de sécurité réseau (NSG) qui protège l’instance gérée. Bien que ces ports soient ouverts au niveau du groupe de sécurité réseau, ils sont protégés au niveau du réseau par le pare-feu intégré.

## <a name="verify-firewall"></a>Vérification du pare-feu

Utilisez n’importe quel outil scanneur de sécurité pour tester ces ports. La capture d’écran suivante montre comment utiliser l’un de ces outils.

![Vérification du pare-feu intégré](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’information sur les instances managées et la connectivité, consultez [Architecture de la connectivité d’Azure SQL Database Managed Instance](sql-database-managed-instance-connectivity-architecture.md).