---
title: Qu’est-ce qu’Azure Stack ? | Microsoft Docs
description: Azure Stack vous permet de tester les services Azure dans votre centre de données.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 041d679cfc428278737a5668887e73f13ac694b1
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50137051"
---
# <a name="what-is-azure-stack"></a>Qu’est-ce qu’Azure Stack ?

Microsoft Azure Stack est une plateforme cloud hybride qui vous permet de fournir des services Azure à partir de votre centre de données. Cette plateforme est conçue pour prendre en charge vos exigences en évolution. Azure Stack peut autoriser de nouveaux scénarios pour vos applications modernes, comme les environnements de périmètre et déconnectés ou la satisfaction d’exigences de sécurité et de conformité spécifiques.

Vous disposez de deux options pour déployer Azure Stack.

## <a name="azure-stack-integrated-systems"></a>Systèmes intégrés Azure Stack
Les systèmes intégrés Azure Stack sont disponibles par le biais d’un partenariat entre Microsoft et des [fournisseurs de matériel partenaires](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), qui aboutit à la création d’une solution combinant innovation cloud et simplicité de gestion. Comme Azure Stack est offert comme matériel et logiciel système intégré, vous avez la flexibilité et le contrôle dont vous avez besoin, ainsi que la capacité à innover depuis le cloud. Les systèmes intégrés Azure Stack présentent une taille comprise entre 4 et 16 nœuds et sont pris en charge conjointement par le fournisseur de matériel partenaire et Microsoft.  Utilisez des systèmes intégrés Azure Stack pour créer des scénarios et déployer de nouvelles solutions pour vos charges de travail de production.

## <a name="azure-stack-development-kit"></a>Kit de développement Azure Stack

Le [Kit de développement Azure Stack (ASDK)](.\asdk\asdk-what-is.md) Microsoft est un déploiement à un seul nœud d’Azure Stack qui vous permet de découvrir et d’évaluer Azure Stack.  Vous pouvez également l’utiliser comme environnement de développement pour concevoir des applications à l’aide d’API et d’outils cohérents avec Azure.

>[!Note]
>Le kit de développement Azure Stack n’est pas conçu pour être utilisé en tant qu’environnement de production.

L’ASDK présente les limites suivantes :

* L’ASDK est associé à un seul fournisseur d’identité Azure Active Directory (Azure AD) ou de services de fédération Active Directory (AD FS). Vous pouvez créer plusieurs utilisateurs dans ce répertoire et attribuer des abonnements à chaque utilisateur.
* Étant donné que tous les composants Azure Stack sont déployés sur un seul ordinateur hôte, les ressources physiques disponibles sont limitées pour les abonnés. Cette configuration n’est pas destinée à l’évaluation des performances ou de la mise à l’échelle.
* Les scénarios de mise en réseau sont limités à cause des exigences de déploiement de la carte réseau et de l’hôte unique.

## <a name="next-steps"></a>Étapes suivantes

[Principaux concepts et fonctionnalités](azure-stack-key-features.md)

[Azure Stack : une extension d’Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
