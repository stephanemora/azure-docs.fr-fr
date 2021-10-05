---
title: Utiliser l’intégration continue
description: Découvrez comment automatiser l'intégration continue dans Azure Data Factory avec les versions de pipelines Azure Pipelines.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2c32b2e986e50fd2679b65b449eccdf5c7629a9a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219559"
---
# <a name="automate-continuous-integration-using-azure-pipelines-releases"></a>Automatiser l’intégration continue à l’aide des versions d’Azure Pipelines

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Vous trouverez ci-après un guide de configuration d’une mise en production Azure Pipelines qui automatise le déploiement d’une fabrique de données dans plusieurs environnements.

## <a name="requirements"></a>Spécifications

-   Un abonnement Azure lié à Visual Studio Team Foundation Server ou Azure Repos qui utilise le [point de terminaison de service Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Une fabrique de données configurée avec l’intégration d’Azure Repos Git.

-   Un [coffre de clés Azure](https://azure.microsoft.com/services/key-vault/) contenant les secrets pour chaque environnement.

## <a name="set-up-an-azure-pipelines-release"></a>Configurer une version d’Azure Pipelines

1.  Dans [Azure DevOps](https://dev.azure.com/), ouvrez le projet configuré avec votre fabrique de données.

1.  Sur le côté gauche de la page, sélectionnez **Pipelines**, puis sélectionnez **Versions**.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image6.png" alt-text="Sélectionner Pipelines, Versions":::

1.  Sélectionnez **Nouveau pipeline** ou, si vous avez des pipelines existants, sélectionnez **Nouveau** puis **Nouveau pipeline de mise en production**.

1.  Sélectionnez le modèle **Tâche vide**.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image13.png" alt-text="Sélectionner Projet vide":::

1.  Dans la zone **Nom de la phase**, entrez le nom de votre environnement.

1.  Sélectionnez **Ajouter un artefact**, puis choisissez le dépôt Git configuré avec votre fabrique de données de développement. Sélectionnez la [branche de publication](source-control.md#configure-publishing-settings) du dépôt comme **branche par défaut**. Par défaut, cette branche de publication est `adf_publish`. Pour **Version par défaut**, sélectionnez **La dernière de la branche par défaut**.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image7.png" alt-text="Ajouter un artefact":::

1.  Ajoutez une tâche de déploiement Azure Resource Manager :

    a.  Dans la vue des phases, sélectionnez **Afficher les tâches de phase**.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image14.png" alt-text="Vue des phases":::

    b.  Créer une tâche. Recherchez **Déploiement de modèles ARM**, puis sélectionnez **Ajouter**.

    c.  Dans la tâche Déploiement, sélectionnez l’abonnement, le groupe de ressources et l’emplacement de la fabrique de données cible. Fournissez les informations d’identification si nécessaire.

    d.  Dans la liste **Actions**, sélectionnez **Créer ou mettre à jour un groupe de ressources**.

    e.  Sélectionnez le bouton de sélection ( **…** ) en regard de la zone **Modèle**. Recherchez le modèle Azure Resource Manager généré dans votre branche de publication du dépôt Git configuré. Recherchez le fichier `ARMTemplateForFactory.json` dans le dossier &lt;FactoryName&gt; de la branche adf_publish.

    f.  Sélectionnez **…** en regard de la zone **Paramètres du modèle** pour choisir le fichier de paramètres. Recherchez le fichier `ARMTemplateParametersForFactory.json` dans le dossier &gt;FactoryName&lt; de la branche adf_publish.

    g.  Sélectionnez **…** en regard de la zone **Remplacer les paramètres du modèle** et entrez les valeurs de paramètre souhaitées pour la fabrique de données cible. Pour les informations d’identification provenant d’Azure Key Vault, entrez le nom du secret entre guillemets doubles. Par exemple, si le nom du secret est cred1, entrez **"$(cred1)"** pour cette valeur.

    h. Sélectionnez **Incrémentiel** comme **Mode de déploiement**.

    > [!WARNING]
    > En mode de déploiement complet, les ressources présentes dans le groupe de ressources mais pas spécifiées dans le modèle Resource Manager sont **supprimées**. Pour plus d’informations, consultez [Modes de déploiement Azure Resource Manager](../azure-resource-manager/templates/deployment-modes.md).

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image9.png" alt-text="Déploiement en production de Data Factory":::

1.  Enregistrez le pipeline de mise en production.

1. Pour déclencher une mise en production, sélectionnez **Créer une mise en production**. Pour automatiser la création des mises en production, consultez les [déclencheurs de mise en production Azure DevOps](/azure/devops/pipelines/release/triggers).

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image10.png" alt-text="Sélectionner Créer une mise en production":::

> [!IMPORTANT]
> Dans les scénarios d’intégration et de livraison continues, le type de runtime d’intégration (IR) doit être le même dans les différents environnements. Par exemple, si vous avez un runtime d’intégration auto-hébergé dans l’environnement de développement, le même runtime d’intégration doit aussi être de type auto-hébergé dans les autres environnements (test, production). De même, si vous partagez des runtimes d’intégration dans plusieurs phases, vous devez les configurer comme étant liés et auto-hébergés dans tous les environnements (développement, test, production).

## <a name="get-secrets-from-azure-key-vault"></a>Obtenez les secrets à partir du coffre Azure Key Vault

Si vous avez des secrets à transmettre dans un modèle Azure Resource Manager, nous vous recommandons d’utiliser Azure Key Vault avec la version Azure Pipelines.

Il existe deux moyens de gérer les secrets :

- Ajoutez les secrets au fichier de paramètres. Pour plus d’informations, consultez [Use Azure Key Vault to pass secure parameter value during deployment](../azure-resource-manager/templates/key-vault-parameter.md) (Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée lors du déploiement).

    Créez une copie du fichier de paramètres qui sera chargée dans la branche de publication. Définissez les valeurs des paramètres que vous souhaitez obtenir à partir du coffre de clés avec le format suivant :

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Lorsque vous utilisez cette méthode, le secret est automatiquement extrait du coffre de clés.

    Le fichier de paramètres doit également être dans la branche de publication.

- Ajoutez une [tâche Azure Key Vault](/azure/devops/pipelines/tasks/deploy/azure-key-vault) avant la tâche de déploiement d’Azure Resource Manager décrite dans la section précédente :

    1.  Dans l’onglet **Tâches**, créez une nouvelle tâche. Recherchez **Azure Key Vault** et ajoutez-le.

    1.  Dans la tâche Key Vault, sélectionnez l’abonnement dans lequel vous avez créé le coffre de clés. Fournissez les informations d’identification si nécessaire, puis sélectionnez le coffre de clés.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image8.png" alt-text="Ajouter une tâche Key Vault":::

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Accorder des autorisations à l’agent Azure Pipelines

La tâche Azure Key Vault peut échouer avec une erreur d’accès refusé si les autorisations appropriées ne sont pas définies. Téléchargez les journaux de la version, puis recherchez le fichier .ps1 contenant la commande pour accorder des autorisations à l’agent Azure Pipelines. Vous pouvez exécuter directement la commande. Vous pouvez aussi copier l’ID du principal à partir du fichier et ajouter manuellement la stratégie d’accès dans le Portail Azure. `Get` et `List` sont les autorisations minimales requises.

## <a name="updating-active-triggers"></a>Mise à jour des déclencheurs actifs

Installez les modules Azure PowerShell les plus récents en suivant les instructions décrites dans [Comment installer et configurer Azure PowerShell](/powershell/azure/install-Az-ps).

>[!WARNING]
>Si vous n’utilisez pas les dernières versions des modules PowerShell et Data Factory, vous risquez de rencontrer des erreurs de désérialisation lors de l’exécution des commandes. 
>

Le déploiement peut échouer si vous tentez de mettre à jour les déclencheurs actifs. Pour mettre à jour les déclencheurs actifs, vous devez les arrêter manuellement puis les redémarrer après le déploiement. Vous pouvez le faire à l’aide d’une tâche Azure PowerShell :

1.  Dans l’onglet **Tâches** de la version, ajoutez une tâche **Azure Powershell**. Choisissez comme version de la tâche la dernière version d’Azure PowerShell. 

1.  Sélectionnez l’abonnement dans lequel se trouve votre fabrique.

1.  Sélectionnez **Chemin d’accès au fichier de script** comme type de script. Pour cela, vous devez enregistrer votre script PowerShell dans votre référentiel. Le script PowerShell suivant peut être utilisé pour arrêter des déclencheurs :

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Vous pouvez suivre des étapes similaires (avec la fonction `Start-AzDataFactoryV2Trigger`) pour redémarrer les déclencheurs après le déploiement.

L'équipe de la Data Factory a fourni un [exemple de script de pré et post-déploiement](continuous-integration-delivery-sample-script.md). 

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de l’intégration et de la livraison continues](continuous-integration-delivery.md)
- [Promouvoir manuellement un modèle Resource Manager pour chaque environnement](continuous-integration-delivery-manual-promotion.md)
- [Utiliser des paramètres personnalisés avec un modèle Resource Manager](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Modèles Resource Manager liés](continuous-integration-delivery-linked-templates.md)
- [Utilisation d’un environnement de production de correctif logiciel](continuous-integration-delivery-hotfix-environment.md)
- [Exemple de script de pré-déploiement et de post-déploiement](continuous-integration-delivery-sample-script.md)
