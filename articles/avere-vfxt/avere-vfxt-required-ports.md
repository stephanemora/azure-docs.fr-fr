---
title: Liste verte des ports pour Avere vFXT pour Azure
description: Liste des ports utilisés par Avere vFXT pour Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 394415ffc7593d058d8d0bf1c0040b88ec25073b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669926"
---
# <a name="required-ports"></a>Ports requis

Les ports listés dans cette section sont utilisés pour les communications vFXT entrantes et sortantes.

N’exposez jamais le cluster vFXT ou l’instance du contrôleur de cluster directement sur l’Internet public.

## <a name="api"></a>API

| Communications entrantes : | | |
| --- | ---- | --- |
| TCP | 22  | SSH  |
| TCP | 443 | HTTPS|



| Communications sortantes : |     |       |
|----------|-----|-------|
| TCP      | 443 | HTTPS |

 
## <a name="nfs"></a>NFS

| Communications entrantes et sortantes  | | |
| --- | --- | ---|
| TCP/UDP | 111  | RPCBIND  |
| TCP/UDP | 2049 | NFS      |
| TCP/UDP | 4045 | NLOCKMGR |
| TCP/UDP | 4046 | MOUNTD   |
| TCP/UDP | 4047 | STATUT   |

