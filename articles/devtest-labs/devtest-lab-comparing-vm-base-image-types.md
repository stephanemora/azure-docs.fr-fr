---
title: Comparaison entre les images personnalisées et les formules dans DevTest Labs | Microsoft Docs
description: Découvrez les différences entre les images personnalisées et les formules comme bases de machine virtuelle afin de déterminer laquelle correspond le mieux à votre environnement.
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: f1fdb46decda2f72e64eff95cf57dba002168943
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634002"
---
# <a name="compare-custom-images-and-formulas-in-devtest-labs"></a>Comparer les images personnalisées et les formules dans DevTest Labs
Les [images personnalisées](devtest-lab-create-template.md) et les [formules](devtest-lab-manage-formulas.md) peuvent toutes deux être utilisées comme bases pour [créer des machines virtuelles](devtest-lab-add-vm.md).  La différence principale entre les images personnalisées et les formules est qu’une image personnalisée est simplement une image basée sur un disque dur virtuel, alors qu’une formule correspond à une image basée sur un disque dur virtuel *en plus* de paramètres préconfigurés, comme la taille de la machine virtuelle, le réseau virtuel, le sous-réseau et les artefacts. Ces paramètres préconfigurés sont définis avec les valeurs par défaut. Celles-ci peuvent être remplacées au moment de la création de la machine virtuelle. 

Dans cet article, vous allez découvrir les avantages et les inconvénients de l’utilisation d’images personnalisées par rapport à l’utilisation de formules.  Vous pouvez également lire le [guide pratique de création d’une image personnalisée à partir d’une machine virtuelle](devtest-lab-create-custom-image-from-vm-using-portal.md) et la [FAQ](devtest-lab-faq.yml).

## <a name="custom-image-benefits"></a>Avantages des images personnalisées
Les images personnalisées représentent un moyen statique et immuable de créer des machines virtuelles dans un environnement de votre choix. 

|Avantages|Inconvénients|
|----|----|
|<li>L’approvisionnement de machines virtuelles à partir d’une image personnalisée est rapide car rien ne change une fois que la machine virtuelle est lancée à partir de l’image. En d’autres termes, il n’existe aucun paramètre à appliquer, car l’image personnalisée est simplement une image sans paramètres. <li>Les machines virtuelles créées à partir d’une même image personnalisée sont identiques.|<li>Si vous avez besoin de mettre à jour certains aspects de l’image personnalisée, l’image doit être recréée. |

## <a name="formula-benefits"></a>Avantages des formules
  
Les formules représentent un moyen dynamique de créer des machines virtuelles à partir de la configuration/des paramètres souhaités.

|Avantages|Inconvénients|
|----|----|
|<li>Les modifications de l'environnement peuvent être capturées à la volée au moyen d’artefacts. Par exemple, si vous voulez installer une machine virtuelle avec les derniers éléments de votre pipeline de mise en production ou inscrire les lignes de code les plus récentes de votre dépôt, vous pouvez simplement spécifier un artefact qui déploie les derniers éléments ou inscrit les lignes de code les plus récentes dans la formule avec <li>l’image de base cible. Chaque fois que cette formule sera utilisée pour créer des machines virtuelles, les derniers éléments/lignes de code seront déployés/inscrites sur la machine virtuelle.  <li>Les formules, à la différence des images personnalisées, peuvent définir des paramètres par défaut, par exemple la taille des machines virtuelles et les paramètres de réseau virtuel.  <li>Les paramètres enregistrés dans une formule apparaissent comme valeurs par défaut, mais peuvent être modifiés à la création de la machine virtuelle. |<li> La création d’une machine virtuelle à partir d’une formule peut prendre plus de temps qu’à partir d’une image personnalisée.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
