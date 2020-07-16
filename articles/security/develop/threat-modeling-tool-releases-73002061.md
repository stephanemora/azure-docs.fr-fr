---
title: Disponibilité générale de Microsoft Threat Modeling Tool version du 02/11/2020 – Azure
description: Compilation des notes de publication pour l’outil de modélisation des menaces
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: da4e61d6c89e62c3598570b30ce749390915ca1b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259336"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Version mise à jour de Threat Modeling Tool 7.3.00206.1 – 02/11/2020

La version 7.3.00206.1 de Microsoft Threat Modeling Tool (TMT) a été publiée le 11 février 2020 et contient les modifications suivantes :

- Résolution des bogues

## <a name="notable-bug-fixes"></a>Correctifs de bogues notables

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Erreurs liées aux valeurs de priorité en dehors des plages attendues

Certains clients ont signalé la réception du message d’erreur suivant lors de l’ouverture des fichiers créés dans « Threat Modeling Tool 2016 » ou des modèles personnalisés :

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Ce problème a été résolu dans cette version.

## <a name="system-requirements"></a>Configuration système requise

- Systèmes d’exploitation pris en charge
  - [Mise à jour anniversaire Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou version ultérieure
- Version .NET obligatoire
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou version ultérieure
- Autres conditions requises
  - Une connexion Internet est nécessaire pour recevoir les mises à jour de l’outil ainsi que les modèles.

## <a name="documentation-and-feedback"></a>Documentation et commentaires

- La documentation de Threat Modeling Tool se trouve sur [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) et contient des informations [sur l’utilisation de l’outil](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Étapes suivantes

Télécharger la version la plus récente de [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
