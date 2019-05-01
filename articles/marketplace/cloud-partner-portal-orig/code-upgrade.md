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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776809"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Mise à niveau du code vers la dernière plateforme

Cet article explique comment mettre à niveau votre version de la plateforme Microsoft Dynamics 365 pour les opérations vers la dernière version.

## <a name="overview"></a>Présentation

La plateforme Microsoft Dynamics 365 pour les opérations est constituée des composants suivants :

Les binaires de la plateforme Dynamics 365 pour les opérations, comme le serveur d’objets d’application (AOS), l’infrastructure de gestion de données, l’infrastructure décisionnelle (BI) et de compte-rendu, les outils de développement et les services d’analyse. Les packages suivants de l’arborescence d’objets d’application (AOA) :

1. Application Platform
2. Application Foundation
3. Test Essentials

**Important !** Pour déplacer vers la dernière de Dynamics 365 pour la plateforme d’opérations, votre Dynamics 365 pour l’implémentation des opérations ne peut pas avoir toutes les personnalisations (couche) d’un des packages AOT qui appartiennent à la plateforme. Cette restriction a été introduite dans la mise à jour de plateforme n°3, afin que des mises à jour continues transparentes puissent être effectuées sur la plateforme. Si la version de votre plateforme est antérieure à la mise à jour n°3, consultez la section de mise à niveau vers la mise à jour de plateforme n°3 à partir d’une version antérieure, à la fin de cet article.

Pour plus d’informations sur la mise à niveau du code, consultez [cet article](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).