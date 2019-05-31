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
ms.date: 05/23/2019
ms.author: rkarlin
ms.openlocfilehash: 3b21371d6321b208b19ca8b2524308736c3ceca9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244345"
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

1. Ouvrez la Console de gestion ICDX pour ajouter des redirecteurs Microsoft Azure Sentinel (Analytique de journal).
2. Dans la barre de navigation ICDx, cliquez sur **Configuration**. 
3. En haut de la **Configuration** , cliquez sur **redirecteurs**.
4. Sous **redirecteurs**, en regard de Microsoft Azure Sentinel (Analytique de journal), cliquez sur **ajouter**. 
4. Dans le **Microsoft Azure Sentinel (journal Analytique)** fenêtre, cliquez sur **afficher les options avancées**. 
5. En haut de l’étendue à fenêtre Microsoft Azure Sentinel (Analytique de journal), procédez comme suit :
    -   **Nom** : Tapez un nom pour le redirecteur comportant pas plus de 30 caractères. Choisissez un nom unique et significatif. Ce nom apparaît dans la liste des redirecteurs sur les **Configuration** écran et dans les tableaux de bord sur le **tableau de bord** écran. Exemple : Microsoft Azure Log Analytique est des États-Unis. Ce champ est obligatoire.
    -   **Description** : Tapez une description pour le redirecteur. Cette description apparaît également dans la liste des redirecteurs sur les **Configuration** écran. Inclure des détails tels que le type d’événement transmis et le groupe qui doit inspecter les données.
    -   **Type de démarrage**: Sélectionnez la méthode de démarrage pour la configuration du redirecteur. Vos options sont manuel et automatique.<br>La valeur par défaut est automatique. 
6. Sous **événements**, procédez comme suit : 
    - **Source** : Sélectionnez un ou plusieurs archives à partir duquel transférer des événements. Vous pouvez sélectionner des archives de collecteur actif (y compris l’Archive courantes), orphelins archives collecteur (autrement dit, les archives pour les collecteurs de que vous avez supprimées), archives de récepteur ICDx ou le système Archive. <br>La valeur par défaut est Archive courants.
      > [!NOTE]
      > Archives de récepteur ICDx sont répertoriés séparément, par nom. 
 
    - **Filtre** : Ajoutez un filtre qui spécifie le sous-ensemble d’événements à transférer. Effectuez l’une des actions suivantes :
        - Pour sélectionner une condition de filtre, cliquez sur un Type, un attribut, un opérateur et une valeur. 
        - Dans le champ filtre, passez en revue la condition de filtre. Vous pouvez modifier directement dans le champ ou la supprimer si nécessaire.
        - Cliquez sur et ou ou à ajouter à la condition de filtre.
        - Vous pouvez également cliquer sur les requêtes enregistrées pour appliquer une requête enregistrée.
    - **Inclure les attributs**: Tapez la liste délimitée par des virgules des attributs à inclure dans les données transférées. Les attributs inclus sont prioritaires sur les attributs exclus.
    - **Exclure les attributs**: Tapez la liste délimitée par des virgules des attributs pour exclure les données transférées.
    - **Taille de lot**: Sélectionnez le nombre d’événements à envoyer par lot. Vos options sont 10, 50, 100, 500 et 1000.<br>La valeur par défaut est 100. 
    - **Limite de taux**: Sélectionnez la vitesse à laquelle les événements sont transférés, exprimée sous la forme d’événements par seconde. Vos options sont illimité, 500 et 1000, 5000, 10000. <br> La valeur par défaut est 5000. 
7. Sous **Destination Azure**, procédez comme suit : 
    - **ID de l’espace de travail**: Collez l’ID de l’espace de travail ci-dessous. Ce champ est obligatoire.
    - **Clé primaire**: Collez la clé primaire ci-dessous. Ce champ est obligatoire.
    - **Nom de journal personnalisé**: Tapez le nom de journal personnalisé dans l’espace de journal Analytique portail Microsoft Azure auquel vous vous apprêtez à transférer des événements. La valeur par défaut est SymantecICDx. Ce champ est obligatoire.
8. Cliquez sur *enregistrer* pour terminer la configuration du redirecteur. 
9. Pour démarrer le redirecteur, sous **Options**, cliquez sur **plus** , puis **Démarrer**.
10. Pour utiliser le schéma pertinent dans Analytique de journal pour les événements de Symantec ICDX, recherchez **SymantecICDX_CL**.


## <a name="validate-connectivity"></a>Valider la connectivité

Il peut prendre plus de 20 minutes jusqu'à ce que vos journaux commencent à apparaître dans le journal Analytique. 



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Symantec ICDX à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [obtenez une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main [détecter des menaces avec Azure Sentinel](tutorial-detect-threats.md).

