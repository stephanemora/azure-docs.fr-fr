---
title: Fonctionnalités de repérage dans Azure Sentinel | Microsoft Docs
description: Utilisez les requêtes de chasse intégrées à Azure Sentinel pour vous aider à poser les bonnes questions afin de détecter les problèmes dans vos données.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2021
ms.author: yelevin
ms.openlocfilehash: a8bf6ec24a67327d715b4a5d09b1d0d633b980c5
ms.sourcegitcommit: ce9178647b9668bd7e7a6b8d3aeffa827f854151
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109810371"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Repérer les menaces avec Azure Sentinel

> [!IMPORTANT]
>
> - Les mises à niveau vers le **tableau de bord de repérage** sont actuellement en **PRÉVERSION**. Les éléments ci-dessous associés à cette mise à niveau portent la mention « (préversion) ». Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

En tant qu’analystes et investigateurs de sécurité, vous souhaitez être proactif dans la recherche des menaces de sécurité, mais vos différents systèmes et appliances de sécurité génèrent de grandes quantités de données qui peuvent être difficiles à analyser et à filtrer pour obtenir des événements explicites. Azure Sentinel dispose d’outils de repérage puissants dédiés à la recherche et la création de requêtes pour repérer les menaces de sécurité dans les sources de données de votre organisation. Afin de faciliter la tâche des analystes de la sécurité dans la recherche proactive des nouvelles anomalies non détectées par vos applications de sécurité ou même par vos règles analytiques planifiées, les requêtes de repérage intégrées d’Azure Sentinel vous aident à poser les bonnes questions pour identifier les problèmes au niveau des données déjà présents dans le réseau. 

Par exemple, l’une des requêtes intégrées fournit des données sur les processus qui s’exécutent le moins souvent sur votre infrastructure. Vous ne souhaitez pas recevoir une alerte chaque fois que ces processus s’exécutent, car ils peuvent se révéler totalement inoffensifs, mais vous pouvez examiner cette requête de temps à autre pour vérifier qu’il ne se produit rien d’inhabituel. 

La fonctionnalité de repérage d’Azure Sentinel vous offre les avantages suivants :

- **Requêtes intégrées** : la page de repérage principale, accessible à partir du menu de navigation Azure Sentinel, fournit des exemples de requêtes prêts à l’emploi, conçus pour vous aider à démarrer et à vous familiariser avec les tables et le langage de requête. Ces requêtes de repérage intégrées sont élaborées en continu par les chercheurs Microsoft en matière de sécurité, qui ajoutent de nouvelles requêtes et affinent les requêtes existantes, de manière à vous offrir un point d’entrée pour la recherche de nouvelles détections et le repérage de l’origine des nouvelles attaques. 

- **Puissant langage de requête avec IntelliSense** : les requêtes de repérage sont créées dans le [langage de requête Kusto (KQL)](/azure/data-explorer/kusto/query/), un langage de requête qui vous offre la puissance et la flexibilité dont vous avez besoin pour faire passer le repérage au niveau supérieur. Il s’agit du même langage que celui utilisé par les requêtes dans vos règles analytiques et ailleurs dans Azure Sentinel.

- **Tableau de bord de repérage (préversion)**  : cette mise à niveau de la page principale vous permet d’exécuter toutes vos requêtes, ou un sous-ensemble sélectionné, en un seul clic. Identifiez où commencer le repérage en examinant le nombre de résultats, les pics ou la modification du nombre de résultats sur une période de 24 heures. Vous pouvez également trier et filtrer par favoris, source de données, tactique ou technique MITRE ATT&CK, résultats ou delta de résultat. Affichez les requêtes qui n’ont pas encore les sources de données nécessaires connectées et recevez des recommandations sur la façon d’activer ces requêtes.

- **Créer vos propres signets** : lors du processus de repérage, vous pouvez rencontrer des résultats de requête qui vous semblent inhabituels ou suspects. Vous pouvez « créer un signet » pour ces éléments afin de les enregistrer et les mettre de côté pour pouvoir y revenir ultérieurement. Vous pouvez utiliser vos éléments avec signet pour créer ou enrichir un incident à des fins d’investigation. Pour plus d’informations sur les signets, consultez l’article [Utiliser des signets dans le repérage](bookmarks.md).

- **Utiliser des notebooks pour dynamiser l’investigation** : les notebooks vous offrent un type d’environnement de bac à sable (sandbox) virtuel, avec son propre noyau. Vous pouvez utiliser des notebooks pour améliorer votre repérage et vos investigations avec le Machine Learning, la visualisation et l’analyse des données. Vous pouvez effectuer une investigation complète dans un notebook, en encapsulant les données brutes, le code que vous exécutez sur celles-ci, les résultats et leurs visualisations, et enregistrer tout cela afin de le partager avec d’autres personnes de votre organisation et de le réutiliser. 

- **Interroger les données stockées** : les données sont accessibles dans des tables sur lesquelles vous pouvez exécuter des requêtes. Par exemple, vous pouvez interroger les événements de création de processus, les événements DNS (Domain Name Service), ainsi que de nombreux autres types d’événements.

- **Liens vers la communauté** : tirez parti de la puissance de l’ensemble de la communauté pour rechercher d’autres requêtes et sources de données.
 
## <a name="get-started-hunting"></a>Démarrer le repérage

Dans le Portail Azure Sentinel, cliquez sur **Repérage**.

   :::image type="content" source="media/hunting/hunting-start.png" alt-text="Début du repérage par Azure Sentinel" lightbox="media/hunting/hunting-start.png":::

- Lorsque vous ouvrez la page **Repérage**, toutes les requêtes de repérage s’affichent dans une même table. Cette table répertorie toutes les requêtes rédigées par l’équipe d’analystes de la sécurité Microsoft, ainsi que toutes les autres requêtes que vous pouvez avoir créées ou modifiées. Chaque requête décrit l’objet du repérage, ainsi que le type de données sur lequel ce processus s’exécute. Ces modèles sont regroupés par tactique : les icônes situées sur la droite classent le type de menace (par exemple, accès initial, persistance et exfiltration).

- (Préversion) Pour voir comment les requêtes s’appliquent à votre environnement, cliquez sur le bouton **Exécuter toutes les requêtes (préversion)** ou sélectionnez un sous-ensemble de requêtes à l’aide des cases à cocher situées à gauche de chaque ligne, puis sélectionnez le bouton **Exécuter les requêtes sélectionnées (préversion)** . L’exécution des requêtes peut prendre de quelques secondes à plusieurs minutes, en fonction du nombre de requêtes sélectionnées, de l’intervalle de temps et de la quantité de données interrogées.

- (Préversion) Une fois l’exécution de vos requêtes terminée, vous pouvez voir les requêtes qui ont retourné des résultats à l’aide du filtre **Résultats**. Vous pouvez ensuite effectuer un tri pour voir quelles requêtes ont le plus ou le moins de résultats. Vous pouvez également voir les requêtes qui ne sont pas actives dans votre environnement en sélectionnant *N/A* dans le filtre **Résultats**. Survolez l’icône d’informations (i) en regard de *N/A* pour voir quelles sources de données sont nécessaires pour activer cette requête.

- (Préversion) Vous pouvez identifier les pics de données en triant ou en filtrant les **Résultats Delta**. Cela permet de comparer les résultats des dernières 24 heures par rapport aux résultats des 24-48 heures précédentes pour faciliter la consultation des grandes différences en matière de volume.

- (Préversion) La **barre tactique MITRE ATT&CK**, en haut du tableau, répertorie le nombre de requêtes mappées à chaque tactique MITRE ATT&CK. La barre tactique est mise à jour dynamiquement en fonction de l’ensemble actuel de filtres appliqués. Il s’agit d’un moyen simple de voir quelles tactiques MITRE ATT&CK s’affichent lorsque vous filtrez selon un nombre de résultats donné, un delta de résultat élevé, des résultats *N/A* ou tout autre ensemble de filtres.

- (Préversion) Les requêtes peuvent également être mappées aux techniques MITRE ATT&CK. Vous pouvez filtrer ou trier par techniques MITRE ATT&CK à l’aide du filtre **Technique**. En ouvrant une requête, vous pourrez cliquer sur la technique pour afficher la description de la technique MITRE ATT&CK.

- Vous pouvez enregistrer n’importe quelle requête dans vos favoris. Les requêtes enregistrées dans vos favoris s’exécutent automatiquement chaque fois que vous accédez à la page **Repérage**. Vous pouvez créer votre propre requête de repérage ou cloner et personnaliser un modèle de requête de repérage existant.
 
- En cliquant sur **Exécuter la requête** dans la page de détails d’une requête de repérage, vous pouvez exécuter une requête sans quitter la page de repérage. Le nombre de correspondances s’affiche dans la colonne **Résultats** du tableau. Passez en revue la liste des requêtes de repérage et leurs correspondances.

- Vous pouvez effectuer un examen rapide de la requête sous-jacente dans le volet des détails de la requête. Vous pouvez voir les résultats en cliquant sur le lien **Afficher les résultats de la requête** (sous la fenêtre de requête) ou sur le bouton **Afficher les résultats** (en bas du volet). La requête s’ouvre dans le panneau **Journaux** (Log Analytics) et sous la requête, vous pouvez examiner les correspondances pour la requête.

- Pour conserver les résultats suspects ou intéressants d’une requête dans Log Analytics, cochez les cases des lignes que vous souhaitez conserver, puis sélectionnez **Ajouter un signet**. Cela crée pour chaque ligne marquée un enregistrement (signet) qui contient les résultats de la ligne, la requête qui a créé les résultats et les mappages d’entités pour extraire les utilisateurs, les hôtes et les adresses IP. Vous pouvez ajouter vos propres étiquettes (voir ci-dessous) et des remarques à chaque signet.

- Vous pouvez voir tous les résultats avec un signet en cliquant sur l’onglet **Signets** dans la page **Repérage** principale. Vous pouvez ajouter des étiquettes aux signets pour les classer à des fins de filtrage. Par exemple, si vous examinez une campagne d’attaque, vous pouvez créer une étiquette pour cette dernière, appliquer l’étiquette aux signets pertinents, puis filtrer tous les signets en fonction de la campagne.

- Vous pouvez examiner un seul résultat avec signet en sélectionnant le signet, puis en cliquant sur **Examiner** dans le volet des détails pour ouvrir l’expérience d’investigation. Vous pouvez également créer un incident à partir d’un ou de plusieurs signets, ou ajouter un ou plusieurs signets à un incident existant, en cochant les cases situées à gauche des signets souhaités, puis en sélectionnant **Créer un incident** ou **Ajouter à un incident existant** dans le menu déroulant **Actions d’incident** en haut de l’écran. Vous pouvez ensuite trier et examiner l’incident comme n’importe quel autre.

- Après avoir découvert ou créé une requête de repérage qui fournit de précieux insights sur les attaques possibles, vous pouvez créer des règles de détection personnalisées basées sur cette requête et communiquer ces insights sous forme d’alertes aux gestionnaires des incidents de sécurité. Affichez les résultats de la requête dans Log Analytics (voir ci-dessus), puis cliquez sur le bouton **Nouvelle règle d’alerte** en haut du volet et sélectionnez **Créer une alerte Azure Sentinel**. L’**Assistant Règle analytique** s’ouvre. Suivez les étapes indiquées dans [Tutoriel : créer des règles analytiques personnalisées pour détecter les menaces](tutorial-detect-threats-custom.md).

## <a name="query-language"></a>Langage de requête 

La fonctionnalité de repérage d’Azure Sentinel repose sur le langage de requête Kusto. Pour plus d’informations sur le langage de requête et sur les opérateurs pris en charge, consultez l’article [Référence sur le langage de requête](../azure-monitor/logs/get-started-queries.md).

## <a name="public-hunting-query-github-repository"></a>Référentiel public GitHub de requêtes de repérage

Consultez le [référentiel de requêtes de repérage](https://github.com/Azure/Azure-Sentinel/tree/master/Hunting%20Queries). Complétez et utilisez les exemples de requêtes partagés par nos clients.

 ## <a name="sample-query"></a>Exemple de requête

Une requête classique commence par un nom de table suivi d’une série d’opérateurs séparés par \|.

Dans l’exemple ci-dessus, démarrez par le nom de table SecurityEvent et ajoutez les éléments enchaînés à l’aide d’une barre verticale selon vos besoins.

1. Définissez un filtre de temps pour consulter uniquement les enregistrements des sept jours qui précèdent.

1. Ajoutez un filtre à la requête pour qu’elle présente uniquement l’ID d’événement 4688.

1. Ajoutez un filtre à la requête sur l’élément CommandLine pour qu’il contienne uniquement les instances de cscript.exe.

1. Projetez uniquement les colonnes que vous souhaitez explorer et limitez les résultats à 1 000, puis cliquez sur **Exécuter la requête**.

1. Cliquez sur le triangle vert et exécutez la requête. Vous pouvez tester la requête et l’exécuter pour rechercher un comportement anormal.

## <a name="useful-operators"></a>Opérateurs utiles

Le langage de requête est très puissant grâce à ses nombreux opérateurs. Voici une liste de certains opérateurs particulièrement utiles :

**where** : filtre une table en fonction du sous-ensemble de lignes correspondant à un prédicat.

**summarize** : génère une table qui agrège le contenu de la table d’entrée.

**join** : fusionne les lignes de deux tables pour former une nouvelle table en mettant en correspondance les valeurs des colonnes spécifiées de chaque table.

**count** : renvoie le nombre d’enregistrements indiqué dans le jeu d’enregistrements d’entrée.

**top** : renvoie les N premiers enregistrements triés d’après les colonnes spécifiées.

**limit** : renvoie au maximum le nombre de lignes spécifié.

**project** : sélectionne les colonnes à inclure, renommer ou supprimer, puis insère les nouvelles colonnes calculées.

**extend** : crée des colonnes calculées et les ajoute au jeu de résultats.

**makeset** : renvoie un tableau (JSON) dynamique de l’ensemble de valeurs distinctes prises par Expr dans le groupe.

**find** : recherche les lignes qui correspondent à un prédicat dans un ensemble de tables.

## <a name="save-a-query"></a>Enregistrement d’une requête

Vous pouvez créer ou modifier une requête et l’enregistrer comme votre propre requête ou la partager avec des utilisateurs situés dans le même locataire.

:::image type="content" source="./media/hunting/save-query.png" alt-text="Enregistrer la requête" lightbox="./media/hunting/save-query.png":::

### <a name="create-a-new-hunting-query"></a>Créer une requête de repérage

1. Cliquez sur **Nouvelle requête**.

1. Renseignez tous les champs vides, puis sélectionnez **Créer**.

    :::image type="content" source="./media/hunting/new-query.png" alt-text="Nouvelle requête" lightbox="./media/hunting/new-query.png":::

### <a name="clone-and-modify-an-existing-hunting-query"></a>Cloner et modifier une requête de repérage existante

1. Dans la table, sélectionnez la requête à modifier.

1. Sélectionnez les points de suspension (...) sur la ligne de la requête que vous souhaitez modifier, puis sélectionnez **Cloner la requête**.

    :::image type="content" source="./media/hunting/clone-query.png" alt-text="Cloner la requête" lightbox="./media/hunting/clone-query.png":::

1. Modifiez la requête, puis sélectionnez **Créer**.

    :::image type="content" source="./media/hunting/custom-query.png" alt-text="Requête personnalisée" lightbox="./media/hunting/custom-query.png":::

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer un examen de repérage à l’aide d’Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- [Use notebooks to run automated hunting campaigns](notebooks.md) (Utiliser des notebooks pour exécuter des campagnes de repérage automatisées)
- [Keep track of data during hunting](bookmarks.md) (Suivre des données pendant la recherche)
