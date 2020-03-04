---
title: Comprendre le stockage des paires clé-valeur dans Azure App Configuration
description: Découvrez la façon dont les données de configuration sont stockées dans Azure App Configuration.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 0b83a35d912c97ae25bc2d69d076e8eae8ca490f
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523602"
---
# <a name="keys-and-values"></a>Clés et valeurs

Azure App Configuration stocke les données de configuration sous la forme de paires clé-valeur. Les paires clé-valeur sont une représentation simple et flexible des paramètres d’application utilisés par les développeurs.

## <a name="keys"></a>Keys

Les clés servent d’identificateurs pour les paires clé-valeur. Elles sont utilisées pour stocker et récupérer des valeurs correspondantes. Il est courant d’organiser les clés dans un espace de noms hiérarchique en utilisant un caractère délimiteur, comme `/` ou `:`. Utilisez la convention qui convient le mieux à votre application. App Configuration traite les clés dans leur ensemble. Il n’analyse pas les clés pour déterminer la structure de leur nom ou pour appliquer une règle quelconque.

Voici deux exemples de noms de clés structurés au sein d’une hiérarchie :

* Selon les services de composants

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Selon les régions de déploiement

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

L’utilisation des données de configuration dans des frameworks d’application peut exiger des schémas de nommage spécifiques pour les valeurs des clés. Par exemple, le framework Spring Cloud de Java définit les ressources `Environment` qui fournissent des paramètres à une application Spring.  Celles-ci sont paramétrables par des variables qui incluent le *nom d’application* et le *profil*. Les clés des données de configuration associées à Spring Cloud commencent généralement par ces deux éléments, séparés par un délimiteur.

Les clés stockées dans App Configuration sont des chaînes Unicode qui respectent la casse. Les clés *app1* et *App1* sont distinctes dans un magasin App Configuration. Gardez cette précision à l’esprit quand vous utilisez des paramètres de configuration au sein d’une application, car certains frameworks gèrent les clés de configuration sans tenir compte de la casse. Nous vous déconseillons d’utiliser la casse pour différencier les clés.

Vous pouvez utiliser n’importe quel caractère Unicode dans les noms de clés, à l’exception de `*`, `,` et `\`.  Si vous avez besoin d’inclure l’un de ces caractères réservés, vous devez l’échapper à l’aide de `\{Reserved Character}`. 

La taille combinée d'une paire clé-valeur est limitée à 10 Ko. Sont inclus tous les caractères de la clé, sa valeur et tous les attributs facultatifs associés. À la hauteur de cette limite, vous pouvez avoir de nombreux niveaux hiérarchiques pour les clés.

### <a name="design-key-namespaces"></a>Concevoir des espaces de noms de clés

Il existe deux approches générales pour nommer des clés utilisées pour des données de configuration : la méthode plate ou la méthode hiérarchique. Ces méthodes sont similaires du point de vue de l’utilisation de l’application, mais le nommage hiérarchique offre plusieurs avantages :

* Plus facile à lire. Les délimiteurs inclus dans un nom de clé hiérarchique fonctionnent comme des espaces dans une phrase. Ils fournissent également des séparations naturelles entre les mots.
* Plus facile à gérer. Une hiérarchie de noms de clé représente des groupes logiques de données de configuration.
* Plus facile à utiliser. Il est plus simple d’écrire une requête qui fait correspondre des clés à un modèle dans une structure hiérarchique et de récupérer uniquement une partie des données de configuration. De plus, de nombreux frameworks de programmation récents prennent en charge nativement les données de configuration hiérarchiques de telle sorte que votre application peut utiliser des jeux spécifiques de configuration.

Vous pouvez organiser hiérarchiquement les clés dans App Configuration de nombreuses façons. Considérez ces clés comme des [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Chaque clé hiérarchique est un *chemin* de ressource constitué d’un ou de plusieurs composants qui sont joints par des délimiteurs. Choisissez le caractère à utiliser comme délimiteur en fonction des besoins de votre application, de votre langage de programmation ou de votre framework. Utilisez plusieurs délimiteurs pour différentes clés dans App Configuration.

### <a name="label-keys"></a>Clés d’étiquette

Les valeurs de clé peuvent accessoirement porter un attribut d’étiquette dans App Configuration. Les étiquettes sont utilisées pour différencier des valeurs de clé pour une même clé. Une clé *app1* avec des étiquettes *A* et *B* forme deux clés distinctes dans un magasin App Configuration. Par défaut, une valeur de clé n'a pas d'étiquette. Pour référencer explicitement une valeur de clé sans étiquette, utilisez `\0` (URL encodée sous la forme `%00`).

Label offre un moyen pratique de créer des variantes d’une clé. Les étiquettes sont souvent utilisées pour spécifier plusieurs environnements pour la même clé :

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Gestion des versions des valeurs de clé

App Configuration ne gère pas automatiquement les versions des valeurs de clé. Utilisez les étiquettes comme un moyen de créer plusieurs versions d’une valeur de clé. Par exemple, vous pouvez entrer un numéro de version d’application ou un ID de validation Git dans des étiquettes pour identifier les valeurs de clé associées à une build logicielle particulière.

Vous pouvez utiliser n’importe quel caractère Unicode dans les étiquettes, à l’exception de `*`, `,` et `\`. Ces caractères sont réservés. Pour inclure un caractère réservé, vous devez l’échapper au moyen de `\{Reserved Character}`.

### <a name="query-key-values"></a>Interroger des valeurs de clé

Chaque valeur de clé est identifiée de manière unique par sa clé et une étiquette pouvant être `null`. Pour interroger un magasin App Configuration afin de connaître des valeurs de clé, vous devez spécifier un modèle. Le magasin App Configuration retourne toutes les valeurs de clé qui correspondent au modèle, ainsi que leurs valeurs et attributs correspondants. Utilisez les modèles de clé suivants dans les appels d’API REST à App Configuration :

| Clé | |
|---|---|
| `key` est omis ou `key=*` | Correspond à toutes les clés |
| `key=abc` | Correspond exactement au nom de clé **abc** |
| `key=abc*` | Correspond aux noms de clé qui commencent par **abc** |
| `key=abc,xyz` | Correspond aux noms de clé **abc** ou **xyz**. Limité à cinq valeurs séparées par des virgules |

Vous pouvez également inclure les modèles d’étiquette suivants :

| Étiquette | |
|---|---|
| `label` est omis ou `label=*` | Correspond à toute étiquette, y compris `null` |
| `label=%00` | Correspond à l’étiquette `null` |
| `label=1.0.0` | Correspond exactement à l’étiquette **1.0.0** |
| `label=1.0.*` | Correspond aux étiquettes qui commencent par **1.0.** |
| `label=%00,1.0.0` | Correspond aux étiquettes `null` ou **1.0.0**, limitées à cinq valeurs séparées par des virgules (CSV) |

## <a name="values"></a>Valeurs

Les valeurs attribuées aux clés sont également des chaînes Unicode. Vous pouvez utiliser tous les caractères Unicode pour les valeurs. Il existe un type de contenu facultatif et défini par l’utilisateur, associé à chaque valeur. Utilisez cet attribut pour stocker des informations sur une valeur afin d’aider votre application à la traiter correctement.

Les données de configuration stockées dans un magasin App Configuration sont chiffrées au repos et en transit. Les clés ne sont pas chiffrées au repos. App Configuration n’est pas une solution de remplacement à Azure Key Vault. N’y stockez pas de secrets d’application.

## <a name="next-steps"></a>Étapes suivantes

* [Capture instantanée à un point dans le temps](./concept-point-time-snapshot.md)  
* [Gestion des fonctionnalités](./concept-feature-management.md)  
