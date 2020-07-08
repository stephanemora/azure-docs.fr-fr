---
title: Activer l’affinité basée sur les cookies avec Application Gateway
description: Cet article fournit des informations sur la façon d’activer l’affinité basée sur les cookies à l’aide d’une Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3af2705fedbb9c19d4f128e8e997d3fa73f8b5a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807966"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Activer l’affinité basée sur les cookies à l’aide d’une Application Gateway
Comme indiqué dans la [documentation d’Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings), Application Gateway prend en charge l’affinité basée sur les cookies, ce qui signifie que le service peut diriger le trafic qui en résulte à partir d’une session utilisateur vers le même serveur à des fins de traitement.

## <a name="example"></a>Exemple
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
