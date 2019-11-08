---
title: Activer l’affinité basée sur les cookies avec Application Gateway
description: Cet article fournit des informations sur la façon d’activer l’affinité basée sur les cookies à l’aide d’une Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795990"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Activer l’affinité basée sur les cookies à l’aide d’une Application Gateway
Comme indiqué dans la [documentation d’Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings), Application Gateway prend en charge l’affinité basée sur les cookies, ce qui signifie que le service peut diriger le trafic qui en résulte à partir d’une session utilisateur vers le même serveur à des fins de traitement.

## <a name="example"></a>Exemples
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
