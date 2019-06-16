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
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: 56310b7356dd9e263238234cf3e28bd498fa70fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66127796"
---
## <a name="upgrade-the-mars-agent"></a>Mettre à niveau l’agent MARS

Les versions de l’agent MARS (Microsoft Azure Recovery Service) antérieures à la version 2.0.9083.0 ont une dépendance au service ACS (Azure Access Control). L’agent MARS est également désigné sous le nom d’agent Sauvegarde Azure. En 2018, [le service ACS (Azure Access Control) a été déprécié](../articles/active-directory/develop/active-directory-acs-migration.md) dans Azure. Depuis le 19 mars 2018, toutes les versions de l’agent MARS antérieures à la version 2.0.9083.0 rencontrent des échecs de sauvegarde. Pour éviter ou résoudre ces échecs, [mettez à niveau l’agent MARS avec la dernière version](https://go.microsoft.com/fwlink/?linkid=229525). Pour identifier les serveurs qui nécessitent une mise à niveau de l’agent MARS, suivez les étapes décrites dans ce [blog concernant la mise à niveau de l’agent MARS](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). L’agent MARS est utilisé pour sauvegarder des fichiers, des dossiers et des données sur l’état du système dans Azure. System Center DPM et le serveur de sauvegarde Azure utilisent l’agent MARS pour sauvegarder des données dans Azure.
