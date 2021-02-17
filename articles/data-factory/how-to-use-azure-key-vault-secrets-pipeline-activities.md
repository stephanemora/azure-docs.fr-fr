---
title: Utiliser des secrets Azure Key Vault dans les activités de pipeline
description: Découvrez comment récupérer des informations d’identification stockées à partir d’Azure Key Vault, et comment les utiliser lors de l’exécution de pipelines Data Factory.
author: ChrisLound
ms.author: chlound
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: d7e1133b8f3880c4c1616ef5ca955ed014348935
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383957"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Utiliser des secrets Azure Key Vault dans les activités de pipeline

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Vous pouvez stocker des informations d’identification ou des valeurs secrètes dans Azure Key Vault et les utiliser pendant l’exécution du pipeline pour les transmettre à vos activités.

## <a name="prerequisites"></a>Prérequis

Cette fonctionnalité repose sur l’identité managée de la fabrique de données.  Découvrez comment cela fonctionne dans la section [Identité managée pour Data Factory](./data-factory-service-identity.md) et vérifiez que votre fabrique de données est bien associée à une identité managée.

## <a name="steps"></a>Étapes

1. Ouvrez les propriétés de votre fabrique de données et copiez la valeur ID de l’application d’identité managée.

    ![Valeur de l’identité managée](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Ouvrez les stratégies d’accès Key Vault et ajoutez les autorisations d’identité managée pour récupérer et répertorier les secrets.

    ![Capture d'écran représentant la page « Stratégies d'accès », sur laquelle l'action « Ajouter une stratégie d'accès » est en surbrillance.](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

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
    |Authentification     |MSI         |
    |Ressource        |https://vault.azure.net       |

    ![Activité web](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Vous devez ajouter la chaîne **?api-version=7.0** à la fin de votre URI de secret.  

    > [!CAUTION]
    > Définissez l’option de sortie sécurisée sur « True » pour empêcher la journalisation de la valeur secrète en texte brut.  Pour toutes les autres activités qui consomment cette valeur, l’option d’entrée sécurisée doit être définie sur « True ».

5. Pour utiliser la valeur dans une autre activité, utilisez l’expression de code suivante : **@activity('Web1').output.value**.

    ![Expression de code](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment utiliser Azure Key Vault pour stocker les informations d’identification des magasins de données et des calculs, voir [Informations d’identification dans Azure Key Vault](./store-credentials-in-key-vault.md).