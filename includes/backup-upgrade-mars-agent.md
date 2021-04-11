---
title: Mettre à niveau l’agent Sauvegarde Azure
description: Explique pourquoi vous devez mettre à niveau l’agent Sauvegarde Azure et où télécharger la mise à niveau.
services: backup
cloud: ''
suite: ''
author: v-amallick
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: v-amallick
ms.openlocfilehash: bf77103db93652e1df837f6b1032b5e53bd41e1f
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294128"
---
## <a name="upgrade-the-mars-agent"></a>Mettre à niveau l’agent MARS

Les versions de l’agent MARS (Microsoft Azure Recovery Services) antérieures à la version 2.0.9083.0 ont une dépendance au service Azure Access Control. L’agent MARS est également désigné sous le nom d’agent Sauvegarde Azure.

En 2018, Microsoft [a déprécié le service Azure Access Control](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Depuis le 19 mars 2018, toutes les versions de l’agent MARS antérieures à la version 2.0.9083.0 rencontrent des échecs de sauvegarde. Pour éviter ou résoudre cess échecs, [mettez à niveau l’agent MARS avec la dernière version](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). Pour identifier les serveurs qui nécessitent une mise à niveau de l’agent MARS, procédez de la manière décrite dans [Mettre à niveau l’agent Microsoft Azure Recovery Services (MARS)](../articles/backup/upgrade-mars-agent.md).

L’agent MARS est utilisé pour sauvegarder des fichiers, des dossiers et des données sur l’état du système dans Azure. System Center DPM et le serveur de sauvegarde Azure utilisent l’agent MARS pour sauvegarder des données dans Azure.
