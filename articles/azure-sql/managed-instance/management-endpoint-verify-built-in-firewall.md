---
title: Vérifier la sécurité des ports dans le pare-feu intégré
description: Apprenez à vérifier la protection par pare-feu intégré dans Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 35d0053d8c45547eaa5e89e6da15fcaf0343d6c5
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617296"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Vérifier le pare-feu intégré à Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Les [règles de sécurité entrante obligatoires](connectivity-architecture-overview.md#mandatory-inbound-security-rules) d’Azure SQL Managed Instance nécessitent l’ouverture des ports de gestion 9000, 9003, 1438, 1440 et 1452 à partir de **n’importe quelle source** sur le groupe de sécurité réseau (NSG) qui protège SQL Managed Instance. Bien que ces ports soient ouverts au niveau du groupe de sécurité réseau, ils sont protégés au niveau du réseau par le pare-feu intégré.

## <a name="verify-firewall"></a>Vérification du pare-feu

Utilisez n’importe quel outil scanneur de sécurité pour tester ces ports. La capture d’écran suivante montre comment utiliser l’un de ces outils.

![Vérification du pare-feu intégré](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur SQL Managed Instance et la connectivité, consultez [Architecture de la connectivité Azure SQL Managed Instance](connectivity-architecture-overview.md).
