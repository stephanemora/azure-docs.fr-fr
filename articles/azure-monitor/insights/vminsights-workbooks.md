---
title: Créez des rapports interactifs avec des classeurs Azure Monitor | Microsoft Docs
description: Simplifier complexe reporting avec des classeurs paramétrables prédéfinis et personnalisés pour Azure Monitor pour les machines virtuelles.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2019
ms.author: magoedte
ms.openlocfilehash: 90c236347380bb5d5e51db56d0f431d2659a7258
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288710"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Créer des rapports interactifs avec les classeurs Azure Monitor

Classeurs combinent du texte, [enregistrer des requêtes](../log-query/query-language.md), mesures et des paramètres dans des rapports interactifs efficaces. Les classeurs sont modifiables par tous les membres de l’équipe ayant accès aux mêmes ressources Azure.

Les classeurs sont utiles pour les scénarios tels que :

* Explorer l’utilisation de votre machine virtuelle lorsque vous ne connaissez pas les mesures d’intérêt à l’avance : L’utilisation du processeur, espace disque, mémoire, les dépendances de réseau, etc. Contrairement à d’autres outils d’analyse de l’utilisation, les classeurs vous permettent de combiner plusieurs types de visualisations et d’analyses, ce qui les rend très utiles pour ce type d’exploration sous forme libre.
* Expliquer à votre équipe comment une machine virtuelle approvisionnée récemment s’exécute, en affichant les métriques pour les compteurs de clés et autres événements du journal.
* Partager les résultats d’une expérience de redimensionnement de votre machine virtuelle avec d’autres membres de votre équipe. Vous pouvez expliquer les objectifs de l’expérience avec le texte, puis afficher les requêtes de métrique et analytique chaque utilisation permettant d’évaluer l’expérience, ainsi que d’effacer des légendes pour que chaque mesure se situe au-dessus ou au-dessous-cibles.
* Création de rapports l’impact d’une panne sur l’utilisation de votre machine virtuelle, combinant des données, explication du texte et une présentation des étapes suivantes afin d’éviter les pannes à l’avenir.

Azure Monitor pour les machines virtuelles inclut plusieurs classeurs pour vous aider à démarrer, et le tableau suivant résume l’ensemble.

| Classeur | Description | Étendue |
|----------|-------------|-------|
| Performances | Fournit une version personnalisable de notre liste des meilleurs N et la vue graphiques dans un classeur unique qui tire parti de tous les compteurs de performances d’Analytique de journal que vous avez activés.| À l’échelle |
| Compteurs de performances | Affichage d’un graphique Top N sur un large ensemble de compteurs de performance. | À l’échelle |
| connexions | Connexions fournit une vue détaillée des connexions entrantes et sortantes à partir de vos machines virtuelles surveillés. | À l’échelle |
| Ports actifs | Fournit une liste des processus qui ont liés aux ports sur les machines virtuelles surveillés et leur activité dans la période choisie. | À l’échelle |
| Ouvrir des ports | Fournit le nombre de ports ouverts sur vos machines virtuelles analysés et les détails sur ces ouvrir des ports. | À l’échelle |
| Connexions ayant échoué | Afficher le nombre de connexions qui ont échoué sur vos machines virtuelles analysés, la tendance à l’échec, et si le pourcentage d’échecs augmente au fil du temps. | À l’échelle |
| Sécurité et audit | Une analyse de votre trafic TCP/IP qui signale sur connexions globales, connexions malveillantes, où les points de terminaison IP se trouvent dans le monde entier.  Pour activer toutes les fonctionnalités, vous devez activer la détection de la sécurité. | À l’échelle |
| Trafic TCP | Un rapport de classement pour vos machines virtuelles surveillés et leur réseau envoyé et reçu de total du trafic dans une grille et affiché sous la forme d’une courbe de tendance. | À l’échelle |
| Comparaison du trafic | Cette classeurs permet de comparer les tendances du trafic réseau pour une seule machine ou un groupe de machines. | À l’échelle |
| Performances | Fournit une version personnalisable de notre affichage des performances qui tire parti de tous les compteurs de performances d’Analytique de journal que vous avez activés. | Machine virtuelle unique | 
| connexions | Connexions fournit une vue détaillée des connexions entrantes et sortantes de votre machine virtuelle. | Machine virtuelle unique |
 
## <a name="starting-with-a-template-or-saved-workbook"></a>Commencer avec un classeur enregistré ou un modèle

Un classeur est composé de sections comprenant des graphiques, des tableaux, du texte et des commandes de saisie modifiables de manière indépendante. Pour mieux comprendre les classeurs, nous allons commencer par ouvrir un modèle et explique comment créer un classeur personnalisé. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Sélectionnez **Machines virtuelles**.

3. Sélectionnez une machine virtuelle dans la liste.

4. Sur la page de la machine virtuelle, dans la section **Surveillance**, sélectionnez **Insights (préversion)**.

5. Dans la page d’informations de machine virtuelle, sélectionnez **performances** ou **Maps** onglet, puis sélectionnez **classeurs View** à partir du lien sur la page. 

    ![Capture d’écran de navigation jusqu’aux classeurs](media/vminsights-workbooks/workbook-option-01.png)

6. Dans la liste déroulante, sélectionnez **accéder à la galerie** à partir du bas de la liste.

    ![Capture d’écran de liste déroulante de classeur](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Il lance la galerie de classeur avec un nombre de classeurs prédéfinis pour vous aider à commencer.

7. Nous allons commencer par le **modèle par défaut** qui se trouve sous le titre **Démarrage rapide**.

    ![Capture d’écran de la galerie de classeurs](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Modification des sections de classeur

Les classeurs présentent deux modes : le **mode d’édition** et le **mode lecture**. Une fois le classeur de modèle par défaut est lancé tout d’abord, il s’ouvre dans **mode d’édition**. Il affiche tout le contenu du classeur, y compris les étapes et les paramètres qui sont masquées dans le cas contraire. Le **mode lecture** présente une vue de rapport simplifiée. Mode de lecture vous permet de soustraire la complexité qui a été placé dans la création d’un rapport tout en conservant les mécanismes sous-jacents seulement quelques clics lorsque nécessaire pour la modification.

![Azure Monitor pour les contrôles d’édition de section de classeurs de machines virtuelles](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Lorsque vous avez terminé une section, cliquez sur **modifications terminées** dans l’angle inférieur gauche de la section.

2. Pour créer un doublon de section, cliquez sur l’icône **Cloner cette section**. La création de sections en double est une bonne façon d’itérer sur une requête sans perdre les itérations précédentes.

3. Pour déplacer une section vers un classeur, cliquez sur l’icône **Déplacer vers le haut** ou **Déplacer vers le bas**.

4. Pour supprimer une section de façon permanente, cliquez sur l’icône **Supprimer**.

## <a name="adding-text-and-markdown-sections"></a>Ajout de texte et de sections Markdown

Le fait d’ajouter des en-têtes, des explications et des commentaires à vos classeurs permet de donner un aspect narratif à un ensemble de tables et de graphiques. Les sections de texte des classeurs prennent en charge la [syntaxe Markdown](https://daringfireball.net/projects/markdown/) pour la mise en forme du texte (en-têtes, gras, italique et listes à puces, par exemple).

Pour ajouter une section de textes à votre classeur, utilisez le bouton **Ajouter du texte** situé en bas du classeur, ou de chaque section.

## <a name="adding-query-sections"></a>Ajout de sections de requête

![Section de requête d’un classeur](media/vminsights-workbooks/005-workbook-query-section.png)

Pour ajouter une section de requête à votre classeur, utilisez le bouton **Ajouter une requête** situé en bas du classeur ou en bas de chaque section.

Les sections de requête sont extrêmement flexibles et peuvent être utilisées pour répondre à des questions telles que :

* Comment était mon utilisation du processeur au cours de la même période qu’une augmentation du trafic réseau ?
* Quelle était la tendance dans l’espace disque disponible au cours du mois dernier ?
* Échecs de connexion réseau combien prions sincèrement ma machine virtuelle sur les deux dernières semaines ? 

Vous aussi ne sont pas uniquement limité à interroger à partir du contexte de la machine virtuelle vous lancé le classeur à partir de. Vous pouvez interroger sur plusieurs machines virtuelles, ainsi que les espaces de travail Analytique de journal, tant que vous avez l’autorisation d’accès à ces ressources.

Pour inclure des données à partir d’autres espaces de travail Analytique de journal ou à partir d’une application Application Insights spécifique en utilisant la **espace de travail** identificateur. Pour en savoir plus sur les requêtes d’inter-ressources, reportez-vous à la [des conseils officiels](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Paramètres de requête analytique avancés

Chaque section a ses propres paramètres avancés, qui sont accessibles via les paramètres ![contrôles d’édition de la section Workbooks](media/vminsights-workbooks/006-settings.png) icône située à droite de la **ajouter des paramètres** bouton.

![Azure Monitor pour les contrôles d’édition de section de classeurs de machines virtuelles](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Largeur personnalisée**    | Met un élément à une taille arbitraire, donc vous pouvez insérer le nombre d’éléments sur une seule ligne, ce qui vous permet de mieux organiser vos tables et graphiques dans des rapports interactifs efficaces.  |
| **Visible sous conditions** | Spécifiez cette option pour masquer les étapes selon un paramètre en mode lecture. |
| **Exporter un paramètre**| Autoriser une ligne sélectionnée dans la grille ou un graphique à provoquer des étapes ultérieures à modifier des valeurs ou deviennent visibles.  |
| **Afficher la requête en mode lecture** | Affiche la requête au-dessus du graphique ou de la table même en mode lecture.
| **Afficher le bouton Ouvrir dans Analytics en mode lecture** | Ajoute l’icône bleue Analytique à l’angle droit du graphique pour autoriser l’accès d’un clic.|

La plupart de ces paramètres sont relativement intuitifs, mais pour comprendre comment fonctionne l’option **Exporter un paramètre**, il est préférable d’examiner un classeur qui utilise cette fonctionnalité.

Un des classeurs prédéfinis - **le trafic TCP**, fournit des informations sur les métriques de connexion à partir d’une machine virtuelle.

La première section du classeur est basée sur les données de requête de journal. La deuxième section est également basée sur les données de requête de journal, mais en sélectionnant une ligne dans la première table sera mise à jour interactive le contenu des graphiques :

![Azure Monitor pour les contrôles d’édition de section de classeurs de machines virtuelles](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Le comportement est possible via l’utilisation de la **lorsqu’un élément est sélectionné, un paramètre d’exportation** les paramètres avancés, qui sont activées dans la requête de journal de la table.

![Azure Monitor pour les contrôles d’édition de section de classeurs de machines virtuelles](media/vminsights-workbooks/009-settings-export.png)

La deuxième requête de journal utilise ensuite les valeurs exportées lorsqu’une ligne est sélectionnée pour créer un ensemble de valeurs qui sont ensuite utilisés par l’en-tête de section et les graphiques. Si aucune ligne n’est sélectionnée, elle masque l’en-tête de section et les graphiques. 

Par exemple, le paramètre masqué dans la deuxième section utilise la référence suivante à partir de la ligne sélectionnée dans la grille :

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Ajout de sections de métriques

Les sections de métriques vous offrent un accès complet pour intégrer des données de métriques Azure Monitor dans vos rapports interactifs. Dans Azure Monitor pour les machines virtuelles, les classeurs prédéfinis contient généralement des données de requête analytique plutôt que des données de mesure.  Vous pouvez choisir créer des classeurs avec des données de mesure, ce qui vous permet de tirer pleinement parti de la meilleure des deux fonctionnalités toutes au même endroit. Vous avez également la possibilité d’extraire des données de métriques à partir des ressources de l’ensemble des abonnements auxquels vous avez accès.

Voici un exemple de données de machine virtuelle en cours d’extraction dans un classeur pour fournir une visualisation de la grille des performances du processeur :

![Azure Monitor pour les contrôles d’édition de section de classeurs de machines virtuelles](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Ajout de sections de paramètres

Les paramètres de classeur vous permettent de modifier les valeurs dans le classeur sans avoir à modifier manuellement les sections de requête ou de texte. Davantage de personnes peuvent ainsi utiliser ce type de rapports basés sur des classeurs, car il n’est pas nécessaire de comprendre le langage de requête analytique sous-jacent.

Pour remplacer la valeur d’un paramètre dans les sections de requête, de texte ou de paramètre, le nom du paramètre doit être saisi entre accolades, par exemple ``{parameterName}``. Les noms de paramètre sont limités à des règles similaires en tant qu’identificateurs de JavaScript, des caractères alphabétiques ou des traits de soulignement, suivis par des caractères alphanumériques ou des traits de soulignement. Par exemple, **a1** est autorisé, mais pas **1a**.

Les paramètres sont linéaires et appliqués aux différentes étapes du classeur, en partant du haut.  Les paramètres déclarés ultérieurement dans un classeur peuvent remplacer les paramètres qui ont été déclarées précédemment. Cela permet également de paramètres qui utilisent des requêtes pour accéder aux valeurs de paramètres définis précédemment. Au sein d’une étape de paramètre, les paramètres sont également linéaires et appliqués de gauche à droite, les paramètres de droite pouvant dépendre d’un paramètre déclaré antérieurement au sein de cette même étape.
 
Il existe quatre types de paramètres, qui sont actuellement prises en charge :

|                  |      |
| ---------------- |:-----|
| **Text**    | Permet à l’utilisateur modifier une zone de texte, et vous pouvez éventuellement fournir une requête pour remplir la valeur par défaut. |
| **Liste déroulante** | Permet à l’utilisateur de choisir parmi un ensemble de valeurs. |
| **Sélecteur d’intervalle de temps**| Permet à l’utilisateur à choisir parmi un ensemble prédéfini de valeurs de plage de temps, ou sélectionner dans une plage horaire personnalisée.|
| **Sélecteur de ressource** | Permet à l’utilisateur à choisir parmi les ressources sélectionnées pour le classeur.|

### <a name="using-a-text-parameter"></a>Utilisation d’un paramètre de texte

La valeur d’un utilisateur tape dans la zone de texte est remplacé directement dans la requête, sans séquence d’échappement ni mise entre guillemets. Si la valeur dont vous avez besoin est une chaîne, le paramètre doit être saisi entre guillemets dans la requête (par exemple **‘{paramètre}’**).

Le paramètre de texte autorise une valeur dans une zone de texte à utiliser n’importe où. Cela peut être un nom de tableau, un nom de colonne, un nom de fonction, un opérateur, etc.  Le type de paramètre de texte a un paramètre **obtenir la valeur par défaut à partir de la requête d’analytique**, ce qui permet au créateur du classeur utiliser une requête pour remplir la valeur par défaut pour cette zone de texte.

Lorsque vous utilisez la valeur par défaut à partir d’une requête de journal, uniquement la première valeur de la première ligne (ligne 0, la colonne 0) est utilisée comme valeur par défaut. Par conséquent, il est recommandé de limiter votre requête afin qu’elle renvoie une seule ligne et une seule colonne. Toutes les autres données retournées par la requête sont ignorées. 

La valeur retournée par la requête est remplacée directement sans espace ni guillemets. Si la requête ne retourne aucune ligne, le résultat du paramètre est une chaîne vide (si le paramètre n’est pas obligatoire) ou une chaîne non définie (si le paramètre est obligatoire).

### <a name="using-a-drop-down"></a>À l’aide d’une liste déroulante.

Le type de paramètre de liste déroulante vous permet de créer un contrôle de liste déroulante, ce qui permet la sélection d’une ou plusieurs valeurs.

La liste déroulante est remplie par une requête de journal ou un JSON. Si la requête retourne une colonne, les valeurs dans cette colonne sont la valeur et l’étiquette dans le contrôle de liste déroulante. Si la requête retourne deux colonnes, la première colonne est la valeur, et la deuxième colonne est le libellé qui apparaît dans la liste déroulante. Si la requête retourne trois colonnes, la troisième colonne est utilisée pour indiquer la sélection par défaut dans cette liste déroulante. Cette colonne peut être de n’importe quel type, mais le plus simple est d’utiliser des valeurs booléennes ou numériques, où 0 correspond à false et 1 à true.

Si la colonne est de type chaîne, la chaîne null/vide est considérée comme fausse (false), et toute autre valeur est considérée comme vraie (true). Pour une sélection unique listes déroulantes, la première valeur avec une valeur true est utilisée en tant que la sélection par défaut.  Pour plusieurs sélection listes déroulantes, toutes les valeurs avec une valeur true sont utilisées en tant que le jeu sélectionné par défaut. Les éléments dans la liste déroulante s’affichent dans l’ordre qui la requête a retourné des lignes. 

Examinons les paramètres présents dans le rapport vue d’ensemble de connexions. Cliquez sur le symbole de modification en regard **Direction**.

![Azure Monitor pour les contrôles d’édition de section de classeurs de machines virtuelles](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Cette action lance la **modifier le paramètre** élément de menu.

![Azure Monitor pour les contrôles d’édition de section de classeurs de machines virtuelles](media/vminsights-workbooks/012-workbook-edit-parameter.png)

Le code JSON vous permet de générer une table arbitraire remplie avec le contenu. Par exemple, le code JSON suivant génère deux valeurs dans la liste déroulante :

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Un exemple plus applicable utilise une liste déroulante pour choisir parmi un ensemble de compteurs de performances par nom :

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

La requête affiche les résultats comme suit :

![Liste déroulante de compteur de performances](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Listes déroulantes sont incroyablement puissants outils de personnalisation et création de rapports interactifs.

### <a name="time-range-parameters"></a>Paramètres d’intervalle de temps

Bien que vous puissiez définir votre propre paramètre d’intervalle de temps personnalisé via le type de paramètre liste déroulante, vous pouvez également utiliser le type de paramètre intervalle de temps prêt à l’emploi si vous n’avez pas besoin du même niveau de flexibilité. 

Les types de paramètre intervalle de temps ont 15 plages par défaut qui vont de cinq minutes aux 90 derniers jours. Il est également possible de sélectionner un intervalle de temps personnalisé, ce qui permet au créateur du rapport de sélectionner des valeurs de début et de fin spécifiques pour l’intervalle de temps.

### <a name="resource-picker"></a>Sélecteur de ressource

Le type de paramètre de sélecteur de ressource vous donne la possibilité de limiter ou d’étendre votre rapport à certains types de ressources. Un exemple de classeur prédéfini qui s’appuie sur le type de sélecteur de ressource est la **performances** classeur.

![Liste déroulante des espaces de travail](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Enregistrer et partager des classeurs avec votre équipe

Les classeurs sont enregistrés dans un espace de travail Analytique de journal ou une ressource de machine virtuelle, selon la façon dont vous accéder à la galerie de classeurs. Le classeur peut être enregistré dans le **Mes rapports** section est privée ou dans le **rapports partagés** section qui est accessible à tous les utilisateurs ayant accès à la ressource. Pour afficher tous les classeurs de la ressource, cliquez sur le bouton **Ouvrir** dans la barre d’action.

Pour partager un classeur qui se trouve actuellement dans **Mes rapports** :

1. Dans la barre d’action, cliquez sur **Ouvrir**.
2. Cliquez sur le bouton «... » en regard du classeur que vous souhaitez partager.
3. Cliquez sur **Déplacer vers les rapports partagés**.

Pour partager un classeur avec un lien ou par e-mail, cliquez sur **Partager** dans la barre d’action. Gardez à l’esprit que les destinataires du lien ont besoin d’accéder à cette ressource dans le portail Azure pour afficher le classeur. Pour apporter des modifications, les destinataires doivent au moins disposer des autorisations de collaborateur pour la ressource.

Pour épingler un lien à un classeur dans un tableau de bord Azure :

1. Dans la barre d’action, cliquez sur **Ouvrir**.
2. Cliquez sur le bouton «... » en regard du classeur que vous souhaitez épingler.
3. Cliquez sur **Épingler au tableau de bord**.

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment utiliser la fonctionnalité de contrôle d’intégrité, consultez [afficher l’intégrité de machine virtuelle Azure](vminsights-health.md), ou pour afficher les dépendances des applications découvertes, consultez [vue Azure Monitor pour le mappage des machines virtuelles](vminsights-maps.md). 