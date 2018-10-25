---
title: Mise à niveau du code vers la dernière plateforme | Microsoft Docs
description: Cette rubrique explique comment mettre à niveau votre version de la plateforme Microsoft Dynamics 365 pour les opérations vers la dernière version
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 787d9ef509bcafeb6a21d4b0ec952ea1eb4fd52b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806399"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Mise à niveau du code vers la dernière plateforme

Cet article explique comment mettre à niveau votre version de la plateforme Microsoft Dynamics 365 pour les opérations vers la dernière version.

## <a name="overview"></a>Vue d’ensemble

La plateforme Microsoft Dynamics 365 pour les opérations est constituée des composants suivants :

Les binaires de la plateforme Dynamics 365 pour les opérations, comme le serveur d’objets d’application (AOS), l’infrastructure de gestion de données, l’infrastructure décisionnelle (BI) et de compte-rendu, les outils de développement et les services d’analyse. Les packages suivants de l’arborescence d’objets d’application (AOA) :

1. Application Platform
2. Application Foundation
3. Test Essentials

**Important**: pour déplacer vers la dernière plateforme Dynamics 365 pour les opérations, votre implémentation de Dynamics 365 pour les opérations ne peut pas avoir toutes les personnalisations (overlayering) d’un des packages AOT appartenant à la plateforme. Cette restriction a été introduite dans la mise à jour de plateforme n°3, afin que des mises à jour continues transparentes puissent être effectuées sur la plateforme. Si la version de votre plateforme est antérieure à la mise à jour n°3, consultez la section de mise à niveau vers la mise à jour de plateforme n°3 à partir d’une version antérieure, à la fin de cet article.

Pour plus d’informations sur la mise à niveau du code, consultez [cet article](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).