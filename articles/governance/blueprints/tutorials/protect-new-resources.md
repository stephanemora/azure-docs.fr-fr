---
title: Protéger les nouvelles ressources avec des verrous de ressource de blueprint
description: Apprenez à utiliser les verrous de ressource Azure Blueprints « Lecture seule » et « Ne pas supprimer » afin de protéger les ressources nouvellement déployées.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d315fb5fe3ce7844946e6a9405a9a5f6a0be8b9d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791607"
---
# <a name="protect-new-resources-with-azure-blueprints-resource-locks"></a>Protéger les nouvelles ressources avec des verrous de ressource Azure Blueprints

Les [verrous de ressource](../concepts/resource-locking.md) Azure Blueprints permettent de protéger les ressources nouvellement déployées contre toute manipulation, même par un compte disposant du rôle _Propriétaire_. Cette protection peut être ajoutée aux ressources créées par un artefact de modèle Resource Manager dans la définition de blueprint.

Cet article contient les étapes suivantes :

> [!div class="checklist"]
> - Créer une définition de blueprint
> - Affecter l’état **Publié** à votre définition de blueprint
> - Affecter votre définition de blueprint à un abonnement existant
> - Inspecter le nouveau groupe de ressources
> - Annuler l’assignation du blueprint afin de retirer les verrous

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, il est nécessaire de disposer d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-new-blueprint-definition"></a>Créer une définition de blueprint

Tout d’abord, créez la nouvelle définition de blueprint.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Dans la page **Démarrage**à gauche, sélectionnez le bouton **Créer** sous _Créer un blueprint_.

1. Trouvez l’exemple de blueprint **Blueprint vide** en haut de la page et sélectionnez **Commencer par un blueprint vide**.

1. Entrez les _Fonctions de base_ de l’exemple de blueprint :

   - **Nom du blueprint** : Entrez le nom de votre copie de l’exemple de blueprint. Pour ce didacticiel, nous utiliserons le nom _locked-storageaccount_.
   - **Description du blueprint** : Décrit la définition de blueprint. Utilisez « For testing blueprint resource locking on deployed resources. » (« Pour tester le verrouillage de ressources de blueprint sur les ressources déployées. »).
   - **Emplacement de la définition** : Utilisez le bouton points de suspension et sélectionnez le groupe d’abonnement ou l’abonnement dans lequel enregistrer votre définition de blueprint.

1. Sélectionnez l’onglet _Artefacts_ dans le haut de la page ou **Suivant : Artefacts** dans le bas de la page.

1. Ajoutez un groupe de ressources à l’abonnement : Sélectionnez la ligne **+ Ajouter un artefact...** sous **Abonnement**.
   Sélectionnez « Groupe de ressources » comme _Type d’artefact_. Définissez le _Nom complet de l'artefact_ sur **RGtoLock**.
   Laissez les champs _Nom du groupe de ressources_ et _Emplacement_ vides, mais vérifiez que la case de chaque propriété est cochée pour créer des **paramètres dynamiques**. Cliquez sur **Ajouter** pour ajouter cet artefact au blueprint.

1. Ajoutez le modèle sous le groupe de ressources : Sélectionnez la ligne **+ Ajouter un artefact...** sous l’entrée **RGtoLock**. Sélectionnez « Modèle Azure Resource Manager » comme _type d’artefact_, définissez _Nom complet de l’artefact_ avec la valeur « StorageAccount », et laissez le champ _Description_ vide. Sous l’onglet **Modèle** dans la zone de l’éditeur, collez le modèle Resource Manager suivant. Après avoir collé le modèle, sélectionnez **Ajouter** pour ajouter cet artefact au blueprint.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "storageAccountType": {
               "type": "string",
               "defaultValue": "Standard_LRS",
               "allowedValues": [
                   "Standard_LRS",
                   "Standard_GRS",
                   "Standard_ZRS",
                   "Premium_LRS"
               ],
               "metadata": {
                   "description": "Storage Account type"
               }
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
           "apiVersion": "2018-07-01",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "StorageV2",
           "properties": {}
       }],
       "outputs": {
           "storageAccountName": {
               "type": "string",
               "value": "[variables('storageAccountName')]"
           }
       }
   }
   ```

1. Cliquez sur **Enregistrer le brouillon** en bas de la page.

Cette étape crée la définition de blueprint dans le groupe d’administration ou l’abonnement sélectionné.

Dès que la notification **Définition de blueprint enregistrée** apparaît dans le portail, passez à l’étape suivante.

## <a name="publish-the-blueprint-definition"></a>Publier la définition de blueprint

Votre définition de blueprint est à présent créée dans votre environnement. Elle est créée en mode **Brouillon** et doit être **publiée** avant de pouvoir être attribuée et déployée.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez la page **Définitions de blueprint** à gauche. Utilisez les filtres pour trouver la définition de blueprint _locked-storageaccount_, puis sélectionnez celle-ci.

1. Sélectionnez **Publier le blueprint** dans le haut de la page. Dans le nouveau volet à droite, définissez **Version** sur _1.0_. Cette propriété est utile si vous prévoyez d’effectuer une modification ultérieurement. Complétez **Change notes** (Remarques sur les modifications) avec un libellé, tel que « Première version publiée pour le verrouillage des ressources déployées du blueprint. » Sélectionnez ensuite **Publier** en bas de la page.

Cette étape permet d’affecter le plan à un abonnement. Après publication, des modifications sont toujours possibles. Les modifications supplémentaires nécessitent la publication avec une nouvelle valeur de **Version** afin de suivre les différences entre les versions différentes d’une même définition de blueprint.

Dès que la notification **Définition de blueprint publiée** apparaît dans le portail, passez à l’étape suivante.

## <a name="assign-the-blueprint-definition"></a>Affecter la définition de blueprint

Une fois que la définition de blueprint a été **publiée**, elle peut être affectée à un abonnement dans le groupe d’administration où elle a été enregistrée. C’est à cette étape que les paramètres sont fournis pour que chaque déploiement de la définition de blueprint soit unique.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez la page **Définitions de blueprint** à gauche. Utilisez les filtres pour trouver la définition de blueprint _locked-storageaccount_, puis sélectionnez celle-ci.

1. Sélectionnez **Affecter le blueprint** dans le haut de la page.

1. Indiquez les valeurs des paramètres pour l’affectation du blueprint :

   - Concepts de base

     - **Abonnements** : Sélectionnez un ou plusieurs des abonnements qui font partie du groupe d’administration où vous avez enregistré votre définition de blueprint. Si vous sélectionnez plusieurs abonnements, une affectation est créée pour chacun d’eux à l’aide des paramètres saisis.
     - **Nom de l’affectation** : Le nom est prérempli automatiquement en fonction du nom de la définition du blueprint. Cette affectation représente le verrouillage du nouveau groupe de ressources. Renommez celle-ci en _assignment-locked-storageaccount-TestingBPLocks_.
     - **Emplacement** : Sélectionnez une région dans laquelle créer l’identité managée. Azure Blueprint utilise cette identité managée pour déployer tous les artefacts dans le blueprint affecté. Pour en savoir plus, consultez [Identités managées pour les ressources Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Pour ce didacticiel, sélectionnez _USA Est 2_.
     - **Version de définition du blueprint** : Choisissez la version **Published** _1.0_ de la définition de blueprint.

   - Verrouiller l'affectation

     Sélectionnez le mode de verrouillage _Lecture seule_. Pour plus d’informations, consultez [Verrouillage des ressources des blueprints](../concepts/resource-locking.md).

   - Identité managée

     Conservez l’option par défaut, _Affecté(e) par le système_. Pour plus d’informations, voir [Identités managées](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Paramètres d'artefact

     Les paramètres définis dans cette section s’appliquent à l’artefact sous lequel elle est définie. Ces paramètres sont des [paramètres dynamiques](../concepts/parameters.md#dynamic-parameters) puisqu’ils sont définis lors de l’affectation du blueprint. Pour chaque artefact, définissez la valeur du paramètre en vous référant à la colonne **Valeur**.

     |Nom de l’artefact|Type d’artefact|Nom du paramètre|Valeur|Description|
     |-|-|-|-|-|
     |Groupe de ressources RGtoLock|Groupe de ressources|Nom|TestingBPLocks|Définit le nom du premier groupe de ressources auquel appliquer les verrous de blueprint.|
     |Groupe de ressources RGtoLock|Groupe de ressources|Lieu|USA Ouest 2|Définit l’emplacement du nouveau groupe de ressources auquel appliquer les verrous de blueprint.|
     |StorageAccount|Modèle Resource Manager|storageAccountType (StorageAccount)|Standard_GRS|Sélectionnez la référence SKU de stockage. La valeur par défaut est _Standard_LRS_.|

1. Une fois tous les paramètres entrés, sélectionnez **Affecter** au bas de la page.

Cette étape déploie les ressources définies et configure l’option **Verrouiller l’affectation** sélectionnée. L’application des verrous de blueprint peut prendre jusqu’à 30 minutes.

Dès que la notification **Définition de blueprint affectée** apparaît dans le portail, passez à l’étape suivante.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Examiner les ressources déployées par l’affectation

L’affectation a créé le groupe de ressources _TestingBPLocks_ et le compte de stockage déployé par l’artefact du modèle Resource Manager. Le nouveau groupe de ressources et l’état de verrouillage sélectionné sont affichés sur la page de détails de l’affectation.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez la page **Blueprints affectés** à gauche. Utilisez les filtres pour trouver l’affectation de blueprint _assignment-locked-storageaccount-TestingBPLocks_, puis sélectionnez celle-ci.

   Cette page répertorie l’affectation réussie, les ressources déployées ainsi que l’état de verrouillage de blueprint qui leur est associé. Si l’affectation est mise à jour, le menu déroulant **Opération d’affectation** affiche des détails sur le déploiement de chaque version de définition. Il est possible de cliquer sur le groupe de ressources pour ouvrir directement la page de propriétés qui lui est associée.

1. Sélectionnez le groupe de ressources **TestingBPLocks**.

1. Sélectionnez la page **Contrôle d’accès (IAM)** à gauche, puis l’onglet **Attributions de rôles**.

   Nous pouvons constater que l’affectation de blueprint _assignment-locked-storageaccount-TestingBPLocks_ possède le rôle _Propriétaire_, qui a permis de déployer et verrouiller le groupe de ressources.

1. Sélectionnez l’onglet **Affectations de refus**.

   L’affectation du blueprint a créé une instance [Refuser l’affectation](../../../role-based-access-control/deny-assignments.md) sur le groupe de ressources déployé pour appliquer le mode _Lecture seule_ de verrouillage du blueprint. Cette affectation de refus empêche un utilisateur disposant de droits appropriés dans l’onglet _Attributions de rôle_ d’effectuer des actions particulières. L’affectation de refus concerne _Tous les principaux_.

   Pour plus d’informations sur l’exclusion d’un principal à partir d’une affectation de refus, consultez [Verrouillage des ressources des blueprints](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Sélectionnez l’affectation de refus, puis la page **Autorisations refusées** à gauche.

   L’affectation de refus empêche toutes les opérations avec la configuration **\*** et **Action**, mais autorise l’accès en lecture en excluant **\*/read** via **NotActions**.

1. Dans la barre de navigation du portail Azure, sélectionnez **TestingBPLocks - Contrôle d’accès (IAM)**. Sélectionnez ensuite la page **Vue d’ensemble** à gauche, puis le bouton **Supprimer le groupe de ressources**. Entrez le nom _TestingBPLocks_ pour confirmer la suppression et sélectionnez **Supprimer** dans le bas du volet.

   Le message de notification du portail **Échec de la suppression du groupe de ressources TestingBPLocks**  apparaît alors. L’erreur indique que, même si votre compte est autorisé à supprimer le groupe de ressources, l’accès est refusé par l’affectation du blueprint. N’oubliez pas que nous avons sélectionné le mode _Lecture seule_ de verrouillage du blueprint pendant l’affectation du blueprint. Le verrou de blueprint empêche un compte disposant d’autorisations, y compris _Propriétaire_, de supprimer la ressource. Pour plus d’informations, consultez [Verrouillage des ressources des blueprints](../concepts/resource-locking.md).

Ces étapes montrent que nos ressources déployées sont à présent protégées avec les verrous du blueprint qui ont empêché la suppression non souhaitée, même à partir d’un compte disposant d’autorisations.

## <a name="unassign-the-blueprint"></a>Annuler l’assignation du blueprint

La dernière étape consiste à supprimer l’affectation de la définition de blueprint. Le fait de supprimer l’affectation ne supprime pas les artefacts concernés.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez la page **Blueprints affectés** à gauche. Utilisez les filtres pour trouver l’affectation de blueprint _assignment-locked-storageaccount-TestingBPLocks_, puis sélectionnez celle-ci.

1. Sélectionnez le bouton **Annuler l’affectation du blueprint** en haut de la page. Lisez l’avertissement dans la boîte de dialogue de confirmation, puis sélectionnez **OK**.

   La suppression du blueprint entraîne la suppression des verrous de celui-ci. Les ressources créées peuvent à nouveau être supprimées par un compte disposant d’autorisations.

1. Sélectionnez **Groupes de ressources** dans le menu Azure, puis sélectionnez **TestingBPLocks**.

1. Sélectionnez la page **Contrôle d’accès (IAM)** à gauche, puis l’onglet **Attributions de rôles**.

Du point de vue de la sécurité du groupe de ressources, l’affectation du blueprint n’a plus d’accès _Propriétaire_.

Dès que la notification **Affectation de blueprint supprimée** apparaît dans le portail, passez à l’étape suivante.

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois le didacticiel terminé, supprimez les ressources suivantes :

- Groupe de ressources _TestingBPLocks_
- Définition de blueprint _locked-storageaccount_

## <a name="next-steps"></a>Étapes suivantes

- Découvrir le [cycle de vie d’un blueprint](../concepts/lifecycle.md).
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../concepts/parameters.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../concepts/resource-locking.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../concepts/sequencing-order.md).
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md).
- Résoudre les problèmes durant l’affectation d’un blueprint en suivant les étapes de [dépannage général](../troubleshoot/general.md).