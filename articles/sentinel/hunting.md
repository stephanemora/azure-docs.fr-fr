---
title: Fonctionnalités de chasse Sentinel version préliminaire d’Azure | Microsoft Docs
description: Cet article décrit comment utiliser les fonctionnalités de recherche Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: adedc8bc1f574ae089f2a11033fab4f390c57a9a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714825"
---
# <a name="hunt-for-threats-with-in-azure-sentinel-preview"></a>Recherche les menaces avec Sentinel version préliminaire d’Azure

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si vous êtes un investigateur qui souhaite être proactive sur les menaces de sécurité, Azure Sentinel recherchez des outils de recherche et de requête puissant chasse à recherche les menaces de sécurité de vos sources de données de votre organisation. Générerez vos systèmes et les appliances de sécurité très volumineuse de données qui peuvent être difficiles à analyser et filtrer des événements significatifs. Pour aider à sécurité analystes recherchez proactive nouvelles anomalies qui n’ont pas été détectés par vos applications de sécurité Azure Sentinel « requêtes de recherche intégrée explique comment poser les bonnes questions pour rechercher les problèmes dans les données que vous avez déjà sur votre réseau. 

Par exemple, une seule requête intégrée fournit des données sur les processus plus rares qui s’exécutent sur votre infrastructure : vous ne voudriez une alerte concernant chaque fois qu’elles sont exécutées, elles peuvent être entièrement inoffensive, mais vous souhaiterez peut-être jeter un œil de la requête à l’occasion pour voir si th la règle attendue 's quelque chose d’inhabituel. 



Avec chasse Sentinel Azure, vous pouvez tirer parti des fonctionnalités suivantes :

- Requêtes intégrées : Pour vous aider à démarrer, une page de démarrage fournit des exemples de requête préchargées conçus pour vous aider à démarrent et vous familiariser avec les tables et le langage de requête. Ces requêtes de recherche intégrées sont développées par les chercheurs de sécurité Microsoft de façon continue, ajout de nouvelles requêtes, et de requêtes ajustement existantes pour vous fournir un point d’entrée pour rechercher les nouvelles détections et déterminer où commencer la recherche pour le débuts de nouvelles attaques. 

- Puissant langage de requête avec IntelliSense : Reposant sur un langage de requête qui vous donne la flexibilité que nécessaire pour prendre la chasse au niveau supérieur.

- Créez vos propres signets : Pendant le processus de recherche, vous êtes susceptible de rencontrer correspondances ou les conclusions, les tableaux de bord ou les activités inhabituelles ou suspectes de rechercher. Afin de marquer ces éléments, donc vous pouvez y revenir à l’avenir, utilisez la fonctionnalité de signet. Signets vous permettent d’enregistrer des éléments pour une utilisation ultérieure, pour être utilisé pour créer un cas de l’investigation. Pour plus d’informations sur les signets, consultez utiliser [signets chasse].

- Utiliser des blocs-notes pour automatiser un examen : Blocs-notes sont comme des playbooks pas à pas que vous pouvez générer pour les différentes étapes d’une enquête et de recherche.  Blocs-notes encapsulent toutes les étapes de chasse dans un manuel réutilisable qui peuvent être partagées avec d’autres personnes de votre organisation. 
- Interroger les données stockées : Les données sont accessibles dans les tables pour vous permettent d’interroger. Par exemple, vous pouvez interroger la création du processus, les événements DNS et les nombreux autres types d’événements.

- Liens vers la Communauté : Exploiter la puissance de la Communauté supérieure pour rechercher d’autres requêtes et sources de données.
 
## <a name="get-started-hunting"></a>Prise en main de chasse

1. Dans le portail Azure Sentinel, cliquez sur **chasse**.
  ![Sentinel Azure commence la chasse](media/tutorial-hunting/hunting-start.png)

2. Lorsque vous ouvrez le **chasse** page, toutes les requêtes de recherche sont affichés dans une seule table. Le tableau répertorie toutes les requêtes écrites par l’équipe Microsoft des analystes de sécurité, ainsi que n’importe quelle requête supplémentaire, vous avez créé ou modifié. Chaque requête fournit une description de ce que cela Mac pour, et quel type de données qu’elle s’exécute sur. Ces modèles sont regroupés par leurs tactiques différents : les icônes sur la droite classer le type de menace, telles que l’accès initial, la persistance et exfiltration. Vous pouvez filtrer ces modèles de requête de recherche à l’aide de l’un des champs. Vous pouvez enregistrer n’importe quelle requête à vos favoris. En enregistrant une requête à vos favoris, la requête est automatiquement exécutée chaque fois que le **chasse** page est accessible. Vous pouvez créer votre propre requête de recherche ou un clone et personnaliser un modèle de requête de recherche existant. 
 
2. Cliquez sur **exécuter la requête** dans la chasse la page de détails de la requête pour exécuter une requête sans quitter la page de chasse.  Le nombre de correspondances est affiché dans la table. Passez en revue la liste des requêtes de recherche et de leur correspondance. Découvrez quelle étape la correspondance est associée à la chaîne de destruction.

3. Procéder à une vérification rapide de la requête sous-jacente dans le volet de détails de requête ou cliquez sur **afficher le résultat de la requête** pour ouvrir la requête dans le journal Analytique. En bas, passez en revue les résultats de la requête.

4.  Cliquez sur la ligne, puis sélectionnez **ajouter signet** pour ajouter les lignes à examiner - vous pouvez le faire pour tout élément qui semble suspect. 

5. Ensuite, revenez à la main **chasse** page et cliquez sur le **signets** onglet pour afficher toutes les activités suspectes. 

6. Sélectionnez un signet, puis cliquez **examiner** pour ouvrir l’expérience d’investigation. Vous pouvez filtrer les signets. Par exemple, si vous recherchez des informations sur une campagne, vous pouvez créer une balise pour la campagne et puis filtrez tous les signets en fonction de la campagne.

1. Une fois que vous avez découvert la requête chasse fournit des informations de valeur élevée dans les attaques possibles, vous pouvez également créer une détection personnalisée règles en fonction de votre requête et communiquez ces connaissances sous forme d’alertes à votre répondeurs aux incidents de sécurité.

 

## <a name="query-language"></a>Langage de requête 

Chasse dans Azure Sentinel est basé sur le langage de requête Azure Log Analytique. Pour plus d’informations sur le langage de requête et les opérateurs pris en charge, consultez [référence de langage de requête](https://docs.loganalytics.io/docs/Language-Reference/).

## <a name="public-hunting-query-github-repository"></a>Référentiel GitHub de requête publique chasse

Découvrez le [référentiel de requêtes de chasse](https://github.com/Azure/Orion). Collaborer et utiliser les exemples de requêtes partagées par nos clients.

 

## <a name="sample-query"></a>Exemple de requête

Une requête classique commence par un nom de table suivi d’une série d’opérateurs séparés par \|.

Dans l’exemple ci-dessus, commencez par la table nom SecurityEvent et ajoutez les éléments redirigés en fonction des besoins.

1. Définir un filtre de temps pour consulter uniquement les enregistrements des sept jours précédents.

2. Ajouter un filtre dans la requête pour afficher uniquement les ID d’événement 4688.

3. Ajouter un filtre dans la requête sur la ligne de commande qui contiendra uniquement les instances de cscript.exe.

4. Projet uniquement les colonnes que vous souhaitez Explorer et limitez les résultats à 1000, puis cliquez sur **exécuter la requête**.
5. Cliquez sur le triangle vert et exécutez la requête. Vous pouvez tester la requête et exécutez-le pour rechercher un comportement anormal.

## <a name="useful-operators"></a>Opérateurs utiles

Le langage de requête est puissant et comporte de nombreux opérateurs disponibles, certains opérateurs utiles sont répertoriées ici :

**où** -une table pour le sous-ensemble de lignes qui satisfont un prédicat de filtre.

**résumer** -créer une table qui agrège le contenu de la table d’entrée.

**jointure** -fusionner les lignes des deux tables pour former une nouvelle table en mettant en correspondance les valeurs des colonnes de chaque table spécifiés.

**nombre** -retourner le nombre d’enregistrements dans le jeu d’enregistrements d’entrée.

**haut** -enregistrements de retourner le N premiers triés selon les colonnes spécifiées.

**limite** -retourner le nombre spécifié de lignes au maximum.

**projet** - sélectionner les colonnes à inclure, renommer ou supprimer et insérer de nouvelles colonnes calculées.

**étendre** - créer des colonnes calculées et les ajouter au jeu de résultats.

**makeset** -retourner un tableau (JSON) dynamique de l’ensemble de valeurs distinctes qui accepte Expr dans le groupe

**rechercher** -rechercher les lignes qui correspondent à un prédicat sur un ensemble de tables.

## <a name="save-a-query"></a>Enregistrement d’une requête

Vous pouvez créer ou modifier une requête et enregistrez-le en tant que votre propre requête ou le partager avec les utilisateurs figurant dans le même client.

   ![Enregistrer la requête](./media/tutorial-hunting/save-query.png)

Créer une nouvelle requête de recherche :

1. Cliquez sur **nouvelle requête** et sélectionnez **enregistrer**.
2. Renseignez tous les champs vides et sélectionnez **enregistrer**.

   ![Nouvelle requête](./media/tutorial-hunting/new-query.png)

Cloner et modifier une requête de recherche existante :

1. Sélectionnez la requête de recherche dans la table que vous souhaitez modifier.
2. Sélectionnez les points de suspension (...) dans la ligne de la requête que vous souhaitez modifier, puis sélectionnez **cloner la requête**.

   ![Cloner la requête](./media/tutorial-hunting/clone-query.png)
 

3. Modifier la requête et sélectionnez **créer**.

   ![requête personnalisée](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris comment exécuter une investigation de chasse avec Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :


- [Utiliser des blocs-notes pour lancer des campagnes de chasse automatisés](notebooks.md)
- [Utiliser des signets pour enregistrer des informations intéressantes lors de la recherche](bookmarks.md)