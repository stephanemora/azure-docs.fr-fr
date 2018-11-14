---
title: Définir une vue personnalisée – Recherche personnalisée Bing
titlesuffix: Azure Cognitive Services
description: Décrit comment créer des services de site et de recherche verticale
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 5e168608202f81f698221b52315de83e9713feab
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282918"
---
# <a name="configure-your-custom-search-experience"></a>Configurer votre expérience de recherche personnalisée

Une instance de Recherche personnalisée vous permet d’adapter l’expérience de recherche pour inclure uniquement du contenu provenant de sites Web qui intéressent vos utilisateurs. Plutôt que d’effectuer une recherche à l’échelle du Web, Bing ne recherche que dans la section du web qui vous convient. Pour créer votre vue personnalisée du Web, utilisez le [portail](https://customsearch.ai) de la Recherche personnalisée Bing. Pour plus d’informations sur la connexion au portail, consultez [Créer votre première instance Recherche personnalisée Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/quick-start). 

Le portail vous permet de créer une instance de recherche qui spécifie les domaines, sous-pages et pages web que Bing doit ou ne doit pas utiliser pour ses recherches. Non seulement vous spécifiez les URL du contenu que vous connaissez, mais vous pouvez également demander au portail de suggérer du contenu susceptible de vous intéresser et que vous pouvez ajouter à votre vue. 

Voici de quelle façon vous pouvez définir une section du Web : 

1.  Domaine. Une section de domaine inclut tout le contenu trouvé dans un domaine internet. Par exemple, www.microsoft.com. L’omission de « www » oblige Bing à rechercher également dans les sous-domaines du domaine. Par exemple, si vous spécifiez microsoft.com, Bing renvoie aussi les résultats provenant de support.microsoft.com ou de technet.microsoft.com.  
  
2.  Sous-page. Une section de sous-page comprend tout le contenu trouvé dans la sous-page, et les chemins situés en dessous de celle-ci. Vous pouvez indiquer un maximum de deux sous-pages dans le chemin. Par exemple, www.microsoft.com/fr-fr/windows/  
  
3.  Page Web. Une section de page Web ne peut inclure que cette page Web dans une recherche personnalisée. Si vous le souhaitez, vous pouvez préciser s’il faut inclure ou non les sous-pages.

Tous les domaines, sous-pages et pages Web que vous spécifiez doivent être publics et indexés par Bing. Si vous possédez un site public à inclure dans la recherche, et que Bing ne l’a pas l’indexé, consultez la [documentation du webmaster](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) Bing pour savoir comment obliger Bing à l’indexer. Consultez également la documentation du webmaster pour savoir comment forcer Bing à mettre à jour votre site analysé si l’index est obsolète.

## <a name="adding-slices-to-your-custom-search"></a>Ajout de sections à votre recherche personnalisée

Lorsque vous définissez votre instance de recherche personnalisée, vous spécifiez les éléments actifs et bloqués, c’est-à-dire les domaines, sous-pages et pages Web dans lesquels vous souhaitez effectuer ou non des recherches.  

- Active (Éléments actifs) : liste contenant les domaines, sous-pages ou pages Web à inclure dans la recherche.  
  
- Blocked (Éléments bloqués) : liste contenant les domaines, sous-pages ou pages Web à exclure de la recherche. Le contenu des éléments que vous bloquez doit se trouver sous les domaines et les sous-pages figurant dans votre liste Active (Éléments actifs).

Pour accéder à la liste des éléments actifs ou des éléments bloqués, cliquez sur les onglets correspondants dans votre instance de recherche personnalisée. 

<a name="active-and-blocked-lists"></a>
## <a name="active-and-blocked-lists"></a>Listes des éléments actifs et des éléments bloqués 

Pour indiquer la section du web dans laquelle vous souhaitez que Bing effectue les recherches, cliquez sur l’onglet **Active** (Éléments actifs) et répertoriez les domaines, sous-pages et pages Web où rechercher. Vous pouvez ajouter une section directement à la liste, ou ajouter plusieurs sections en chargeant un fichier texte par le biais de l’icône de chargement.

Informations détaillées sur File Upload (Charger un fichier) : 

- Le chargement de fichier n’est disponible que pour l’ajout de sections à la liste des éléments actifs ; vous ne pouvez pas l’utiliser pour ajouter des sections à la liste des éléments bloqués.  
  
- Créez un fichier texte et indiquez un seul domaine, sous-page ou page Web par ligne. L’intégralité du chargement est rejeté si une erreur se produit.  
  
- Si la liste des éléments bloqués contient la sous-page, la page Web ou le domaine que vous avez indiqué dans le fichier chargé, le service le supprime de la liste des éléments bloqués et l’ajoute à la liste des éléments actifs.  
  
- Le service ignore les doublons dans le fichier chargé.

Pour modifier ou supprimer des sections, utilisez les options de la colonne **Controls** (Contrôles). 

De même, vous pouvez ajouter des sections à la liste des éléments bloqués (mais vous ne pouvez pas utiliser un fichier à charger pour spécifier ces sections).

## <a name="pinned-list"></a>Liste des éléments épinglés

Le portail vous permet également d’épingler une page Web particulière en haut des résultats de recherche si l’utilisateur entre un terme de recherche spécifique. L’onglet **Pinned** (Éléments épinglés) contient une liste de paires, constituées de termes de requête associés à des pages Web, qui spécifient la page Web devant s’afficher en tant que meilleur résultat pour une requête particulière. En outre, seules les pages web indexées apparaîtront dans les recherches. Pour plus d’informations sur l’épinglage de résultats, consultez [Classer par ordre de priorité](#adjustrank). 

L’épinglage de résultats n’est pas disponible pour la recherche d’images et la recherche de vidéos.

## <a name="website-suggestions"></a>Suggestions de sites web

Après avoir ajouté des sections à la liste des éléments actifs, le service génère des suggestions de sites web et de sous-pages que vous pouvez trouver intéressant d’ajouter à votre recherche. La zone **You might want to add** (Vous aimeriez peut-être ajouter) contient ces suggestions. La page de paramètres de l’instance comprend cette zone uniquement si des suggestions sont disponibles. 

Pour ajouter des suggestions à votre liste d’éléments actifs, cliquez sur l’icône +.  Étant donné que le service génère des suggestions en fonction de vos paramètres, n’oubliez pas de cliquer sur **Actualiser** après avoir ajouté les suggestions. 

## <a name="preview-pane"></a>Volet de visualisation

Vous pouvez tester votre instance de recherche en utilisant le volet de visualisation situé à droite pour soumettre des requêtes de recherche et afficher les résultats. 

1. Sélectionnez **My Instance** (Mon Instance).
2. Sélectionnez un filtre de recherche sécurisée et le marché pour lequel effectuer la recherche (voir [Paramètres de requête](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)).
3. Entrez une requête et appuyez sur Entrée ou cliquez sur l’icône de recherche pour afficher les résultats issus de la configuration active. 

Pour sélectionner le type de recherche à effectuer, cliquez sur **Web** pour obtenir des résultats web, sur **Image** pour obtenir des résultats d’images, ou sur **Vidéo** pour obtenir des résultats de vidéos. 

Lorsque vous utilisez le volet de visualisation, vous pouvez également consulter les résultats de Bing en sélectionnant **Bing** au lieu de **My Instance** (Mon instance). Il peut être utile de comparer les résultats issus de votre expérience de recherche avec les résultats retournés par Bing.

<a name="adjustrank"></a>
## <a name="adjust-rank"></a>Classer par ordre de priorité

Le portail vous permet de modifier la position d’éléments dans le classement pour exploiter les résultats que Bing retourne. Dans le volet de visualisation, entrez un terme à rechercher et exécutez la requête. Le volet de visualisation affiche la liste des résultats de la recherche pour la requête. À droite de chaque résultat se trouve la liste des réglages que vous pouvez effectuer. 

- Bloquer. Déplace le domaine, la sous-page ou la page Web dans la liste des éléments bloqués. Vous sélectionnez le niveau à bloquer. Bing exclut le contenu du site sélectionné dans les résultats de recherche.  
  
- Boost (Élever). Élève le contenu du domaine ou de la page secondaire à un niveau supérieur dans les résultats de recherche. Vous choisissez s’il faut privilégier le contenu issu de la sous-page ou du domaine auquel la page web appartient. [En savoir plus](#boosting-and-demoting)  
  
- Demote. (Rétrograder.) Rétrograde le contenu du domaine ou de la page secondaire à un niveau inférieur dans les résultats de recherche. Vous choisissez s’il faut rétrograder le contenu issu de la sous-page ou du domaine auquel la page Web appartient. [En savoir plus](#boosting-and-demoting).  
  
- Pin-to-top (Épingler en haut). Définit la page web qui s’affiche en haut des résultats si la chaîne de requête de l’utilisateur correspond à la chaîne de requête de l’épingle, selon les conditions de correspondance de l’épingle. Il n’est pas nécessaire que la liste des éléments actifs contienne cette page Web pour que vous puissiez l’épingler. [En savoir plus](#pin-to-top).

La modification du classement par ordre de priorité n’est pas disponible pour la recherche d’images et la recherche de vidéos.

## <a name="boosting-and-demoting"></a>Élévation et rétrogradation

Vous pouvez élever, un peu ou beaucoup, ou rétrograder tout domaine ou sous-page dans votre liste d’éléments actifs. Par défaut, toutes les sections sont ajoutées avec la même pondération. Les éléments qui sont élevés (Boosted) ou beaucoup élevés (Super boosted) dans le classement bénéficient d’une meilleure position dans les résultats de recherche (la position très élevée étant supérieure à la position élevée). Les éléments qui sont rétrogradés sont situés plus bas dans les résultats de recherche.

Il est important de noter que ces trois positions donnent respectivement des pondérations différentes aux domaines ou aux sous-pages. Il ne s’agit que d’un signal parmi tant d’autres utilisés par la fonction de classement pour déterminer l’ordre des résultats. Cela signifie que leur effet pour une requête spécifique n’est pas garanti, car beaucoup d’autres facteurs peuvent influer sur le classement général des résultats Web.  Pour déterminer l’impact éventuel de cette élévation ou rétrogradation sur la fonction de classement, testez l’expérience de recherche par le biais du volet de visualisation.

Vous pouvez élever les éléments, un peu ou beaucoup, ou les rétrograder en utilisant les contrôles Ranking Adjust (Classer par ordre de priorité) dans la liste Active (Éléments actifs), ou à l’aide des contrôles Boost (Élever) et Demote (Rétrograder) dans le volet de visualisation. Le service ajoute la section à votre liste d’éléments actifs et ajuste le classement en conséquence.

Les modifications d’élévation (moyenne ou forte) et de rétrogradation ne sont pas disponibles pour la recherche d’images et la recherche de vidéos.

## <a name="pin-to-top"></a>Épingler en haut

Pour épingler une page Web en tête des résultats de recherche pour une requête spécifique, choisissez l’une des options suivantes :

1.  Dans l’onglet  **Pinned**  (Éléments épinglés), entrez l’URL de la page web à épingler en haut des résultats, ainsi que la requête qui déclenche l’épinglage.  
  
2.  Dans le volet **Preview** (Aperçu), entrez un terme de requête, puis cliquez sur Search (Rechercher). Ensuite, dans les résultats, identifiez la page Web que vous souhaitez épingler au début de la liste des résultats si l’utilisateur entre la même requête. Ensuite, cliquez sur  **Pin to top** (Épingler en haut). Le service ajoute la page web et la requête à la liste des éléments épinglés (**Pinned**). 

Vous pouvez suivre vos épingles dans l’onglet  **Pinned**  (Éléments épinglés). Les épingles sont affichées sous la forme de paires « \<page Web\>, \<requête\> ». 

Pour une requête spécifique, vous ne pouvez épingler qu’une page Web en haut des résultats.

Les épingles ne sont pas disponibles pour la recherche d’images et la recherche de vidéos.

### <a name="specifying-the-pins-match-condition"></a>Spécification de la condition de correspondance de l’épingle

La page web n’est épinglée en haut des résultats que si la chaîne de requête de l’utilisateur correspond à la chaîne de requête de l’épingle, selon la condition de correspondance de l’épingle. Par défaut, une correspondance d’épingle n’est trouvée que si la chaîne de requête de l’épingle et celle de l’utilisateur correspondent exactement. Vous pouvez modifier le comportement par défaut en spécifiant l’une des conditions de correspondance suivantes.

- Commence par &mdash; Une correspondance d’épingle est trouvée si la chaîne de requête de l’utilisateur commence par la chaîne de requête de l’épingle.
- Se termine par &mdash; Une correspondance d’épingle est trouvée si la chaîne de requête de l’utilisateur se termine par la chaîne de requête de l’épingle.
- Contient &mdash; Une correspondance d’épingle est trouvée si la chaîne de requête de l’utilisateur contient la chaîne de requête de l’épingle.

Pour modifier la condition de correspondance de l’épingle, cliquez sur l’icône de modification de l’épingle (représentée par un crayon). Dans la colonne **Query match condition** (Condition de correspondance de la requête), cliquez sur la liste déroulante, puis sélectionnez la nouvelle condition à utiliser. Ensuite, cliquez sur l’icône d’enregistrement pour enregistrer les modifications.

Les comparaisons ne respectent pas la casse.

### <a name="changing-the-order-of-the-pins"></a>Changement de l’ordre des épingles

Pour changer l’ordre des épingles, vous pouvez les glisser-déplacer ou modifier leur numéro de classement. Pour glisser-déplacer une épingle de la liste, cliquez dessus, gardez le bouton de la souris enfoncé, faites glisser l’épingle au-dessus de celle que vous souhaitez remplacer, puis relâchez le bouton de la souris. Notez que le numéro de classement de l’épingle a changé.

Vous pouvez également modifier le numéro de l’épingle. Dans la colonne **Controls** (Contrôles), cliquez sur l’icône de modification de l’épingle (représentée par un crayon). Entrez le numéro correspondant à la position souhaitée de l’épingle dans la liste. Ensuite, appuyez sur Entrée ou cliquez sur l’icône d’enregistrement. Par exemple, si l’épingle se trouve à la sixième position dans la liste et que vous souhaitez qu’elle se trouve à la deuxième position, remplacez son numéro de classement par 2.

Si plusieurs correspondances d’épingles sont trouvées, l’ordre des épingles déterminera celle que devra utiliser Bing. Par exemple, si les épingles 2 et 3 correspondent, Bing utilisera l’épingle 2.

## <a name="use-bing-to-specify-slices"></a>Utiliser Bing pour spécifier des sections

Il existe deux manières de spécifier les sections du Web qui composent votre recherche personnalisée. Si vous connaissez les sections que vous souhaitez inclure dans votre instance, ajoutez-les à la liste des éléments actifs (**Active**) de votre instance. Pour plus d’informations sur l’ajout par vous-même d’éléments dans la liste **Active** (Éléments actifs), consultez [Listes des éléments actifs et des éléments bloqués](#active-and-blocked-lists).

En revanche, si vous ne savez pas quelles sections inclure, vous pouvez exécuter des requêtes Bing dans le volet **Preview** (Aperçu) et voir ce que Bing retourne. Vous pouvez ensuite sélectionner les sections que vous voulez inclure dans votre recherche personnalisée. Cela signifie probablement que devrez exécuter plusieurs termes de requête, afin de vous assurer de l’identification de toutes les sections souhaitées pour votre instance. 

Suivez ces étapes pour que Bing ajoute des sections à votre instance Recherche personnalisée. 

1.  Connectez-vous au [portail](https://customsearch.ai) de la Recherche personnalisée Bing.
2.  Créez une instance ou sélectionnez une instance à mettre à jour.
3.  Dans le volet Preview (Aperçu) situé à droite, sélectionnez Bing dans la liste déroulante.
4.  Dans la zone de recherche, entrez un terme de requête pertinent par rapport à votre instance.
5.  Cliquez sur **Add site** (Ajouter le site) en regard du résultat que vous souhaitez inclure.
6.  Cliquez sur le bouton **OK** .

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="view-statistics"></a>Consulter les statistiques

Si vous êtes abonné au niveau approprié de la Recherche personnalisée (voir les [Tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), un onglet **Statistics** (Statistiques) est ajouté à vos instances de production. Cet onglet de statistiques affiche des informations détaillées sur l’utilisation de vos points de terminaison de Recherche personnalisée, notamment sur le volume d’appels, les requêtes principales, la répartition géographique, les codes de réponse et la recherche sécurisée. Vous pouvez filtrer ces informations à l’aide des contrôles fournis.

## <a name="understanding-quota"></a>Présentation des quotas

- Pour chaque instance de recherche personnalisée, le nombre maximal d’ajustements de classement que vous pouvez apporter aux sections **Active** (Éléments actifs) et **Blocked** (Éléments bloqués) est limité à 400.
- L’ajout d’une section à l’onglet Active (Éléments actifs) ou Blocked (Éléments bloqués) compte pour un ajustement de classement.
- L’élévation et la rétrogradation comptent pour deux ajustements de classement.
- Pour chaque instance de recherche personnalisée, le nombre maximal d’épingles à utiliser est limité à 200.

## <a name="next-steps"></a>Étapes suivantes

- [Appeler votre recherche personnalisée](./search-your-custom-view.md)
- [Configurer votre expérience d’interface utilisateur hébergée](./hosted-ui.md)
- [Utiliser des marqueurs d’ornement pour mettre en surbrillance du texte](./hit-highlighting.md)
- [Paginer des pages web](./page-webpages.md)