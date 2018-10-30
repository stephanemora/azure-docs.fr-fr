---
title: Valider les mises à jour logicielles de Microsoft dans le service Validation en tant que service pour Azure Stack | Microsoft Docs
description: Découvrez comment valider les mises à jour logicielles de Microsoft avec le service Validation en tant que service.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 7fcc7d5a1d87fe93d32772dbbb84f1d3c91d5631
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648780"
---
# <a name="validate-software-updates-from-microsoft"></a>Valider les mises à jour logicielles issues de Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft publie régulièrement des mises à jour du logiciel Azure Stack. Avant d’être rendues publiques, ces mises à jour sont fournies aux partenaires qui participent à la conception des offres Azure Stack pour permettre à ces derniers de valider les mises à jour pour leurs solutions et de faire part de leurs commentaires à Microsoft.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>Appliquer la mise à jour mensuelle

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>Créer un workflow

Les validations de mise à jour utilisent le même workflow que **Validation du Package**. Suivez les instructions pour [Create a Package Validation workflow](azure-stack-vaas-validate-oem-package.md#create-a-package-validation-workflow) (créer un workflow de Validation de package).

## <a name="run-tests"></a>Exécuter les tests

Les validations de mise à jour utilisent le même workflow que **Validation du Package**. Suivez les instructions pour [Execute Package Validation tests](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests) (exécutez les tests de Validation de package).

Vous n’avez pas besoin de demander la signature du package pour les validations de mise à jour.

## <a name="next-steps"></a>Étapes suivantes

- [Surveiller et gérer les tests dans le portail VaaS](azure-stack-vaas-monitor-test.md)
