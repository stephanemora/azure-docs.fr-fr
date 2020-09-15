---
title: Inscrire le fournisseur de ressources Azure VMware Solution
description: Procédure d’inscription du fournisseur de ressources Azure VMware Solution.
ms.topic: include
ms.date: 09/04/2020
ms.openlocfilehash: 08741181b778b44289868a9f3fb1c2368955248c
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512360"
---
Pour utiliser Azure VMware Solution, vous devez d’abord inscrire le fournisseur de ressources pour votre abonnement.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Pour d’autres façons d’inscrire un fournisseur de ressources, consultez [Fournisseurs et types de ressources Azure](../../azure-resource-manager/management/resource-providers-and-types.md).