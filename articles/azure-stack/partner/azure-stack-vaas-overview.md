---
title: Vue d’ensemble de la validation en tant que service pour Azure Stack | Microsoft Docs
description: Vue d’ensemble des problèmes connus liés à la validation en tant que service Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c96e7385356354d398108ad69492603d38667e46
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234519"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Qu’est-ce que la validation en tant que service pour Azure Stack ?

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

La validation en tant que service (VaaS) est un service Azure natif, conçu pour les partenaires de solutions qui conçoivent conjointement les offres Azure Stack avec Microsoft. Les partenaires de solutions peuvent utiliser le service pour vérifier que leurs solutions répondent aux exigences de Microsoft, et qu’elles fonctionnent comme prévu avec Azure Stack.

La principale utilisation de VaaS consiste à :

- Valider de nouvelles solutions Azure Stack
- Valider les modifications apportées au logiciel Azure Stack
- Obtenir les packages des partenaires de solutions signés numériquement qui sont utilisés au cours du déploiement
- Utiliser des outils annexes en préversion dans la validation Azure Stack

## <a name="validate-new-azure-stack-solution"></a>Valider une nouvelle solution Azure Stack

Les partenaires utilisent le workflow de validation de solution pour vérifier les nouvelles solutions Azure Stack. La solution doit réussir les tests des composants Azure Stack du Hardware Lab Kit (HKL) exigés. Vous devez seulement exécuter le workflow à deux reprises pour chaque nouvelle solution : une fois avec la configuration minimale et une autre fois avec la configuration maximale.

Pour plus d’informations, consultez [Valider une nouvelle solution Azure Stack](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Valider les modifications apportées au logiciel Azure Stack

Les partenaires utilisent le workflow de validation de package pour vérifier que leur solution fonctionne avec la mise à jour récente du logiciel Azure Stack. Au minimum, le workflow de validation de package doit être exécuté sur l’environnement matériel recommandé par Microsoft, et sur une solution où les correctifs et mises à jour ont été utilisés pour appliquer la mise à jour. Vous devez exécuter la validation de package sur la build de référence.

Pour plus d’informations, consultez [Valider les mises à jour logicielles issues de Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Obtenir les packages des partenaire de solutions signés numériquement

En plus de la validation des mises à jour Azure Stack, vous pouvez également utiliser le workflow de validation de package pour vérifier les mises à jour des packages de personnalisation OEM, qui incluent les pilotes, les microprogrammes et d’autres logiciels spécifiques au partenaire Azure Stack, et qui sont utilisés pendant le déploiement du logiciel Azure Stack. Déployez le package que vous vérifiez sur la version actuelle du logiciel Azure Stack, avec au moins la solution de taille minimale pouvant être prise en charge. Le package mis à jour doit être chargé sur le service avant de démarrer le test. Si les tests réussissent, notifiez vaashelp@microsoft.com. Informez le partenaire Azure Stack que le package a passé les tests et qu’il va être approuvé numériquement avec la signature numérique Azure Stack. Microsoft signe le package et notifie le partenaire Azure Stack que celui-ci est disponible au téléchargement dans le portail.

Pour plus d’informations, consultez [Valider les packages OEM](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-azure-stack-validation-collateral"></a>Utiliser des outils annexes en préversion dans la validation Azure Stack

Microsoft propose régulièrement de nouvelles fonctionnalités dans Azure Stack. Dans le cadre du processus de développement pour la diffusion de ces fonctionnalités sur le marché, de nouveaux outils de tests annexes sont mis à disposition dans le workflow de passe de test. Ce workflow comprend des outils de test annexes provenant d’autres flux de travail pour permettre l’exécution de tests non officiels. N’utilisez pas le workflow de passe de test pour soumettre les résultats en vue d’une approbation. Pour obtenir une approbation officielle de votre solution, utilisez le workflow de validation de solution et de validation de package.

## <a name="next-steps"></a>Étapes suivantes

- Commencez par [Configurer votre compte de validation en tant que service](azure-stack-vaas-validate-solution-new.md)
