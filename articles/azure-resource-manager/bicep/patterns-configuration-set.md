---
title: Modèle de jeu de configuration
description: Décrit le modèle de jeu de configuration.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: b2ccac6f646304d7d530e616a57c8490e17aba22
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122564097"
---
# <a name="configuration-set-pattern"></a>Modèle de jeu de configuration

Au lieu de définir un grand nombre de paramètres individuels, créez des jeux de valeurs prédéfinis. Pendant le déploiement, sélectionnez le jeu de valeurs à utiliser.

## <a name="context-and-problem"></a>Contexte et problème

Un seul fichier Bicep définit souvent de nombreuses ressources. Chaque ressource devra peut-être utiliser une configuration différente en fonction de l’environnement dans lequel vous la déployez. Par exemple, vous pouvez créer un fichier Bicep qui déploie un plan et une application App Service ainsi qu’un compte de stockage. Chacune de ces ressources a plusieurs options qui affectent son coût, sa disponibilité et sa résilience. Pour les environnements de production, il est préférable d’utiliser un jeu de configuration qui privilégie la haute disponibilité et la résilience. Pour les environnements hors production, il est préférable d’utiliser un autre jeu de configuration qui privilégie la réduction des coûts.

Vous pouvez créer des paramètres pour définir chaque configuration, mais cela présente certains inconvénients :

- Il s’agit d’une approche assez lourde pour les utilisateurs du modèle, qui doivent comprendre les valeurs à utiliser pour chaque ressource et l’impact de la définition de chaque paramètre.
- Le nombre de paramètres de votre modèle augmente avec chaque nouvelle ressource que vous définissez.
- Les utilisateurs peuvent choisir des combinaisons de valeurs de paramètres qui n’ont pas été testées ou qui ne fonctionneront pas correctement.

## <a name="solution"></a>Solution

Créez un paramètre unique pour spécifier le type d’environnement. Utilisez une variable afin de sélectionner automatiquement la configuration pour chaque ressource en fonction de la valeur du paramètre.

> [!NOTE]
> On parle parfois de l’approche de type _taille de T-shirt_. Quand vous achetez un T-shirt, vous disposez d’un choix limité en termes de longueur, de largeur, de forme de manches, etc. Vous avez simplement le choix entre les tailles petite, moyenne et grande, et le designer du T-shirt a utilisé des mesures prédéfinies en fonction de ces tailles.

## <a name="example"></a>Exemple

Supposons que vous disposez d’un modèle qui peut être déployé sur deux types d’environnement : production et hors production. La configuration dont vous avez besoin diffère selon le type d’environnement :

| Propriété | Environnements hors production | Les environnements de production |
|-|-|-|
| **Plan App Service** |
| Nom de la référence (SKU) | S2 | P2V3 |
| Capacité (nombre d’instances) | 1 | 3 |
| **application App Service** |
| Always On | Désactivé | activé |
| **Compte de stockage** |
| Nom de la référence (SKU) | Standard_LRS | Standard_ZRS |

Vous pouvez utiliser le modèle de jeu de configuration pour ce modèle.

Acceptez un seul paramètre qui indique le type d’environnement : production ou hors production. Utilisez l’élément décoratif de paramètre `@allowedValues` pour être certain que les utilisateurs de votre modèle fournissent uniquement les valeurs que vous attendez :

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="5-9" :::

Créez ensuite une _variable de mappage_, c’est-à-dire un objet qui définit la configuration spécifique en fonction du type d’environnement. Notez que la variable a deux objets nommés `Production` et `NonProduction`. Ces noms correspondent aux valeurs autorisées pour le paramètre dans l’exemple précédent :

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="16-49" :::

Quand vous définissez les ressources, utilisez la table de configuration pour définir les propriétés de la ressource :

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="51-74" highlight="4, 14, 23" :::

## <a name="considerations"></a>Considérations

- Dans votre variable de mappage, envisagez de regrouper les propriétés par ressource pour simplifier leur définition.
- Dans votre variable de mappage, vous pouvez définir des valeurs de propriété individuelles (comme la propriété `alwaysOn` dans l’exemple) ou des variables objet qui définissent une propriété d’objet (comme les propriétés de SKU dans l’exemple).
- Envisagez d’utiliser un jeu de configuration avec des [conditions de ressource](conditional-resource-deployment.md). Votre code Bicep pourra ainsi déployer certaines ressources dans des environnements spécifiques uniquement.

## <a name="next-steps"></a>Étapes suivantes

[Découvrez le modèle de fichier de variables partagées.](patterns-shared-variable-file.md)
