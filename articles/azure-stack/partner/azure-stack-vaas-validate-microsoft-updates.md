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
ms.date: 1/14/2019
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: bcb56789b2781cd1d081af8c112222a1f1269a74
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304888"
---
# <a name="validate-software-updates-from-microsoft"></a>Valider les mises à jour logicielles issues de Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft publie régulièrement des mises à jour du logiciel Azure Stack. Ces mises à jour sont fournies à nos ingénieurs partenaires Azure Stack. Les mises à jour sont fournies avant d’être mises à la disposition du grand public. Vous pouvez vérifier les mises à jour par rapport à votre solution et fournir des commentaires à Microsoft.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>Appliquer la mise à jour mensuelle

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>Créer un workflow

Les validations de mises à jour utilisent le même workflow que la **validation de la solution**.

## <a name="run-tests"></a>Exécuter les tests

1. Les validations de mises à jour utilisent le même workflow que la **validation de la solution**. 

2. Suivez les instructions données dans [Exécuter les tests de validation de la solution](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Sélectionnez plutôt les tests suivants :
    - Vérification de la mise à jour mensuelle Azure Stack
    - Moteur de simulation cloud

Vous n’avez pas besoin de demander la signature du package pour les validations de mises à jour.

## <a name="next-steps"></a>Étapes suivantes

- [Surveiller et gérer les tests dans le portail VaaS](azure-stack-vaas-monitor-test.md)