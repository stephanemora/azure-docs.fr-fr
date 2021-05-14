---
title: Langage Bicep pour les modèles Azure Resource Manager
description: Décrit le langage Bicep pour le déploiement d’infrastructure sur Azure via des modèles Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: af207e6ca88eab50fe6030883379c87c0ec05691
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773744"
---
# <a name="what-is-bicep-preview"></a>Qu’est-ce que Bicep (préversion) ?

Bicep est un langage permettant de déployer de manière déclarative des ressources Azure. Vous pouvez utiliser Bicep au lieu de JSON pour développer vos modèles Azure Resource Manager (modèles ARM). Bicep simplifie l’expérience de création en fournissant une syntaxe concise, une meilleure prise en charge de la réutilisation du code et une cohérence des types améliorée. Bicep est un langage spécifique à un domaine (DSL), ce qui signifie qu’il est conçu pour un scénario ou un domaine particulier. Il n’est pas conçu comme un langage de programmation général pour l’écriture d’applications.

La syntaxe JSON pour la création de modèle peut être détaillée et nécessiter une expression complexe. Bicep améliore cette expérience sans perdre aucune des fonctionnalités d’un modèle JSON. Il s’agit d’une abstraction transparente sur le JSON pour les modèles ARM. Chaque fichier Bicep se compile en un modèle ARM standard. Les types de ressources, les versions d’API et les propriétés qui sont valides dans un modèle ARM sont valides dans un fichier Bicep. Il existe quelques [limitations connues](#known-limitations) dans la version actuelle.

Bicep n'est actuellement disponible qu'en préversion. Pour suivre l’état du travail, consultez le [référentiel de projet Bicep](https://github.com/Azure/bicep).

Pour en savoir plus sur Bicep, consultez la vidéo suivante.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Bien démarrer

Pour commencer à utiliser Bicep, [Installez les outils](bicep-install.md).

Une fois les outils installés, essayez le [tutoriel Bicep](./bicep-tutorial-create-first-bicep.md). La série de tutoriels vous guide dans la structure et les fonctionnalités de Bicep. Vous déployez des fichiers Bicep et convertissez un modèle ARM en un fichier Bicep équivalent.

Pour afficher les fichiers JSON et Bicep équivalents côte à côte, consultez le [Terrain de jeu de Bicep](https://aka.ms/bicepdemo).

Si vous disposez d’un modèle ARM existant que vous souhaitez le convertir en Bicep, consultez [Conversion de modèles ARM entre JSON et Bicep](bicep-decompile.md).

## <a name="benefits-of-bicep-versus-other-tools"></a>Avantages de Bicep par rapport à d’autres outils

Bicep offre les avantages suivants par rapport aux autres options :

* **Support pour tous les types de ressources et versions d’API** : Bicep prend immédiatement en charge toutes les versions de préversion et de disponibilité générale (GA) pour les services Azure. Dès qu’un fournisseur de ressources introduit de nouveaux types de ressources et de versions d’API, vous pouvez les utiliser dans votre fichier Bicep. Vous ne devez pas attendre la mise à jour des outils avant d’utiliser les nouveaux services.
* **Expérience de création** : lorsque vous utilisez VS code pour créer vos fichiers Bicep, vous bénéficiez d’une expérience de création de première classe. L’éditeur fournit une validation enrichie de type sécurisé, IntelliSense et de la syntaxe.
* **Modularité** : vous pouvez diviser votre code Bicep en parties gérables à l’aide de [modules](bicep-modules.md). Le module déploie un ensemble de ressources associées. Les modules vous permettent de réutiliser le code et de simplifier le développement. Ajoutez le module à un fichier Bicep chaque fois que vous devez déployer ces ressources.
* **Intégration aux services Azure** : Bicep est intégré aux services Azure, tels qu’Azure Policy, les specs de modèle et les blueprints.
* **Aucun fichier d’état ou état à gérer** : tous les états sont stockés dans Azure. Les utilisateurs peuvent collaborer et être assurés que leurs mises à jour sont traitées comme prévu. Utilisez l’[opération de simulation](template-deploy-what-if.md) pour afficher des modifications avant de déployer votre modèle.
* **Aucuns frais et open source** : Bicep est complètement gratuit. Vous ne devez pas payer les fonctionnalités Premium. Elles sont également prises en charge par le support Microsoft.

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

La structure du fichier Bicep est plus flexible celle du modèle JSON. Vous pouvez déclarer des paramètres, des variables et des sorties n’importe où dans le fichier. Dans JSON, vous devez déclarer l’ensemble des paramètres, variables et sorties dans les sections correspondantes du modèle.

## <a name="known-limitations"></a>Limitations connues

Les limites suivantes existent actuellement :

* Impossible de définir le mode ou la taille de lot sur les boucles de copie.
* Impossible de combiner des boucles et des conditions.
* Les objets et tableaux sur une seule ligne, comme `['a', 'b', 'c']`, ne sont pas pris en charge.

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

Lorsque vous êtes prêt, vous pouvez [convertir les fichiers JSON en langage Bicep](bicep-decompile.md).

## <a name="next-steps"></a>Étapes suivantes

Commencer en suivant le [Tutoriel Bicep](./bicep-tutorial-create-first-bicep.md).
