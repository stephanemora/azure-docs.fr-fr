---
title: Stockage de paires clé-valeur dans Azure App Configuration | Microsoft Docs
description: Vue d’ensemble de la façon dont les données de configuration sont stockées dans Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 054de294c9edb0fe5b75da4ac7fd54ce987123de
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185219"
---
# <a name="keys-and-values"></a>Clés et valeurs

Azure App Configuration stocke les données de configuration sous la forme de paires clé-valeur. Les paires clé-valeur constituent un moyen simple mais souple de représenter divers types de paramètres d’application que les développeurs connaissent bien.

## <a name="keys"></a>Clés

Les clés servent de nom pour les paires clé-valeur ; elles sont utilisées pour stocker et récupérer des valeurs correspondantes. Il est courant d’organiser les clés dans un espace de noms hiérarchique en utilisant un caractère délimiteur, comme `/` ou `:`. Utilisez la convention qui convient le mieux à votre application. App Configuration traite les clés dans leur ensemble. Il n’analyse pas les clés pour déterminer la structure de leur nom ou pour appliquer une règle quelconque.

L’utilisation des données de configuration dans des frameworks d’application peut exiger des schémas de nommage spécifiques pour les valeurs des clés. Par exemple, le framework Spring Cloud de Java définit des ressources `Environment` qui fournissent des paramètres à une application Spring pour qu’elle soit paramétrée par des variables qui incluent le *nom de l’application* et le *profil*. Les clés des données de configuration associées à Spring Cloud commencent généralement par ces deux éléments, séparés par un délimiteur.

Les clés stockées dans App Configuration sont des chaînes Unicode qui respectent la casse. Les clés *app1* et *App1* sont distinctes dans un magasin App Configuration. Gardez cette précision à l’esprit quand vous utilisez des paramètres de configuration au sein d’une application, car certains frameworks gèrent les clés de configuration sans tenir compte de la casse. Par exemple, le système de configuration ASP.NET Core traite les clés comme des chaînes qui ne respectent pas la casse. Pour éviter tout comportement inattendu quand vous interrogez App Configuration au sein d’une application ASP.NET Core, n’utilisez pas de clés qui diffèrent uniquement par leur casse.

Vous pouvez utiliser tous les caractères Unicode dans les noms de clés entrés dans App Configuration, à l’exception de `*`, `,` et `\`. Ces caractères sont réservés. Si vous avez besoin d’inclure un caractère réservé, vous devez l’échapper au moyen de `\{Reserved Character}`. La taille combinée d’une paire clé-valeur est limitée à 10 000 caractères. Sont inclus tous les caractères de la clé, sa valeur et tous les attributs facultatifs associés. À la hauteur de cette limite, vous pouvez avoir de nombreux niveaux hiérarchiques pour les clés.

### <a name="design-key-namespaces"></a>Concevoir des espaces de noms de clés

Il existe deux approches générales pour nommer des clés utilisées pour des données de configuration : la méthode plate ou la méthode hiérarchique. Ces méthodes sont similaires du point de vue de l’utilisation de l’application, mais le nommage hiérarchique offre plusieurs avantages :

* Plus facile à lire. Au lieu d’une seule longue séquence de caractères, les délimiteurs inclus dans un nom de clé hiérarchique fonctionnent comme des espaces dans une phrase. Ils fournissent également des séparations naturelles entre les mots.
* Plus facile à gérer. Une hiérarchie de noms de clé représente des groupes logiques de données de configuration.
* Plus facile à utiliser. Il est plus simple d’écrire une requête qui fait correspondre des clés à un modèle dans une structure hiérarchique et de récupérer uniquement une partie des données de configuration. De plus, de nombreux frameworks de programmation récents prennent en charge nativement les données de configuration hiérarchiques de telle sorte que votre application peut utiliser des jeux spécifiques de configuration.

Vous pouvez organiser hiérarchiquement les clés dans App Configuration de nombreuses façons. Considérez ces clés comme des [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Chaque clé hiérarchique est un *chemin* de ressource constitué d’un ou de plusieurs composants qui sont joints par des délimiteurs. Choisissez le caractère à utiliser comme délimiteur en fonction des besoins de votre application, de votre langage de programmation ou de votre framework. Utilisez plusieurs délimiteurs pour différentes clés dans App Configuration.

Voici plusieurs exemples de la façon dont vous pouvez structurer vos noms de clé dans une hiérarchie :

* Selon les services de composants

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Selon les régions de déploiement

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>Clés d’étiquette

Les valeurs de clé peuvent accessoirement porter un attribut d’étiquette dans App Configuration. Les étiquettes sont utilisées pour différencier des valeurs de clé pour une même clé. Une clé *app1* avec des étiquettes *A* et *B* forme deux clés distinctes dans un magasin App Configuration. Par défaut, l’étiquette d’une valeur de clé est vide ou `null`.

Label offre un moyen pratique de créer des variantes d’une clé. Les étiquettes sont souvent utilisées pour spécifier plusieurs environnements pour la même clé :

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Gestion des versions des valeurs de clé

App Configuration ne gère pas automatiquement les versions des valeurs de clé quand vous les modifiez. Utilisez les étiquettes comme un moyen de créer plusieurs versions d’une valeur de clé. Par exemple, vous pouvez entrer un numéro de version d’application ou un ID de validation Git dans des étiquettes pour identifier les valeurs de clé associées à une build logicielle particulière.

Vous pouvez utiliser n’importe quel caractère Unicode dans les étiquettes, à l’exception de `*`, `,` et `\`. Ces caractères sont réservés. Pour inclure un caractère réservé, vous devez l’échapper au moyen de `\{Reserved Character}`.

### <a name="query-key-values"></a>Interroger des valeurs de clé

Chaque valeur de clé est identifiée de manière unique par sa clé et une étiquette pouvant être `null`. Pour interroger un magasin App Configuration afin de connaître des valeurs de clé, vous devez spécifier un modèle. Le magasin App Configuration retourne toutes les valeurs de clé qui correspondent au modèle, ainsi que leurs valeurs et attributs correspondants. Utilisez les modèles de clé suivants dans les appels d’API REST à App Configuration :

| Clé | |
|---|---|
| `key` est omis ou `key=*` | Correspond à toutes les clés |
| `key=abc` | Correspond exactement au nom de clé **abc** |
| `key=abc*` | Correspond aux noms de clé qui commencent par **abc** |
| `key=*abc` | Correspond aux noms de clé qui finissent par **abc** |
| `key=*abc*` | Correspond aux noms de clé qui contiennent **abc** |
| `key=abc,xyz` | Correspond aux noms de clé **abc** ou **xyz**, limités à cinq valeurs séparées par des virgules (CSV) |

Vous pouvez également inclure les modèles d’étiquette suivants :

| Étiquette | |
|---|---|
| `label` est omis ou `label=*` | Correspond à toute étiquette, y compris `null` |
| `label=%00` | Correspond à l’étiquette `null` |
| `label=1.0.0` | Correspond exactement à l’étiquette **1.0.0** |
| `label=1.0.*` | Correspond aux étiquettes qui commencent par **1.0.** |
| `label=*.0.0` | Correspond aux étiquettes qui finissent par **.0.0** |
| `label=*.0.*` | Correspond aux étiquettes qui contiennent **.0.** |
| `label=%00,1.0.0` | Correspond aux étiquettes `null` ou **1.0.0**, limitées à cinq valeurs séparées par des virgules (CSV) |

## <a name="values"></a>Valeurs

Les valeurs attribuées aux clés sont également des chaînes Unicode. Vous pouvez utiliser tous les caractères Unicode pour les valeurs. Il existe un type de contenu facultatif et défini par l’utilisateur, associé à chaque valeur. Utilisez cet attribut pour stocker des informations, par exemple un schéma d’encodage, sur une valeur afin d’aider votre application à la traiter correctement.

Les données de configuration stockées dans un magasin App Configuration, y compris toutes les clés et les valeurs, sont chiffrées au repos et en transit. App Configuration n’est pas une solution de remplacement à Azure Key Vault. N’y stockez pas de secrets d’application.

## <a name="next-steps"></a>Étapes suivantes

* [Capture instantanée à un point dans le temps](./concept-point-time-snapshot.md)  
* [Gestion des fonctionnalités](./concept-feature-management.md)  
