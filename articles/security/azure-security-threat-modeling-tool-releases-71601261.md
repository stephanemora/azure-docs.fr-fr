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
ms.date: 01/25/2019
ms.author: jegeib
ms.openlocfilehash: c96b924294286be57de90dae7e6534b5ed9306ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586224"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Mise à jour de Threat Modeling Tool version 7.1.60126.1 - 29/01/2019

La version 7.1.60126.1 de Microsoft Threat Modeling Tool a été publiée le 29 janvier 2019 et contient les modifications suivantes :

- La version minimale requise de .NET a été augmentée à [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- La version minimale requise de Windows est maintenant la [Mise à jour anniversaire Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) en raison de la dépendance de .NET.
- Une fonctionnalité d’activation/de désactivation de validation de modèle a été ajoutée au menu Options de l’outil.
- Plusieurs liens des propriétés des menaces ont été mis à jour.
- Des modifications mineures de l’expérience utilisateur ont été apportées dans l’écran d’accueil de l’outil.
- Threat Modeling Tool hérite maintenant des paramètres TLS du système d’exploitation hôte et est pris en charge dans les environnements nécessitant TLS 1.2 ou version ultérieure.

## <a name="feature-changes"></a>Évolution des fonctionnalités

### <a name="model-validation-option"></a>Option de validation du modèle

Sur la base des commentaires des clients, une option a été ajoutée à l’outil afin d'activer ou de désactiver la validation du modèle. Auparavant, si votre modèle utilisait un seul flux de données unidirectionnel entre deux objets, vous pouviez recevoir un message d'erreur indiquant : ObjectsName requires at least one 'Any'. Une fois la validation du modèle désactivé, ces avertissements ne s'affichent plus.

L'option permettant d'activer ou de désactiver la validation du modèle se trouve dans le menu Fichier->Paramètres->Options. Par défaut, ce paramètre est désactivé.

![Option de validation du modèle](./media/azure-security-threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Conditions requises pour le système

- Systèmes d'exploitation pris en charge
  - [Mise à jour anniversaire Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou version ultérieure
- Version .NET obligatoire
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou version ultérieure
- Conditions supplémentaires
  - Une connexion Internet est nécessaire pour recevoir les mises à jour de l’outil ainsi que les modèles.

## <a name="known-issues"></a>Problèmes connus

### <a name="unsupported-systems"></a>Systèmes non pris en charge

#### <a name="issue"></a>Problème

Les utilisateurs de systèmes Windows 10 dans l'impossibilité d'installer .NET 4.7.1 ou version ultérieure, comme Windows 10 Entreprise LTSB (version 1507), ne pourront pas ouvrir l'outil après la mise à niveau. Ces versions plus anciennes de Windows ne sont plus prises en charge pour l'outil Threat Modeling Tool, et ne doivent pas installer la dernière mise à jour.

#### <a name="workaround"></a>Solution de contournement

Les utilisateurs de Windows 10 Entreprise LTSB (version 1507) qui ont installé la dernière mise à jour peuvent revenir à la version précédente de l'outil Threat Modeling Tool via la boîte de dialogue de désinstallation de la section Applications et fonctionnalités.

## <a name="documentation-and-feedback"></a>Documentation et commentaires

- La documentation de Threat Modeling Tool se trouve sur [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) et contient des informations [sur l’utilisation de l’outil](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Étapes suivantes

Télécharger la version la plus récente de [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
