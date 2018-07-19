---
title: Mettre à niveau l’agent Sauvegarde Azure
description: Explique pourquoi vous devez mettre à niveau l’agent Sauvegarde Azure et où télécharger la mise à niveau.
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 3/16/2018
ms.author: markgal
ms.openlocfilehash: 4142f88c3ccb376acbdd2af07c5cfdf8fd275780
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38750328"
---
## <a name="upgrade-the-mars-agent"></a>Mettre à niveau l’agent MARS
Les versions de l’agent Microsoft Azure Recovery Service (MARS) antérieures à la version 2.0.9083.0 ont une dépendance au service Azure Access Control (ACS). En 2018, il est prévu qu’Azure [déprécie le service Azure Access Control (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). Depuis le 19 mars 2018, toutes les versions de l’agent MARS antérieures à la version 2.0.9083.0 rencontrent des échecs de sauvegarde. Pour éviter ou résoudre cess échecs, [mettez à niveau l’agent MARS avec la dernière version](https://go.microsoft.com/fwlink/?linkid=229525). Pour identifier les serveurs qui nécessitent une mise à niveau de l’agent MARS, suivez les étapes de ce [blog concernant la mise à niveau des agents Sauvegarde Azure](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). L’agent MARS est utilisé pour sauvegarder les types de données suivants dans Azure :
- Fichiers 
- Données sur l’état du système
- Sauvegarde de System Center DPM dans Azure
- Serveur Azure Backup (MABS)
