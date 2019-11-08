---
title: Recevoir des notifications concernant un coffre de clés et y répondre avec Azure Event Grid
description: Découvrez comment intégrer Key Vault avec Azure Event Grid.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 3b24da4d988554da240baba2984df44ff4744aaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464106"
---
# <a name="how-to-receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Activation Recevoir des notifications concernant un coffre de clés et y répondre avec Azure Event Grid (préversion)

L’intégration entre Key Vault et Azure Event Grid, actuellement en préversion, permet aux utilisateurs d’être avertis en cas de modification de l’état d’un secret stocké dans le coffre de clés. Pour une vue d’ensemble de la fonctionnalité, voir [Monitoring de Key Vault avec Azure Event Grid](event-grid-overview.md).

Ce guide montre comment recevoir des notifications de Key Vault par le biais d’Azure Event Grid et comment répondre aux changements d’état avec Azure Automation.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Un coffre de clés dans votre abonnement Azure. Pour créer rapidement un coffre de clés, suivez les étapes décrites dans [Définir et récupérer un secret dans Azure Key Vault avec Azure CLI](quick-create-cli.md).

## <a name="concepts"></a>Concepts

Azure Event Grid est un service de gestion d’événements pour le cloud. Dans ce guide, vous allez vous abonner aux événements du coffre de clés et diriger les événements vers Azure Automation. Lorsque l’un des secrets du coffre de clés est sur le point d’expirer, Event Grid est averti de la modification de l’état et lance une requête HTTP POST sur le point de terminaison. Un webhook déclenche ensuite une exécution Azure Automation d’un script PowerShell.

![image](media/image1.png)

## <a name="create-an-azure-automation-account"></a>Créer un compte Azure Automation

Créez un compte Azure Automation sur le [Portail Azure](https://portal.azure.com).

1.  Accédez à portal.azure.com et connectez-vous à votre abonnement.

1.  Dans la zone de recherche, tapez « Comptes Automation ».

1.  Sous la section « Services » de la liste déroulante de la barre de recherche, sélectionnez « Comptes Automation ».

1.  Cliquez sur Ajouter.

    ![](media/image2.png)

1.  Renseignez les informations requises dans le panneau « Ajouter un compte Automation » et sélectionnez « Créer ».

## <a name="create-a-runbook"></a>Créer un runbook

Une fois votre compte Azure Automation prêt, créez un runbook.

![](media/image3.png)

1.  Sélectionnez le compte Automation que vous venez de créer.

1.  Sélectionnez « Runbooks » sous la section Automatisation des processus.

1.  Cliquez sur « Créer un runbook ».

1.  Donnez un nom à votre runbook et sélectionnez « PowerShell » comme type de runbook.

1.  Cliquez sur le runbook que vous avez créé, puis sélectionnez le bouton « Modifier ».

1.  Entrez le code suivant (à des fins de test), puis cliquez sur le bouton « Publier » pour obtenir en sortie le résultat de la requête POST reçue.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![](media/image4.png)

## <a name="create-a-webhook"></a>Créer un webhook

À présent, créez un webhook pour déclencher votre nouveau runbook.

1.  Sélectionnez « Webhooks » dans la section Ressources du runbook que vous venez de publier.

1.  Cliquez sur « Ajouter un webhook ».

    ![](media/image5.png)

1.  Sélectionnez « Créer un webhook ».

1. Donnez un nom au webhook, définissez une date d’expiration et **copiez l’URL**.

    > [!IMPORTANT] 
    > Il n’est pas possible d’afficher l’URL après la création. Veillez à enregistrer une copie dans un endroit sûr et accessible pour la suite de ce guide.

1. Cliquez sur « Paramètres et paramètres d’exécution », puis sélectionnez « OK ». N’entrez aucun paramètre. Le bouton « Créer » s’active.

1. Sélectionnez « OK », puis « Créer ».

    ![](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Créer un abonnement Event Grid

Créez un abonnement Event Grid sur le [Portail Azure](https://portal.azure.com).

1.  Ouvrez le Portail Azure en cliquant sur le lien suivant : https://portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true.

1.  Accédez à votre coffre de clés et sélectionnez l’onglet « Événements ». Si l’onglet n’apparaît pas, vérifiez que vous utilisez bien la [préversion du portail](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true).

    ![](media/image7.png)

1.  Cliquez sur le bouton « + Abonnement aux événements ».

1.  Donnez un nom descriptif à l’abonnement.

1.  Choisissez « Schéma Event Grid ».

1.  « Ressource de rubrique » est normalement le coffre de clés à surveiller pour connaître les changements d’état.

1.  Dans « Filtrer sur les types d’événements », laissez toutes les cases cochées (« 9 sélectionnés »).

1.  Dans « Type de point de terminaison », sélectionnez « Webhook ».

1.  Sélectionnez « Sélectionner un point de terminaison ». Dans le nouveau volet contextuel, collez l’URL du webhook de l’étape [Créer un webhook](#create-a-webhook) dans le champ « Point de terminaison de l’abonné ».

1.  Sélectionnez « Confirmer la sélection » dans le volet contextuel.

1.  Sélectionnez « Create  (Créer) ».

    ![](media/image8.png)

## <a name="test-and-verify"></a>Tester et vérifier

Vérifiez que votre abonnement Event Grid est correctement configuré.  Ce test suppose d’être abonné à la notification « Nouvelle version du secret créée » de [Créer un abonnement Event Grid](#create-an-event-grid-subscription) et de disposer des privilèges nécessaires pour créer une nouvelle version d’un secret dans un coffre de clés.

![](media/image9.png)

![](media/image10.png)

1.  Accédez à votre coffre de clés sur le Portail Azure.

1.  Créer un secret. À des fins de test, définissez la date d’expiration sur le jour suivant.

1.  Accédez à l’onglet des événements dans votre coffre de clés.

1.  Sélectionnez l’abonnement Event Grid que vous avez créé.

1.  Sous Métriques, regardez si un événement a été capturé. Deux événements sont attendus : SecretNewVersion et SecretNearExpiry. Cela confirme qu’Event Grid a bien capturé le changement d’état du secret dans votre coffre de clés.

    ![](media/image11.png)

1.  Allez sur votre compte Azure Automation.

1.  Sélectionnez l’onglet « Runbooks », puis le runbook que vous avez créé.

1.  Sélectionnez l’onglet « Webhooks », puis vérifiez que le timestamp « Dernier déclenchement » se situe dans un délai de 60 secondes après la création du nouveau secret.  Cela confirme qu’Event Grid a lancé une requête POST sur le webhook en indiquant les détails de l’événement de changement d’état dans votre coffre de clés et que le webhook a été déclenché.

    ![](media/image12.png)

1. Revenez à votre runbook et sélectionnez l’onglet « Vue d’ensemble ».

1. Examinez la liste Tâches récentes. Vous devriez voir qu’une tâche a été créée et que l’état est terminé.  Cela confirme que le webhook a déclenché le runbook pour commencer l’exécution de son script.

    ![](media/image13.png)

1. Sélectionnez la tâche récente, puis examinez la requête POST envoyée au webhook par Event Grid. Examinez le JSON et vérifiez que les paramètres de votre coffre de clés et du type d’événement sont corrects. Si le paramètre « type d’événement » de l’objet JSON correspond à l’événement qui s’est produit dans le coffre de clés (dans cet exemple, Microsoft.KeyVault.SecretNearExpiry), le test a réussi.

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="unable-to-create-event-subscription"></a>Impossible de créer un abonnement aux événements

Réinscrivez Event Grid et le fournisseur Key Vault dans vos fournisseurs de ressources d’abonnement Azure. Voir [Fournisseurs et types de ressources Azure](../azure-resource-manager/resource-manager-supported-services.md).

## <a name="next-steps"></a>Étapes suivantes

Félicitations ! Si vous avez suivi toutes les étapes ci-dessus, vous savez maintenant répondre programmatiquement aux modifications d’état des secrets stockés dans votre coffre de clés.

Si vous utilisiez un système basé sur l’interrogation pour rechercher les modifications d’état des secrets dans vos coffres de clés, changez pour utiliser cette fonctionnalité de notification. Vous pouvez également remplacer le script de test de votre runbook par du code pour renouveler programmatiquement vos secrets lorsqu’ils sont sur le point d’expirer.

En savoir plus :

- [Vue d’ensemble d’Azure Key Vault](key-vault-overview.md)
- [Vue d’ensemble d’Azure Event Grid](../event-grid/overview.md)
- [Monitoring de Key Vault avec Azure Event Grid (préversion)](event-grid-overview.md)
- [Schéma des événements Azure Event Grid pour Azure Key Vault (préversion)](../event-grid/event-schema-key-vault.md)
- [Vue d’ensemble d’Azure Automation](../automation/index.yml)
