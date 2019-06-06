---
title: Activer et demandez l’accès juste-à-temps pour les Applications gérées Azure
description: Décrit comment les serveurs de publication des Applications managées Azure demandent un accès juste-à-temps à une application managée.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: ea933f5382cb42c01de523326b094c1813401132
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481773"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Activer et demandez l’accès juste-à-temps pour les Applications gérées Azure

Les consommateurs de votre application managée peuvent être réticents à accorder un accès permanent au groupe de ressources managé. En tant que serveur de publication d’une application de gestionnaire, vous préférerez peut-être que les consommateurs sachent exactement quand vous avez besoin accéder aux ressources gérées. Pour donner aux clients les mieux contrôler l’accès aux ressources gérées, les Applications gérées Azure fournit une fonctionnalité appelée l’accès juste-à-temps (JIT), qui est actuellement en version préliminaire.

Accès JIT vous permet de demander l’accès avec élévation de privilèges à des ressources d’une application managée pour le dépannage ou de maintenance. Vous avez toujours accès en lecture seule aux ressources, mais pour une période de temps spécifique, vous pouvez avoir un accès supérieur.

Le flux de travail pour accorder l’accès est :

1. Vous ajoutez une application managée à la place de marché et que vous spécifiez que l’accès JIT est disponible.

1. Au cours du déploiement, le consommateur permet l’accès JIT pour cette instance de l’application managée.

1. Après le déploiement, le consommateur peut modifier les paramètres pour l’accès JIT.

1. Vous envoyez une demande d’accès lorsque vous avez besoin pour résoudre les problèmes ou de mettre à jour les ressources managées.

1. Le consommateur approuve votre demande.

Cet article se concentre sur les actions des serveurs de publication pour activer l’accès JIT et soumettre des demandes. Pour en savoir plus sur l’approbation des demandes d’accès JIT, consultez [approuver l’accès juste-à-temps dans les Applications gérées Azure](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Ajouter l’étape de l’accès JIT à l’interface utilisateur

Votre fichier CreateUiDefinition.json est exactement comme le fichier d’interface utilisateur créé pour un accès permanent, mais vous devez inclure une étape qui permet aux consommateurs d’activer l’accès JIT. Pour en savoir plus sur la publication de votre première application managée offre dans Azure Marketplace, consultez [les Applications gérées Azure dans la place de marché](publish-marketplace-app.md).

Pour prendre en charge la fonctionnalité JIT pour votre offre, ajoutez le contenu suivant à votre fichier CreateUiDefinition.json :

Dans « étapes » :

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```
 
« Outputs » :

```json
"jitAccessPolicy": "[parse(concat('{\"jitAccessEnabled\":', string(steps('jitConfiguration').jitConfigurationControl.jitEnabled), ',\"jitApprovalMode\":\"', steps('jitConfiguration').jitConfigurationControl.jitApprovalMode, '\",\"maximumJitAccessDuration\":\"', steps('jitConfiguration').jitConfigurationControl.maxAccessDuration, '\",\"jitApprovers\":', string(steps('jitConfiguration').jitConfigurationControl.approvers), '}'))]"
```

> [!NOTE]
> Accès JIT est en version préliminaire. Le schéma de configuration de JIT peut changer dans les futures itérations.

## <a name="enable-jit-access"></a>Activer l’accès JIT

Lorsque vous définissez votre offre dans la place de marché, assurez-vous que vous activez l’accès JIT.

1. Se connecter à la [portail de publication Cloud partenaire](https://cloudpartner.azure.com).

1. Fournir des valeurs pour publier votre application gérée dans la place de marché. Sélectionnez **Oui** pour **activer l’accès JIT ?**

   ![Activer l’accès juste-à-temps](./media/request-just-in-time-access/marketplace-enable.png)

Vous avez ajouté une étape de configuration JIT à votre interface utilisateur et que vous avez activé l’accès JIT dans l’offre de la place de marché. Lorsque les consommateurs de déploiement votre application managée, ils peuvent [activer l’accès JIT pour leur instance](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Demander l'accès

Lorsque vous avez besoin d’accéder aux ressources gérées du consommateur, vous envoyez une demande pour un rôle spécifique, l’heure et la durée. Le consommateur doit ensuite approuver la demande.

Pour envoyer une demande d’accès JIT :

1. Sélectionnez **accès JIT** pour l’application managée, vous devez accéder.

1. Sélectionnez **rôles éligibles**, puis sélectionnez **activer** dans la colonne d’ACTION pour le rôle que vous souhaitez.

   ![Activer la demande d’accès](./media/request-just-in-time-access/send-request.png)

1. Sur le **activer le rôle** , sélectionnez une heure de début et la durée pour votre rôle d’être actives. Sélectionnez **activer** pour envoyer la demande.

   ![Activer l’accès](./media/request-just-in-time-access/activate-access.png) 

1. Afficher les notifications pour voir que la nouvelle demande JIT est correctement envoyée au consommateur.

   ![Notification](./media/request-just-in-time-access/in-progress.png)

   Maintenant, vous devez attendre au consommateur de [approuver votre demande](approve-just-in-time-access.md#approve-requests).

1. Pour afficher l’état de toutes les demandes JIT pour une application managée, sélectionnez **accès JIT** et **historique demande**.

   ![Afficher l’état](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Problèmes connus

L’ID du principal du compte demandant l’accès JIT doit être explicitement inclus dans la définition d’application managée. Le compte ne peut uniquement être inclus via un groupe qui est spécifié dans le package. Cette limitation sera résolue dans une version ultérieure.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’approbation des demandes pour l’accès JIT, consultez [approuver l’accès juste-à-temps dans les Applications gérées Azure](approve-just-in-time-access.md).
