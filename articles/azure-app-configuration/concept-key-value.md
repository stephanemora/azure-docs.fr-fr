---
title: Stockage de paires clé-valeur dans Azure App Configuration | Microsoft Docs
description: Vue d’ensemble de la manière dont les données de configuration sont stockées dans Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: f04067358b0b2bae745727a5dd7a1f5554f9f70e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884979"
---
# <a name="key-value-store"></a>stockage clé-valeur

Azure App Configuration stocke les données de configuration sous forme de paires clé-valeur, ce qui constitue un moyen simple mais souple de représenter divers types de paramètres d’application que les développeurs connaissent bien.

## <a name="keys"></a>Clés

Les clés servent de nom pour les paires clé-valeur et elles sont utilisées pour stocker et récupérer des valeurs correspondantes. Il est courant d’organiser les clés dans un espace de noms hiérarchique en utilisant un caractère délimiteur (comme `/` ou `:`) selon la convention qui convient le mieux à votre application. App Configuration traite les clés dans leur ensemble. Elles ne sont pas analysées pour déterminer la structure de leurs noms ni pour appliquer une règle quelconque.

L’utilisation du magasin de configuration dans les frameworks d’application peut exiger des schémas de nommage spécifiques pour les paires clé-valeur. Par exemple, le framework Spring Cloud de Java définit des ressources `Environment` qui fournissent des paramètres à une application Spring pour qu’elle soit paramétrée par des variables, notamment le *nom de l’application* et le *profil*. Les clés des données de configuration Spring Cloud commencent généralement par ces deux éléments, séparés par un délimiteur.

Les clés stockées dans App Configuration sont des chaînes Unicode qui respectent la casse. Les clés *app1* et *App1* sont des clés distinctes dans un magasin de configuration d’application. Gardez-le à l’esprit quand vous utilisez des paramètres de configuration au sein d’une application, étant donné que certains frameworks gèrent les clés de configuration sans tenir compte de la casse. Par exemple, le système de configuration ASP.NET Core traite les clés comme des chaînes qui ne respectent pas la casse. Pour éviter tout comportement inattendu quand vous interrogez App Configuration au sein d’une application ASP.NET Core, n’utilisez pas de clés qui diffèrent uniquement par leur casse.

Vous êtes autorisé à utiliser tous les caractères Unicode dans les noms de clés entrés dans App Configuration, à l’exception de `*`, `,` et `\` qui sont réservés. Si vous avez besoin d’inclure un caractère réservé, vous devez l’échapper à l’aide de `\{Reserved Character}`. La taille combinée d’une paire clé-valeur est limitée à 10 000 caractères. Sont inclus tous les caractères de la clé, sa valeur et tous les attributs facultatifs associés. À la hauteur de cette limite, vous pouvez avoir de nombreux niveaux hiérarchiques pour les clés.

### <a name="designing-key-namespaces"></a>Conception des espaces de noms de clés

Il existe deux approches générales pour nommer des clés utilisées pour des données de configuration : la méthode plate ou la méthode hiérarchique. Ces méthodes sont très similaires du point de vue de l’utilisation de l’application, mais la dernière offre plusieurs avantages :

* Plus facile à lire. Au lieu d’une seule longue séquence de caractères, les délimiteurs inclus dans une clé hiérarchique nomment les fonctions comme des espaces dans une phrase et fournissent des séparations naturelles entre les mots.
* Plus facile à gérer. Une hiérarchie de noms de clé représente des groupes logiques de données de configuration.
* Plus facile à utiliser. Il est plus simple d’écrire une requête qui fait correspondre des clés à un modèle dans une structure hiérarchique et de récupérer uniquement une partie des données de configuration. De plus, de nombreux frameworks de programmation récents prennent en charge nativement les données de configuration hiérarchiques de sorte que votre application puissent utiliser des jeux spécifiques de configuration.

Vous pouvez organiser hiérarchiquement les clés dans App Configuration de nombreuses façons. Vous pouvez considérer ces clés comme des [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Chaque clé hiérarchique est un *chemin* de ressource constitué d’un ou plusieurs composants, joints par des délimiteurs. Vous pouvez choisir le caractère à utiliser comme délimiteur en fonction des besoins de votre application, langage de programmation ou framework. Vous pouvez utiliser plusieurs délimiteurs pour différentes clés dans App Configuration.

Voici plusieurs exemples de la façon dont vous pouvez structurer vos noms de clé dans une hiérarchie :

* Selon les environnements

        AppName:Test:DB:Endpoint
        AppName:Staging:DB:Endpoint
        AppName:Production:DB:Endpoint

* Selon les services de composants

        AppName:Service1:Test:DB:Endpoint
        AppName:Service1:Staging:DB:Endpoint
        AppName:Service1:Production:DB:Endpoint
        AppName:Service2:Test:DB:Endpoint
        AppName:Service2:Staging:DB:Endpoint
        AppName:Service2:Production:DB:Endpoint

* Selon les régions de déploiement

        AppName:Production:Region1:DB:Endpoint
        AppName:Production:Region2:DB:Endpoint

### <a name="versioning-key-values"></a>Gestion des versions de paires clé-valeur

Les paires clé-valeur peuvent éventuellement porter un attribut d’**étiquette** dans App Configuration. Les étiquettes sont utilisées pour différencier les paires clé-valeur qui ont la même clé. Une clé *app1* avec des étiquettes *v1* et *v2* forment deux paires clé-valeur distinctes dans un magasin de configuration d’application. Par défaut, l’étiquette d’une paire clé-valeur est vide (ou `null`).

App Configuration ne gère pas les versions des paires clé-valeur automatiquement quand vous les modifiez. Vous pouvez utiliser des étiquettes comme moyen de créer plusieurs versions d’une paire clé-valeur. Par exemple, vous pouvez entrer un numéro de version d’application ou un ID de validation Git dans des étiquettes pour identifier les paires clé-valeur associées à une build logicielle particulière.

Vous êtes autorisé à utiliser tous les caractères Unicode dans les étiquettes, à l’exception de `*`, `,` et `\`, qui sont réservés. Si vous avez besoin d’inclure un caractère réservé, vous devez l’échapper à l’aide de `\{Reserved Character}`.

### <a name="querying-key-values"></a>Interrogation des paires clé-valeur

Chaque paire clé-valeur est identifiée de manière unique par sa clé et une étiquette pouvant être `null`. Pour interroger un magasin de configuration d’application sur des paires clé-valeur, vous spécifiez un modèle. Le magasin de configuration d’application retourne toutes les paires clé-valeur qui correspondent au modèle, ainsi que leurs valeurs et attributs correspondants. Vous pouvez utiliser les modèles de clé suivants dans les appels d’API REST à App Configuration :

| Clé | |
|---|---|
| `key` est omis ou `key=*` | Correspond à toutes les clés |
| `key=abc` | Correspond exactement au nom de clé **abc** |
| `key=abc*` | Correspond aux noms de clé qui commencent par **abc** |
| `key=*abc` | Correspond aux noms de clé qui finissent par **abc** |
| `key=*abc*` | Correspond aux noms de clé qui contiennent **abc** |
| `key=abc,xyz` | Correspond aux noms de clé **abc** ou **xyz**, limités à 5 valeurs séparées par des virgules (CSV) |

Vous pouvez également inclure les modèles d’étiquette suivants :

| Étiquette | |
|---|---|
| `label` est omis ou `label=*` | Correspond à toute étiquette, y compris `null` |
| `label=%00` | Correspond à l’étiquette `null` |
| `label=1.0.0` | Correspond exactement à l’étiquette **1.0.0** |
| `label=1.0.*` | Correspond aux étiquettes qui commencent par **1.0.** |
| `label=*.0.0` | Correspond aux étiquettes qui finissent par **.0.0** |
| `label=*.0.*` | Correspond aux étiquettes qui contiennent **.0.** |
| `label=%00,1.0.0` | Correspond aux étiquettes `null` ou **1.0.1**, limitées à 5 valeurs séparées par des virgules (CSV) |

## <a name="values"></a>Valeurs

Les valeurs attribuées aux clés sont également des chaînes Unicode. Vous pouvez utiliser tous les caractères Unicode pour les valeurs. Il existe un **type de contenu** facultatif et défini par l’utilisateur, associé à chaque valeur. Vous pouvez utiliser cet attribut pour stocker des informations (par exemple, un schéma d’encodage) sur une valeur afin d’aider votre application à la traiter correctement.

Les données de configuration stockées dans un magasin de configuration d’application, notamment toutes les clés et valeurs, sont chiffrées au repos et en transit. Néanmoins, App Configuration n’est pas une solution qui remplace Azure Key Vault. Vous ne devez pas y stocker des secrets d’application.

## <a name="next-steps"></a>Étapes suivantes

* [Concept : Capture instantanée à un point dans le temps](concept-point-time-snapshot.md)  
