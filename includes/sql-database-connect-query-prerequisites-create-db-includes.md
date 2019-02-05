---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 01/28/2019
ms.author: genemi
ms.openlocfilehash: edeaa932abe4d1882f957d0ed26aaddd97dabe3f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198059"
---
<!-- sql-database-connect-query-prerequisites-create-db-includes.md -->

- Une base de données SQL Azure placée sur un [serveur logique](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) ou une [instance managée](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index). Vous pouvez utiliser une des techniques suivantes pour créer une base de données :

| Serveur logique | Instance gérée |
| --- | --- |
| [Portal](../articles/sql-database/sql-database-get-started-portal.md) | [Portal](../articles/sql-database/sql-database-managed-instance-get-started.md) |
| [INTERFACE DE LIGNE DE COMMANDE](../articles/sql-database/sql-database-get-started-cli.md) | |
| [PowerShell](../articles/sql-database/sql-database-get-started-powershell.md) | |

- **Serveur logique uniquement** : règle de pare-feu configurée au niveau du serveur permettant de vous connecter à votre serveur logique. Pour plus d’informations, consultez [Créer une règle de pare-feu au niveau du serveur](../articles/sql-database/sql-database-get-started-portal-firewall.md).
- **Instance managée uniquement** : connexion configurée à partir de l’ordinateur qui accède à l’instance managée. Vous pouvez utiliser les options suivantes :
  - [Machine virtuelle Azure](../articles/sql-database/sql-database-managed-instance-configure-vm.md) située dans le même réseau virtuel Azure que l’instance managée et ayant accès à l’instance.
  - [Connexion point à site](../articles/sql-database/sql-database-managed-instance-configure-p2s.md) sur votre ordinateur qui vous permet de joindre votre ordinateur au réseau virtuel où se trouve l’instance managée et de l’utiliser comme tout autre serveur SQL dans votre réseau.
