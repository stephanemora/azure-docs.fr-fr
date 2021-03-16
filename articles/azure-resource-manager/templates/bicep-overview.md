---
title: Langage Bicep pour les modèles Azure Resource Manager
description: Décrit le langage Bicep pour le déploiement d’infrastructure sur Azure via des modèles Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2fb13bca9e9d456889185d512ee2fc9d4cbbe673
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036382"
---
# <a name="what-is-bicep-preview"></a>Qu’est-ce que Bicep (préversion) ?

Bicep est un langage permettant de déployer de manière déclarative des ressources Azure. Il simplifie l’expérience de création en fournissant une syntaxe concise et une meilleure prise en charge de la réutilisation du code. Bicep est un langage spécifique à un domaine (DSL), ce qui signifie qu’il est conçu pour un scénario ou un domaine particulier. Bicep n’est pas conçu comme un langage de programmation général pour l’écriture d’applications.

Par le passé, vous développiez des modèles Azure Resource Manager (modèles ARM) avec JSON. La syntaxe JSON pour la création de modèle peut être détaillée et nécessiter une expression complexe. Bicep améliore cette expérience sans perdre aucune des fonctionnalités d’un modèle JSON. Il s’agit d’une abstraction transparente sur le JSON pour les modèles ARM. Chaque fichier Bicep se compile en un modèle ARM standard. Les types de ressources, les versions d’API et les propriétés qui sont valides dans un modèle ARM sont valides dans un fichier Bicep.

## <a name="get-started"></a>Bien démarrer

Pour commencer à utiliser Bicep, [Installez les outils](https://github.com/Azure/bicep/blob/main/docs/installing.md).

Une fois les outils installés, essayez le [tutoriel Bicep](./bicep-tutorial-create-first-bicep.md). La série de tutoriels vous guide dans la structure et les fonctionnalités de Bicep. Vous déployez des fichiers Bicep et convertissez un modèle ARM en un fichier Bicep équivalent.

Pour afficher les fichiers JSON et Bicep équivalents côte à côte, consultez le [Terrain de jeu de Bicep](https://aka.ms/bicepdemo).

Si vous avez un modèle ARM existant que vous souhaitez convertir en code Bicep, consultez [Décompiler JSON vers Bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="bicep-improvements"></a>Améliorations apportées à Bicep

Bicep offre une syntaxe plus simple et plus concise que JSON. Vous n’utilisez pas d’expressions `[...]`. Au lieu de cela, vous appelez directement des fonctions, et récupérez des valeurs à partir de paramètres et de variables. Vous attribuez à chaque ressource déployée un nom symbolique qui facilite le référencement de cette ressource dans votre modèle.

Par exemple, le code JSON suivant retourne une valeur de sortie à partir d’une propriété de ressource.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

L’expression de sortie équivalente dans Bicep est plus facile à écrire. L’exemple suivant retourne la même propriété en utilisant le nom symbolique **publicIP** pour une ressource définie dans le modèle :

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Pour une comparaison complète de la syntaxe, consultez [Comparaison de JSON et Bicep pour les modèles](compare-template-syntax.md).

Bicep gère automatiquement les dépendances entre ressources. Vous pouvez éviter de définir `dependsOn` quand le nom symbolique d’une ressource est utilisé dans une autre déclaration de ressource.

Avec Bicep, vous pouvez scinder votre projet en plusieurs modules.

La structure du fichier Bicep est plus flexible celle du modèle JSON. Vous pouvez déclarer des paramètres, des variables et des sorties n’importe où dans le fichier. Dans JSON, vous devez déclarer l’ensemble des paramètres, variables et sorties dans les sections correspondantes du modèle.

L’extension VS Code pour Bicep offre une validation plus riche et IntelliSense. Par exemple, l’extension dispose d’IntelliSense pour l’obtention des propriétés d’une ressource.

## <a name="faq"></a>Questions fréquentes (FAQ)

**Pourquoi créer un nouveau langage au lieu d’utiliser un langage existant ?**

Vous pouvez considérer Bicep comme une révision du langage de modèle ARM existant plutôt qu’un nouveau langage. La syntaxe a changé, mais la fonctionnalité de base et le runtime restent les mêmes.

Avant de développer Bicep, nous avons envisagé d’utiliser un langage de programmation existant. Nous avons décidé que notre public cible trouverait plus facile d’apprendre Bicep que de commencer avec un autre langage.

**Pourquoi ne pas concentrer votre énergie sur Terraform ou d’autres infrastructures tierces en tant qu’offres de code ?**

Chaque utilisateur a ses préférences en matière de langages et d’outils. Nous voulons nous assurer que tous ces outils offrent une magnifique expérience sur Azure. Bicep participe de cet effort.

Si vous êtes heureux d’utiliser Terraform, il n’y a aucune raison de changer. Microsoft veille à ce que Terraform sur Azure soit le meilleur possible.

Pour les clients qui ont choisi des modèles ARM, nous pensons que Bicep améliore l’expérience de création. Bicep facilite également la transition pour les clients qui n’ont pas adopté d’infrastructure en tant que code.

**Bicep est-il uniquement destiné à Azure ?**

Bicep est un langage DSL axé sur le déploiement de solutions complètes sur Azure. La poursuite de cet objectif nécessite d’utiliser des API qui se trouvent en dehors d’Azure. Nous prévoyons de fournir des points d’extensibilité pour ces scénarios.

**Qu’advient-il de mes modèles ARM existants ?**

Ils continueront de fonctionner comme ils l’ont toujours fait. Vous n’avez pas besoin d’apporter de modifications. Nous continuerons à prendre en charge le langage JSON du modèle ARM sous-jacent. Les fichiers Bicep se compilent en un JSON qui est envoyé à Azure pour le déploiement.

Lorsque vous êtes prêt, vous pouvez [convertir les fichiers JSON en langage Bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="next-steps"></a>Étapes suivantes

Commencer en suivant le [Tutoriel Bicep](./bicep-tutorial-create-first-bicep.md).
