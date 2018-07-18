---
title: Fichier Include
description: Fichier Include
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/09/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: 524bc1d3a19ad8ecfc8d0d210e735d6a9ae0058b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34678210"
---
### <a name="set-up-pagination"></a>Configurer la pagination

Pour certains connecteurs et leurs actions qui extraient des éléments multiples, vos résultats peuvent dépasser la taille de page par défaut du connecteur. Dans ce cas, l’action retourne uniquement la première page de résultats. Par exemple, la taille de page par défaut pour l’action **SQL Server - Obtenir les lignes** est 2048, mais peut différer en fonction d’autres paramètres. Pour être sûr d’obtenir tous les enregistrements, activez le paramètre **Pagination** pour cette action. Avec ce paramètre, votre application logique demande au connecteur les enregistrements restants, mais retourne tous les résultats dans un message unique à l’issue de l’action. 

Voici quelques connecteurs pour lesquels vous pouvez activer la pagination pour des actions spécifiques : 

* <a href="https://docs.microsoft.com/connectors/db2/" target="_blank">DB2</a>
* <a href="https://docs.microsoft.com/connectors/dynamicscrmonline/" target="_blank">Dynamics 365 CRM Online</a>
* <a href="https://docs.microsoft.com/connectors/excel/" target="_blank">Excel</a>
* <a href="https://docs.microsoft.com/connectors/oracle/" target="_blank">Oracle Database</a>
* <a href="https://docs.microsoft.com/connectors/sharepointonline/" target="_blank">SharePoint</a>
* <a href="https://docs.microsoft.com/connectors/sql/" target="_blank">SQL Server</a> 

Voici un exemple pour l’action **Obtenir les lignes** :

1. Pour savoir si l’action prend en charge la pagination, ouvrez les **Paramètres** de l’action. 

   ![Dans l’action, ouvrez Paramètres.](./media/connectors-pagination-bulk-data-transfer/sql-action-settings.png)

2. Si l’action prend en charge la pagination, modifiez le paramètre **Pagination** en le passant de **Désactivé** à **Activé**. Pour faire en sorte que l’action retourne un jeu de résultats minimum, indiquez une valeur **Limite**.

   ![Indiquer que l’action doit retourner un nombre minimal de résultats](./media/connectors-pagination-bulk-data-transfer/sql-action-settings-pagination.png)

3. Une fois ces opérations effectuées, sélectionnez **Terminé**.