---
title: Mise à niveau du code vers la dernière plateforme | Place de marché Azure
description: Cette rubrique explique comment mettre à niveau votre version de la plateforme Microsoft Dynamics 365 pour les opérations vers la dernière version
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: aedc2c7474de0fe068a329eb2205e9bb08e62c3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935285"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Mise à niveau du code vers la dernière plateforme

Cet article explique comment mettre à niveau votre version de la plateforme Microsoft Dynamics 365 pour les opérations vers la dernière version.

## <a name="overview"></a>Vue d'ensemble

La plateforme Microsoft Dynamics 365 pour les opérations est constituée des composants suivants :

Les binaires de la plateforme Dynamics 365 pour les opérations, comme le serveur d’objets d’application (AOS), l’infrastructure de gestion de données, l’infrastructure décisionnelle (BI) et de compte-rendu, les outils de développement et les services d’analyse. Les packages suivants de l’arborescence d’objets d’application (AOA) :

1. Application Platform
2. Application Foundation
3. Test Essentials

**Important** : pour déplacer vers la dernière plateforme Dynamics 365 pour les opérations, votre implémentation de Dynamics 365 pour les opérations ne peut pas avoir toutes les personnalisations (overlayering) d’un des packages AOT appartenant à la plateforme. Cette restriction a été introduite dans la mise à jour de plateforme n°3, afin que des mises à jour continues transparentes puissent être effectuées sur la plateforme. Si la version de votre plateforme est antérieure à la mise à jour n°3, consultez la section de mise à niveau vers la mise à jour de plateforme n°3 à partir d’une version antérieure, à la fin de cet article.

Pour plus d’informations sur la mise à niveau du code, consultez [cet article](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).