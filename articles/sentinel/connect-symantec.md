---
title: Connectez les données de Symantec ICDX vers Azure Sentinel Preview | Microsoft Docs
description: Découvrez comment connecter les données de Symantec ICDX à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 0410b052f17a868aed70ce407b9c9fdefbe023df
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233642"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Connecter votre application Symantec ICDX 

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Connecteur de Symantec ICDX vous permet de connecter facilement tous vos journaux de solution de sécurité Symantec avec votre Azure Sentinel, pour afficher des tableaux de bord, créer des alertes personnalisées et améliore l’examen. Cela vous donne plus d’informations sur le réseau de votre organisation et améliore vos capacités d’opération de sécurité. Intégration entre Symantec ICDX et Sentinel Azure se sert de l’API REST.


> [!NOTE]
> Données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="configure-and-connect-symantec-icdx"></a>Configurer et connecter Symantec ICDX 

Symantec ICDX peut intégrer et exporter les journaux directement vers Azure Sentinel.

1. Ouvrez la Console de gestion ICDX.
2. Dans le menu de navigation de gauche, sélectionnez **Configuration** , puis le **redirecteurs** onglet.
3. Dans la ligne de Microsoft Azure Log Analytique, cliquez sur **plus**, suivie **modifier**. 
4. Dans le **redirecteur de Microsoft Azure Log Analytique** fenêtre, définissez les éléments suivants :
    - Laissez le nom de journal personnalisé en tant que la valeur par défaut, SymantecICDX.
    - Copiez l’ID de l’espace de travail et le coller dans le **identificateur du client** champ. Copie le **clé primaire** et collez-le dans le champ de clé partagée. Vous pouvez copier ces valeurs à partir du portail Azure Sentinel en sélectionnant **connecteurs de données** , puis **Symantec ICDX**.
6. Pour utiliser le schéma pertinent dans Analytique de journal pour les événements de Symantec ICDX, recherchez **SymantecICDX_CL**.


## <a name="validate-connectivity"></a>Valider la connectivité

Il peut prendre plus de 20 minutes jusqu'à ce que vos journaux commencent à apparaître dans le journal Analytique. 



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Symantec ICDX à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).

