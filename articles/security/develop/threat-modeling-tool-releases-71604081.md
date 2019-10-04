---
title: Versions de Threat Modeling Tool - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Compilation des notes de publication pour l’outil de modélisation des menaces
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 6992ac58ebf8976714e5a478dd7b886efab2a4b7
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934800"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Mise à jour de Threat Modeling Tool version 7.1.60408.1 - 09/04/2019

La version 7.1.60408.1 de Microsoft Threat Modeling Tool (TMT) a été publiée le 9 avril 2019 et contient les modifications suivantes :

- Nouveaux gabarits pour Azure Key Vault et Azure Traffic Manager
- Le numéro de version TMT est maintenant affiché sur l’écran d’accueil
- Les liens de support ont été mis à jour
- Résolution des bogues

## <a name="feature-changes"></a>Évolution des fonctionnalités

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nouveaux gabarits pour Azure Key Vault et Azure Traffic Manager

![Gabarit Azure Key Vault](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

De nouveaux gabarits et de nouvelles menaces pour Azure Key Vault et Azure Traffic Manager ont été ajoutés à l’ensemble de gabarits Azure. Lors de l’ouverture de modèles basés sur l’ensemble de gabarits Azure, les utilisateurs seront invités à mettre à jour le gabarit associé au modèle. La mise à jour d’un modèle basé sur l’ensemble de gabarits Azure peut également être initiée manuellement à l’aide de la commande « Appliquer le modèle » dans le menu « Fichier » et en appliquant à nouveau le dernier fichier Services.tb7 d’Azure Cloud.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Le numéro de version TMT est maintenant affiché sur l’écran d’accueil

La version du client de Threat Modeling Tool est maintenant affichée sur l’écran d’accueil de l’application pour faciliter l’accès.

![Gabarit Azure Key Vault](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Les liens de support ont été mis à jour

Tous les liens de support dans l’outil ont été mis à jour pour diriger les utilisateurs vers [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) au lieu d’un forum MSDN.

## <a name="system-requirements"></a>Conditions requises pour le système

- Systèmes d'exploitation pris en charge
  - [Mise à jour anniversaire Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou version ultérieure
- Version .NET obligatoire
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou version ultérieure
- Conditions supplémentaires
  - Une connexion Internet est nécessaire pour recevoir les mises à jour de l’outil ainsi que les modèles.

## <a name="documentation-and-feedback"></a>Documentation et commentaires

- La documentation de Threat Modeling Tool se trouve sur [docs.microsoft.com](threat-modeling-tool.md) et contient des informations [sur l’utilisation de l’outil](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Étapes suivantes

Télécharger la version la plus récente de [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
