---
title: Vérifier la sécurité des ports dans le pare-feu intégré
description: Apprenez à vérifier la protection par pare-feu intégré dans Azure SQL Managed Instance.
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
ms.openlocfilehash: 04a630e51a68fab8f6c60262fbd14fad2e4aef1c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84031150"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Vérifier le pare-feu intégré à Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Les [règles de sécurité entrante obligatoires](connectivity-architecture-overview.md#mandatory-inbound-security-rules) d'Azure SQL Managed Instance nécessitent l'ouverture des ports de gestion 9000, 9003, 1438, 1440 et 1452 à partir de **n'importe quelle source** sur le groupe de sécurité réseau (NSG) qui protège SQL Managed Instance. Bien que ces ports soient ouverts au niveau du groupe de sécurité réseau, ils sont protégés au niveau du réseau par le pare-feu intégré.

## <a name="verify-firewall"></a>Vérification du pare-feu

Utilisez n’importe quel outil scanneur de sécurité pour tester ces ports. La capture d’écran suivante montre comment utiliser l’un de ces outils.

![Vérification du pare-feu intégré](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur les instances SQL Managed Instance et la connectivité, consultez [Architecture de la connectivité Azure SQL Managed Instance](connectivity-architecture-overview.md).
