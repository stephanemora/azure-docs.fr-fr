---
title: Valider une nouvelle solution Azure Stack | Microsoft Docs
description: Découvrez comment valider une nouvelle solution Azure Stack avec le service Validation en tant que service.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 7949e764baa7a4e20eb988c78817b6b4f0045593
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333766"
---
# <a name="validate-a-new-azure-stack-solution"></a>Valider une nouvelle solution Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Apprenez à utiliser le workflow de **validation de solution** pour certifier de nouvelles solutions Azure Stack.

Une solution Azure Stack est une nomenclature matérielle qui a été convenue conjointement entre Microsoft et le partenaire après avoir satisfait aux exigences de certification du logo Windows Server. Une solution doit être renouvelée en cas d’une modification de la nomenclature matérielle. Si vous avez d’autres questions concernant le renouvellement de la certification de solutions, contactez l’équipe à l’adresse [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com).

Pour certifier votre solution, exécutez deux fois le workflow de validation de solution. Exécutez-le une première fois pour la configuration *minimale* prise en charge. Exécutez-le une seconde fois pour la configuration *maximale* prise en charge. Microsoft certifie la solution si les deux configurations réussissent tous les tests.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Créer un workflow de validation de solution

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]
2. Sélectionnez **Démarrer** sur la vignette **Validations de solution**.

    ![Vignette du workflow Validations de solution](media/tile_validation-solution.png)

3. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
4. Sélectionnez la **Configuration de la solution**.
    - **Minimale** : la solution est configurée avec le nombre minimal de nœuds pris en charge.
    - **Maximale** : la solution est configurée avec le nombre maximal de nœuds pris en charge.
5. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Informations relatives à la validation de solution](media/workflow_validation-solution_info.png)

6. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Après la création d’un workflow, les paramètres d’environnement ne peuvent plus être modifiés.

7. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
8. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Vous allez être redirigé vers la page de résumé de tests.

## <a name="execute-solution-validation-tests"></a>Exécuter des tests de validation de solution

Sur la page **Résumé des tests de validation de solution**, vous trouverez une liste de tests requis pour terminer la validation.

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

![Planifier un test de validation de solution](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Étapes suivantes

- [Surveiller et gérer les tests dans le portail VaaS](azure-stack-vaas-monitor-test.md)