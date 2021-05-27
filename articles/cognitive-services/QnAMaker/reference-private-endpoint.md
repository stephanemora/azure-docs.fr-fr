---
title: Configurer un point de terminaison privé - QnA Maker
description: Comprendre la création d’un point de terminaison privé disponible dans QnA Maker managé.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 00a55b852158abee01692d3c4ccbf1209c937cf4
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110379516"
---
# <a name="private-endpoints"></a>Points de terminaison privés

Azure Private Endpoint est une interface réseau qui vous permet de vous connecter de façon privée et sécurisée à un service basé sur Azure Private Link. Désormais, le service Réponses aux questions personnalisées vous aide à créer des points de terminaison privés dans le service Recherche Azure.

Les points de terminaison privés sont fournis par [Azure Private Link](../../private-link/private-link-overview.md), en tant que service distinct. Pour plus d’informations sur les coûts, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/private-link/). 

## <a name="prerequisites"></a>Prérequis
> [!div class="checklist"]
> * Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer.
> * Vous devez disposer d’une [ressource Analyse de texte](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics) (avec la fonctionnalité Réponses aux questions personnalisées) créée dans le portail Azure. Rappelez-vous l’ID Azure Active Directory, l’abonnement et le nom de ressource Analyse de texte que vous avez sélectionnés lors de la création de la ressource.

## <a name="steps-to-enable-private-endpoint"></a>Étapes pour activer un point de terminaison privé
1. Attribuez le rôle *Contributeur* au service Analyse de texte dans l’instance du service Recherche Azure. Cette opération nécessite un accès *Propriétaire* à l’abonnement. Ouvrez l’onglet Identité de la ressource de service pour accéder à l’identité.

> [!div class="mx-imgBorder"]
> ![Identité d’Analyse de texte](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoints-identity.png)

2. Ajoutez l’identité ci-dessus comme *Contributeur* en accédant à l’onglet Azure Search Service IAM.

![IAM pour service géré](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Cliquez sur *Ajouter des attributions de rôles*, ajoutez l’identité, puis cliquez sur *Enregistrer*.

![Attribution de rôle gérée](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. À présent, ouvrez l’onglet *Mise en réseau* de l’instance du service Recherche Azure, puis basculez les données de connectivité du point de terminaison de *Public* vers *Privé*. Cette opération est un long processus qui peut prendre jusqu’à 30 minutes. 

![Mise en réseau Recherche Azure gérée](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Ouvrez l’onglet *Mise en réseau* du service Analyse de texte, puis, sous *Autoriser l’accès depuis*, sélectionnez l’option *Points de terminaison privés et réseaux sélectionnés* et cliquez sur *Enregistrer*.
 
> [!div class="mx-imgBorder"]
> ![Mise en réseau du service Analyse de texte](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-custom-qna.png)

Cela permet d’établir une connexion de point de terminaison privée entre le service Analyse de texte et l’instance du service Recherche cognitive Azure. Vous pouvez vérifier la connexion au point de terminaison privé sous l’onglet *Mise en réseau* de l’instance du service Recherche cognitive Azure. Une fois l’intégralité de l’opération terminée, vous pouvez utiliser votre service Analyse de texte. 

![Service de mise en réseau géré](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>Détails de la prise en charge
 * Une fois que vous avez activé l’accès privé à votre service Analyse de texte, nous ne prenons plus en charge les modifications apportées au service Azure Recherche cognitive. Si vous modifiez le service Recherche cognitive Azure via l’onglet « Fonctionnalités » après avoir activé l’accès privé, le service Analyse de texte devient inutilisable.
 * Après avoir établi une connexion au point de terminaison privé, si vous basculez la mise en réseau du service Recherche cognitive Azure sur « Publique », vous ne pouvez plus utiliser le service Analyse de texte. La mise en réseau du service Recherche Azure doit être « Privée » pour que la connexion au point de terminaison privé fonctionne
