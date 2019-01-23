---
title: Gérer l’accès à la gestion Azure avec l’accès conditionnel dans Azure Active Directory
description: Découvrez l’utilisation de l’accès conditionnel dans Azure AD pour gérer l’accès à la gestion Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: b824d122a5d26c17c41a0e2ea1c595c9e2dd7206
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354346"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Gérer l’accès à la gestion Azure avec l’accès conditionnel

L’accès conditionnel dans Azure Active Directory (Azure AD) contrôle l’accès aux applications cloud en fonction de conditions spécifiées par vos soins. Pour autoriser l’accès, vous créez des stratégies d’accès conditionnel qui autorisent ou interdisent l’accès selon que les exigences définies dans la stratégie sont respectées ou non. 

En règle générale, vous utilisez l’accès conditionnel pour contrôler l’accès à vos applications cloud. Vous pouvez également définir des stratégies pour contrôler l’accès à la gestion Azure en fonction de certaines conditions (par exemple, le risque de connexion, l’emplacement ou l’appareil) et pour appliquer des exigences, telles que l’authentification multifacteur.

Pour créer une stratégie pour la gestion Azure, sélectionnez **Gestion Microsoft Azure** sous **Applications cloud** quand vous choisissez l’application à laquelle appliquer la stratégie.

![Accès conditionnel pour la gestion Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

La stratégie que vous créez s’applique à tous les points de terminaison de gestion Azure, y compris le portail Azure, le fournisseur Azure Resource Manager, les API de gestion de service classiques, Azure PowerShell et le portail administrateur des abonnements Visual Studio. Notez que la stratégie s’applique à Azure PowerShell, qui appelle l’API Azure Resource Manager. Elle ne s’applique pas à [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), qui appelle Microsoft Graph.

> [!CAUTION]
> Vous devez comprendre le fonctionnement de l’accès conditionnel avant de configurer une stratégie pour gérer l’accès à la gestion Azure. Veillez à ne pas créer de conditions susceptibles de bloquer votre propre accès au portail.

Pour plus d’informations sur la configuration et l’utilisation de l’accès conditionnel, consultez [Accès conditionnel dans Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).