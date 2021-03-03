---
title: Configurer le transfert DNS pour Azure Red Hat OpenShift 4
description: Configurer le transfert DNS pour Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 68cc5dbee5b96472d11bdfb3a8bf35d118638dcd
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633867"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Configurer le transfert DNS sur un cluster Azure Red Hat OpenShift 4

Pour configurer le transfert DNS sur un cluster Azure Red Hat OpenShift, vous devez modifier l’opérateur DNS. Cette modification permet à vos pods d’application de s’exécuter au sein du cluster pour résoudre les noms hébergés sur un serveur DNS privé en dehors du cluster. Cette procédure est documentée [ici](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html) pour OpenShift 4.6.

Par exemple, si vous souhaitez transférer toutes les demandes DNS pour que *.example.com soit résolu par un serveur DNS 192.168.100.10, vous pouvez modifier la configuration de l’opérateur en exécutant la commande suivante :
 
```bash
oc edit dns.operator/default
```
 
Dans l’éditeur qui s’ouvre, vous pouvez remplacer `spec: {}` par :
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Enregistrez le fichier, puis quittez l’éditeur.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le transfert DNS pour OpenShift 4.6, cliquez [ici](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html).