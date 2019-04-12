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
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 502c1e8a422eb9e74586e5a6820d5b12ec4ae2a4
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501902"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Mise à jour d’outil de modélisation de contre les menaces 7.1.60408.1 - 4/9/2019

Version 7.1.60408.1 de Microsoft Threat Modeling outil (TMT) a été publiée sur 2019 9 avril et contient les modifications suivantes :

- Nouveau gabarits pour Azure Key Vault et Azure Traffic Manager
- Numéro de version TMT est à présent affiché sur l’écran d’accueil
- Liens de support ont été mis à jour.
- Résolution des bogues

## <a name="feature-changes"></a>Évolution des fonctionnalités

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nouveau gabarits pour Azure Key Vault et Azure Traffic Manager

![Stencil de coffre de clés Azure](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Nouveau gabarits et les menaces pour Azure Key Vault et Azure Traffic Manager ont été ajoutés à l’ensemble de gabarit Azure. Lors de l’ouverture des modèles basés sur le jeu de gabarit Azure, les utilisateurs seront invités à mettre à jour le modèle associé au modèle. La mise à jour un modèle basé sur l’ensemble de gabarit Azure peut également être lancé manuellement à l’aide de la commande « Appliquer le modèle » dans le menu « Fichier » et réappliquer le dernier fichier Services.tb7 de Cloud Azure.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Numéro de version TMT est à présent affiché sur l’écran d’accueil

La version du client de l’outil de modélisation des menaces est à présent affichée sur l’écran d’accueil de l’application pour les options d’ergonomie.

![Stencil de coffre de clés Azure](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Liens de support ont été mis à jour.

Tous les liens de support dans l’outil ont été mis à jour pour diriger les utilisateurs à [ tmtextsupport@microsoft.com ](mailto:tmtextsupport@microsoft.com) au lieu d’un forum MSDN.

## <a name="system-requirements"></a>Conditions requises pour le système

- Systèmes d'exploitation pris en charge
  - [Mise à jour anniversaire Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou version ultérieure
- Version .NET obligatoire
  - [.Net 4.7.1](http://go.microsoft.com/fwlink/?LinkId=863262) ou version ultérieure
- Conditions supplémentaires
  - Une connexion Internet est nécessaire pour recevoir les mises à jour de l’outil ainsi que les modèles.

## <a name="documentation-and-feedback"></a>Documentation et commentaires

- La documentation de Threat Modeling Tool se trouve sur [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) et contient des informations [sur l’utilisation de l’outil](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Étapes suivantes

Télécharger la version la plus récente de [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).