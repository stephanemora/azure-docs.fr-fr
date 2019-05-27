---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 87fd6b626efb60c7fc7ec8896f2c3758ae5cc33c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132864"
---
### <a name="app-service-plan"></a>plan App Service
Crée le plan de service pour l'hébergement de l'application web. Vous fournissez le nom du plan à l'aide du paramètre **hostingPlanName** . L’emplacement du plan est identique à celui utilisé pour le groupe de ressources. Le niveau tarifaire et la taille de worker sont spécifiés dans les paramètres **sku** et **workerSize**.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

