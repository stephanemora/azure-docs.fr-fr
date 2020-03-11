---
title: Mettre à niveau l’agent Sauvegarde Azure
description: Explique pourquoi vous devez mettre à niveau l’agent Sauvegarde Azure et où télécharger la mise à niveau.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: 227ec87081817b273b66684e31a0ebd0e11134a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197116"
---
## <a name="upgrade-the-mars-agent"></a>Mettre à niveau l’agent MARS

Les versions de l’agent MARS (Microsoft Azure Recovery Services) antérieures à la version 2.0.9083.0 ont une dépendance au service Azure Access Control. L’agent MARS est également désigné sous le nom d’agent Sauvegarde Azure.

En 2018, Microsoft [a déprécié le service Azure Access Control](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Depuis le 19 mars 2018, toutes les versions de l’agent MARS antérieures à la version 2.0.9083.0 rencontrent des échecs de sauvegarde. Pour éviter ou résoudre cess échecs, [mettez à niveau l’agent MARS avec la dernière version](https://go.microsoft.com/fwlink/?linkid=229525). Pour identifier les serveurs qui nécessitent une mise à niveau de l’agent MARS, suivez les étapes décrites dans ce [blog concernant la mise à niveau de l’agent MARS](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/).

L’agent MARS est utilisé pour sauvegarder des fichiers, des dossiers et des données sur l’état du système dans Azure. System Center DPM et le serveur de sauvegarde Azure utilisent l’agent MARS pour sauvegarder des données dans Azure.
