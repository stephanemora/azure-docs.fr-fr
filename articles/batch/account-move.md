---
title: Déplacer un compte Azure Batch vers une autre région
description: Apprenez à déplacer un compte Azure Batch vers une région différente.
ms.topic: how-to
ms.date: 05/05/2021
ms.custom: subject-moving-resources
ms.openlocfilehash: 9c0e45b62f6dd6152ab80beaa751925702b1fa37
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776738"
---
# <a name="move-an-azure-batch-account-to-another-region"></a>Déplacer un compte Azure Batch vers une autre région

Il existe des scénarios dans lesquels il peut être judicieux de déplacer un [compte Batch](accounts.md) existant d’une région à une autre. Par exemple, vous pouvez le déplacer vers une autre région dans le cadre de la planification d’une reprise d’activité après sinistre.

Vous ne pouvez pas déplacer des comptes Azure Batch directement d’une région à l’autre. Toutefois, vous pouvez utiliser un modèle Azure Resource Manager pour exporter la configuration existante de votre compte Batch. Vous pouvez ensuite déplacer la ressource dans une autre région en exportant le compte Batch vers un modèle, en modifiant les paramètres pour qu’ils correspondent à la région de destination, puis en déployant le modèle dans la nouvelle région. Vous pouvez ensuite recréer les travaux et autres fonctionnalités dans le compte.

 Pour plus d’informations sur Resource Manager et les modèles, consultez [Démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

Cette rubrique explique comment déplacer un compte Batch entre des régions à l’aide du portail Azure.

## <a name="prerequisites"></a>Prérequis

- Déplacez le compte de stockage associé à votre compte Batch vers la nouvelle région cible en suivant les étapes décrites dans [Déplacer un compte Stockage Azure vers une autre région](../storage/common/storage-account-move.md). Si vous préférez, vous pouvez laisser le compte de stockage dans la région d’origine. Toutefois, nous vous recommandons de le déplacer, car vous noterez généralement de meilleures performances s’il se trouve dans la même région que votre compte Batch. Les instructions ci-dessous supposent que vous avez déjà migré votre compte de stockage.
- Vérifiez que les services et fonctionnalités utilisés par votre compte Batch sont pris en charge dans la région cible.

## <a name="prepare"></a>Préparation

Pour commencer, vous devez exporter un modèle Resource Manager et modifiez-le.

### <a name="export-a-template"></a>Exporter un modèle

Tout d’abord, exportez un modèle qui contient les paramètres et les informations de votre compte Batch.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Choisissez **Toutes les ressources**, puis sélectionnez votre compte Batch.

3. Sélectionnez > **Automatisation** > **Exporter le modèle**.

4. Choisissez **Télécharger** dans le panneau **Exporter le modèle**.

5. Localisez le fichier .zip que vous avez téléchargé à partir du portail, puis décompressez-le dans le dossier de votre choix.

   Ce fichier zip contient les fichiers .json qui composent le modèle et des scripts pour le déployer.

### <a name="modify-the-template"></a>Modifier le modèle

Ensuite, chargez et modifiez le modèle afin de pouvoir créer un nouveau compte Batch dans la région cible.

1. Dans le portail Azure, sélectionnez **Créer une ressource**.

1. Dans **Rechercher sur la Place de marché**, tapez **déploiement de modèle**, puis appuyez sur **Entrée**.

1. Sélectionnez **Déploiement de modèle (déployer à l’aide de modèles personnalisés)** .

1. Sélectionnez **Create** (Créer).

1. Sélectionnez **Générer votre propre modèle dans l’éditeur**.

1. Sélectionnez **Charger le fichier**, puis sélectionnez le fichier **template.json** que vous avez téléchargé dans la section précédente.

1. Dans le fichier **template.json** téléchargé, nommez le compte Batch cible en entrant une nouvelle **defaultValue** pour le nom du compte Batch. Cet exemple définit la **defaultValue** du nom du compte Batch sur `mytargetaccount`. Il remplace aussi la chaîne dans **defaultValue** par l’ID de ressource pour `mytargetstorageaccount`.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "batchAccounts_mysourceaccount_name": {
                "defaultValue": "mytargetaccount",
                "type": "String"
            }
        },
   ```

1. Ensuite, mettez à jour la **defaultValue** du compte de stockage avec l’ID de ressource de votre compte de stockage migré. Pour obtenir cette valeur, accédez au compte de stockage dans le portail Azure, sélectionnez la **Vue JSON** près du haut de l’écran, puis copiez la valeur affichée sous **ID de ressource**. Cet exemple utilise l’ID de ressource pour un compte de stockage nommé `mytargetstorageaccount` dans le groupe de ressources `mytargetresourcegroup`.

   ```json
           "storageAccounts_mysourcestorageaccount_externalid": {
            "defaultValue": "/subscriptions/{subscriptionID}/resourceGroups/mytargetresourcegroup/providers/Microsoft.Storage/storageAccounts/mytargetstorageaccount",
            "type": "String"
        }
    },
   ```

1. Enfin, modifiez la propriété **location** pour utiliser votre région cible. Cet exemple définit `centralus` comme région cible.

```json
    {
        "resources": [
            {
                "type": "Microsoft.Batch/batchAccounts",
                "apiVersion": "2021-01-01",
                "name": "[parameters('batchAccounts_mysourceaccount_name')]",
                "location": "centralus",  
```

Pour obtenir les codes d’emplacement des régions, consultez [Emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Le code d’une région est le nom de la région sans espace. Par exemple, **Central US** = **centralus**.

## <a name="move"></a>Déplacer

Déployez le modèle pour créer un compte de Batch dans la région cible.

1. Maintenant que vous avez apporté vos modifications, sélectionnez **Enregistrer** sous le fichier **template.json**.

1. Entrez ou sélectionnez les valeurs de propriété :
   - **Abonnement**: Sélectionnez un abonnement Azure.
   - **Groupe de ressources** : sélectionnez le groupe de ressources que vous avez créé lors du déplacement du compte de stockage associé.
   - **Région** : sélectionnez la région Azure vers laquelle vous déplacez le compte.

1. Sélectionnez **Vérifier et créer**, puis **Créer**.

### <a name="configure-the-new-batch-account"></a>Configurer le nouveau compte Batch

Certaines fonctionnalités ne sont pas exportées vers un modèle. Vous devez donc les recréer dans le nouveau compte Batch. Ces options en question sont les suivantes :

- travaux
- Calendriers de travaux
- Certificats
- packages d’application

Veillez à les configurer en fonction des besoins dans le nouveau compte si nécessaire. Vous pouvez voir comment vous avez configuré ces fonctionnalités dans votre compte Batch source à des fins de référence.

## <a name="discard-or-clean-up"></a>Ignorer ou nettoyer

Une fois que vous avez confirmé que votre nouveau compte Batch fonctionne correctement dans la nouvelle région et que vous avez restauré les fonctionnalités nécessaires, vous pouvez supprimer le compte Batch source.

Pour supprimer un compte Batch à l’aide du portail Azure :

1. Sur le portail Azure, développez le menu de gauche pour ouvrir le menu des services, et sélectionnez **Comptes Batch**.

2. Recherchez le compte Batch à supprimer, puis faites un clic droit sur le bouton **Plus** ( **...** ) se trouvant à droite de la liste. Assurez-vous qu’il s’agit du compte Batch source d’origine, et non de celui que vous avez créé.

3. Sélectionnez **Supprimer**, puis confirmez.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [déplacement de ressources vers un nouveau groupe de ressource ou un nouvel abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Découvrez comment [Déplacer des machines virtuelles Azure vers une autre région](../site-recovery/azure-to-azure-tutorial-migrate.md).
