---
title: Récupérer les métadonnées de l’équilibreur de charge à l’aide d’Azure Instance Metadata Service (IMDS)
titleSuffix: Azure Load Balancer
description: Commencez à apprendre comment récupérer les métadonnées de l’équilibreur de charge à l’aide d’Azure Instance Metadata Service.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 9ec217cefb05929ed6f5c7395df5e68891e823ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739778"
---
# <a name="retrieve-load-balancer-metadata-using-the-azure-instance-metadata-service-imds"></a>Récupérer les métadonnées de l’équilibreur de charge à l’aide d’Azure Instance Metadata Service (IMDS)

## <a name="prerequisites"></a>Prérequis

* Utilisez la [version la plus récente de l’API](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#supported-api-versions) pour votre requête.

## <a name="sample-request-and-response"></a>Exemple de requête et de réponse
> [!IMPORTANT]
> Dans cet exemple, les proxys sont contournés. Vous **devez** contourner les proxys lorsque vous interrogez IMDS. Pour plus d’informations, consultez [Proxys](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#proxies).
### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01" | ConvertTo-Json
```
> [!NOTE]
> Ce paramètre -NoProxy a été introduit dans Windows PowerShell 6.0. Si vous utilisez une version antérieure de PowerShell, supprimez -NoProxy du corps de la demande et assurez-vous que vous n’utilisez pas de proxy lors de la récupération des informations de IMDS. En savoir plus [ici](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service?tabs=windows#proxies).
> 
### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H "Metadata:true" --noproxy "*" "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01"
```

---
### <a name="sample-response"></a>Exemple de réponse

```json
{
   "loadbalancer": {
    "publicIpAddresses":[
      {
         "frontendIpAddress":"51.0.0.1",
         "privateIpAddress":"10.1.0.4"
      }
   ],
   "inboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frontendIpAddress":"2603:10e1:100:2::1:1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
   ],
   "outboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frotendIpAddress":"2603:10e1:100:2::1:1",
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
    ]
   }
}

```

## <a name="next-steps"></a>Étapes suivantes
[Codes d’erreur courants et étapes de résolution des problèmes](troubleshoot-load-balancer-imds.md)

En savoir plus sur [Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md)

[Récupérer toutes les métadonnées d’une instance](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#access-azure-instance-metadata-service)

[Déployer un équilibreur de charge standard](quickstart-load-balancer-standard-public-portal.md)

