---
title: Utiliser des secrets Azure Key Vault dans les activités de pipeline
description: Découvrez comment récupérer des informations d’identification stockées à partir d’Azure Key Vault, et comment les utiliser lors de l’exécution de pipelines Data Factory.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 9ca2ea6a45bdf37f15f2ab4fd9c685f11f6d7f64
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031490"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Utiliser des secrets Azure Key Vault dans les activités de pipeline

Vous pouvez stocker des informations d’identification ou des valeurs secrètes dans Azure Key Vault et les utiliser pendant l’exécution du pipeline pour les transmettre à vos activités.

## <a name="prerequisites"></a>Conditions préalables requises

Cette fonctionnalité repose sur l’identité managée de la fabrique de données.  Découvrez comment cela fonctionne dans la section [Identité managée pour Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) et vérifiez que votre fabrique de données est bien associée à une identité managée.

## <a name="steps"></a>Étapes

1. Ouvrez les propriétés de votre fabrique de données et copiez la valeur ID de l’application d’identité managée.

    ![Valeur de l’identité managée](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Ouvrez les stratégies d’accès Key Vault et ajoutez les autorisations d’identité managée pour récupérer et répertorier les secrets.

    ![Stratégies d’accès Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Stratégies d’accès Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Cliquez sur **Ajouter**, puis sur **Enregistrer**.

3. Accédez à votre secret Key Vault et copiez l’identificateur de secret.

    ![Identificateur de secret](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Prenez note de l’URI de secret que vous souhaitez obtenir lors de l’exécution de votre pipeline Data Factory.

4. Dans votre pipeline Data Factory, ajoutez une nouvelle activité web et configurez-la comme suit.  

    |Propriété  |Valeur  |
    |---------|---------|
    |Sortie sécurisée     |True         |
    |URL     |[Valeur de votre URI de secret]?api-version=7.0         |
    |Méthode     |GET         |
    |Authentication     |MSI         |
    |Ressource        |https://vault.azure.net       |

    ![Activité web](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Vous devez ajouter la chaîne **?api-version=7.0** à la fin de votre URI de secret.  

    > [!CAUTION]
    > Définissez l’option de sortie sécurisée sur « True » pour empêcher la journalisation de la valeur secrète en texte brut.  Pour toutes les autres activités qui consomment cette valeur, l’option d’entrée sécurisée doit être définie sur « True ».

5. Pour utiliser la valeur dans une autre activité, utilisez l’expression de code suivante : **@activity("web").output.value**.

    ![Expression de code](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment utiliser Azure Key Vault pour stocker les informations d’identification des magasins de données et des calculs, voir [Informations d’identification dans Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault).
