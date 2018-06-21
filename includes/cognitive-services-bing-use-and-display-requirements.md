---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: MikeDodaro
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/19/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 174af83686eba665a729246be7a477b9a5054f30
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2018
ms.locfileid: "35370948"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Conditions d’utilisation et d’affichage de l’API Recherche Bing

Les conditions d’utilisation et d’affichage s’appliquent à toute implémentation du contenu et des informations associées. Par exemple, ces conditions s’appliquent aux relations, métadonnées et autres signaux. Ces derniers peuvent être disponibles via des appels aux API suivantes :

- Recherche personnalisée Bing
- Recherche d’entité Bing
- Recherche d’images Bing
- Recherche Bing Actualités
- Recherche de vidéos Bing
- Recherche visuelle Bing
- Recherche web Bing
- Vérification orthographique Bing
- Suggestion automatique Bing

Vous trouverez des informations détaillées sur l’implémentation de ces conditions dans la documentation relative à des fonctionnalités et des résultats spécifiques.     

## <a name="bing-spell-check-and-bing-autosuggest-apis"></a>API Vérification orthographique Bing et Suggestion automatique Bing

Ne pas :

- copier, stocker ou mettre en cache des données provenant des API Vérification orthographique Bing ou Suggestion automatique Bing ;
- utiliser des données provenant des API Vérification orthographique Bing ou Suggestion automatique Bing dans le cadre de Machine Learning ou d’une activité algorithmique similaire. N’utilisez pas ces données pour former, évaluer ou améliorer des services nouveaux ou existants que vous ou des parties tierces pourriez proposer.

## <a name="definitions"></a>Définitions

- *Réponse* fait référence à une catégorie de résultats renvoyés dans une réponse. Par exemple, une réponse de l’API Recherche web Bing peut inclure des réponses appartenant aux catégories de résultats page web, image, vidéo, visuel et actualités.   
- *Réponse* signifie toutes les réponses et toutes les données associées reçues en réponse à un seul appel à une API Recherche.
- *Résultat* fait référence à un élément d’information dans une réponse. Par exemple, le jeu de données connecté à un article d’actualités est un résultat dans une réponse d’actualités.
- *API Recherche* signifie, collectivement, les API Recherche personnalisée, Recherche d’entités, Recherche d’images, Recherche d’actualités, Recherche de vidéos, Recherche visuelle et Recherche web Bing. 


## <a name="search-apis"></a>API Recherche

Les conditions requises de cette section s’appliquent aux API Recherche. Les API Recherche n’incluent pas Vérification orthographique Bing et Suggestion automatique Bing. Les conditions requises pour ces deux API sont décrites dans la section précédente.

### <a name="internet-search-experience"></a>Expérience de recherche Internet

Toutes les données renvoyées dans les réponses ne peuvent être utilisées que dans les expériences de recherche Internet. Une expérience de recherche Internet signifie que le contenu affiché, le cas échéant : 
- est pertinent et réactif à la requête directe de l’utilisateur final ou autre indication de l’intérêt et de l’intention de recherche de l’utilisateur (par exemple, les requêtes de recherche indiquées par l’utilisateur) ; 
- permet aux utilisateurs de rechercher des sources de données et d’y accéder (par exemple, les URL fournies sont implémentées en tant que liens hypertexte de sorte que le contenu ou l’attribution est un lien actionnable affichant les données de manière visible). ou, en cas d’utilisation de l’API Recherche d’entités Bing, est accompagné d’un lien visible vers l’URL bing.com fournie dans la réponse, pour permettre à l’utilisateur d’accéder aux résultats de la recherche de la requête pertinente sur bing.com ;
- inclut plusieurs résultats parmi lesquels l’utilisateur peut effectuer sa sélection (par exemple, plusieurs résultats ou tous les résultats de la réponse d’actualités sont affichés) ; 
- est limité à une quantité appropriée pour servir l’objectif de recherche (par exemple, les images miniatures sont miniatures par rapport à l’affichage de l’utilisateur) ; 
- inclut une indication visible pour l’utilisateur signalant que le contenu est issu de résultats de recherche Internet (par exemple, une mention « extrait du web ») ;
- inclut toute autre combinaison de mesures appropriées pour garantir que l’utilisation des données reçues à partir des API Recherche n’enfreint pas la législation applicable ou les droits des tiers. Par exemple, si vous utilisez une licence Creative Commons, vous respectez les termes du contrat de licence applicable. Consultez vos conseillers juridiques pour déterminer les mesures appropriées.
La seule exception à la condition d’expérience de recherche Internet concerne la détection d’URL, comme décrit plus loin dans cet article. 

### <a name="restrictions"></a>Restrictions

Ne pas :

- copier, stocker ou mettre en cache les données issues des réponses (sauf en cas de rétention dans la mesure permise par la section « Continuité de service » plus loin dans cet article) ; 
- utiliser les données provenant des API Recherche dans le cadre de Machine Learning ou d’une activité algorithmique similaire. N’utilisez pas ces données pour former, évaluer ou améliorer des services nouveaux ou existants que vous ou des parties tierces pourriez proposer ;
- modifier le contenu des résultats (sauf reformatage n’altérant pas toute autre condition), sauf si la loi l’exige ou si Microsoft a donné son accord ; 
- omettre l’attribution et les URL associées au contenu des résultats ;
- réorganiser (y compris par omission) des résultats affichés dans une réponse lorsqu’un ordre ou un classement est fourni, sauf si la loi l’exige ou si Microsoft a donné son accord. (Pour l’API Recherche personnalisée Bing, cette règle ne s’applique pas à la réorganisation implémentée via le portail customsearch.ai.) ;
- afficher d’autres contenus dans toute partie d’une réponse qui pourraient laisser penser à l’utilisateur que ces contenus font partie de la réponse ; 
- afficher des publicités qui ne sont pas fournies par Microsoft sur n’importe quelle page affichant une partie d’une réponse ; 
- afficher des publicités avec les réponses (i) provenant des API Recherche d’images, Recherche d’actualités, Recherche de vidéos ou Recherche visuelle Bing ; ou (ii) qui sont filtrées ou principalement (ou exclusivement) limitées aux résultats de type image, actualités, vidéos et/ou visuels.

### <a name="notices-and-branding"></a>Avis et marque 

- Affichez de manière visible un lien hypertexte fonctionnel vers la [Déclaration de confidentialité Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839), à proximité de chaque point de l’expérience utilisateur qui offre à l’utilisateur la possibilité d’entrer une requête de recherche. Nommez ce lien hypertexte **Déclaration de confidentialité Microsoft**.
- Affichez de manière visible la marque Bing, en respectant les [Directives relatives à l’usage de la marque Bing](https://go.microsoft.com/fwlink/?linkid=833278), à proximité de chaque point de l’expérience utilisateur qui offre à l’utilisateur la possibilité d’entrer une requête de recherche. Ces éléments de marque doivent clairement indiquer à l’utilisateur que l’expérience de recherche internet est optimisée par Microsoft.
- Vous pouvez attribuer à Microsoft chaque réponse (ou partie d’une réponse) affichée à partir des API Recherche web, Recherche d’images, Recherche d’actualités et Recherche visuelle Bing, sauf mention contraire de Microsoft indiquée par écrit pour votre usage spécifique. Ce point est décrit dans [Directives relatives à l’usage de la marque Bing](https://go.microsoft.com/fwlink/?linkid=833278). 
- N’attribuez pas des réponses (ou des parties de réponses) affichées à partir de l’API Recherche personnalisée Bing à Microsoft, sauf mention contraire de Microsoft indiquée par écrit pour votre usage spécifique.

### <a name="transferring-responses"></a>Transfert des réponses

Si vous permettez à un utilisateur de transférer une réponse d’un API de recherche à un autre utilisateur, par exemple via une application de messagerie ou à une publication sur les réseaux sociaux, les éléments suivants s’appliquent : 
- Les réponses transférées doivent :
  - comprendre du contenu non modifié à partir du contenu des réponses affichées à l’utilisateur effectuant le transfert. Les modifications de mise en forme sont autorisées ;
  - exclure les données sous forme de métadonnées ;
  - pour les réponses provenant des API Recherche web, Recherche d’images, Recherche d’actualités, Recherche vidéo et Recherche visuelle Bing, afficher les mentions indiquant que la réponse a été obtenue via une expérience de recherche internet optimisée par Bing. Par exemple, « Optimisé par Bing » ou « En savoir plus sur cette image sur Bing », ou par le biais de l’utilisation du logo Bing ;
  - pour les réponses provenant de l’API Recherche personnalisée Bing, afficher les mentions indiquant que la réponse a été obtenue via une expérience de recherche internet. Par exemple, « En savoir plus sur ce résultat de recherche » ;
  - afficher de manière bien visible la requête complète utilisée pour générer la réponse ;
  - inclure un lien visible ou une attribution similaire à la source sous-jacente de la réponse, directement ou par l’intermédiaire du moteur de recherche (bing.com, m.bing.com ou votre service de recherche personnalisé).
- Vous n’êtes pas autorisé à automatiser le transfert des réponses. Un transfert doit être déclenché par une action utilisateur attestant clairement une intention de transférer une réponse.
- Vous pouvez uniquement autoriser un utilisateur à transférer les réponses affichées en réponse au transfert de la requête de l’utilisateur.

### <a name="continuity-of-service"></a>Continuité de service 

Vous ne devez pas copier, stocker ou mettre en cache les données des réponses de l’API Recherche. Toutefois, pour permettre la continuité de l’accès au service et le rendu de données, vous pouvez conserver les résultats uniquement dans les conditions suivantes :

**Appareil.** Vous pouvez autoriser un utilisateur à conserver les résultats sur un appareil pour la durée la plus courte entre (i) 24 heures à compter de l’heure de la requête ou (ii) jusqu’à ce qu’un utilisateur envoie une autre requête pour des résultats mis à jour, à condition que les résultats de rétention soient utilisés uniquement dans les cas suivants :

- pour permettre à l’utilisateur d’accéder aux résultats qui lui ont renvoyés précédemment sur cet appareil (par exemple, en cas d’interruption de service) ;
- pour stocker les résultats renvoyés par votre requête proactive personnalisée en prévision des besoins de l’utilisateur en fonction de ses signaux (par exemple, en cas d’interruption de service prévue).

**Serveur.** Vous pouvez toutefois conserver des résultats spécifiques à un seul utilisateur en toute sécurité sur un serveur que vous contrôlez, et afficher les résultats conservés uniquement :

- pour permettre à l’utilisateur d’accéder à un rapport historique des résultats précédemment renvoyés à cet utilisateur dans votre solution. Les résultats ne peuvent pas (i) être conservés pendant plus de 21 jours à compter de la requête initiale de l’utilisateur final et (ii) être affichés en réponse à une requête nouvelle ou répétée d’un utilisateur ;
- pour stocker les résultats renvoyés par votre requête proactive personnalisée en prévision des besoins de l’utilisateur en fonction de ses signaux. Vous pouvez stocker ces résultats pour la durée la plus courte entre (i) 24 heures à compter de l’heure de la requête ou (ii) jusqu’à ce qu’un utilisateur envoie une autre requête pour des résultats mis à jour.

Chaque fois qu’ils sont conservés, les résultats pour un utilisateur spécifique ne peuvent pas être confondus avec ceux d’un autre utilisateur. Autrement dit, les résultats de chaque utilisateur doivent être conservés et remis séparément.

### <a name="general"></a>Généralités 

Pour la présentation de tous les résultats conservés, vous devez :

- inclure une mention claire et visible de l’heure d’envoi de la requête ;
- présenter à l’utilisateur un bouton ou un moyen similaire de relancer la requête et d’obtenir des résultats mis à jour ; 
- conserver la marque Bing dans la présentation des résultats ;
- supprimer (et actualiser avec une nouvelle requête si nécessaire) les résultats stockés dans les délais spécifiés.

### <a name="non-display-url-discovery"></a>Détection d’URL non affichées 

Vous pouvez uniquement utiliser les réponses de recherche dans une expérience de recherche hors Internet, dans le seul but de détecter les URL des sources d’informations réactives à une requête de votre utilisateur ou client. Vous pouvez copier ces URL dans un rapport ou une réponse similaire que vous fournissez :

- uniquement à l’utilisateur ou au client, en réponse à la requête spécifique ;
- uniquement s’il comprend un contenu supplémentaire précieux par rapport à la requête.

La sections précédentes des Conditions d’utilisation et d’affichage des API Recherche ne s’appliquent pas à ce non-affichage, excepté pour les points suivants : 

- Vous ne devez pas mettre en cache, copier ou stocker des données ou du contenu issus ou dérivés de la réponse de recherche, autre que la copie d’URL limitée décrite précédemment.
- Vous devez garantir que l’utilisation des données (y compris les URL) reçues à partir des API de recherche n’enfreint pas la législation applicable ou les droits des tiers.
- Vous ne devez pas utiliser les données (y compris les URL) provenant des API Recherche dans le cadre d’un index de recherche, de Machine Learning ou d’une activité algorithmique similaire. N’utilisez pas ces données pour créer, former, évaluer ou améliorer des services que vous ou des parties tierces pourriez proposer.

## <a name="gdpr-compliance"></a>Conformité RGPD  

En ce qui concerne les informations personnelles relevant du Règlement général sur la protection des données (RGPD) de l’Union européenne et qui sont traitées lors des appels aux API Recherche, à l’API Vérification orthographique Bing ou à l’API Suggestion automatique Bing, vous comprenez que vous et Microsoft êtes des contrôleurs de données indépendants au regard du RGPD. Vous êtes indépendamment responsables de la conformité au RGPD.  

