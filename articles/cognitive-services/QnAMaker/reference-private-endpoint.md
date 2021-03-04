---
title: Configurer un point de terminaison privé - QnA Maker
description: Comprendre la création d’un point de terminaison privé disponible dans QnA Maker managé.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 7907c81e45680de49f6653891fb4204a59db1002
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710547"
---
# <a name="private-endpoints"></a>Points de terminaison privés

Azure Private Endpoint est une interface réseau qui vous permet de vous connecter de façon privée et sécurisée à un service basé sur Azure Private Link. QnA Maker prend désormais en charge de la création de points de terminaison privés dans le service Recherche Azure. Cette fonctionnalité est disponible dans QnA Maker managé. 

Les points de terminaison privés sont fournis par [Azure Private Link](../../private-link/private-link-overview.md), en tant que service distinct. Pour plus d’informations sur les coûts, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/private-link/). 

## <a name="prerequisites"></a>Prérequis
> [!div class="checklist"]
> * Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer.
> * Une [ressource gérée](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) QnA Maker créée dans le portail Azure. Rappelez-vous l’ID Azure Active Directory, l’abonnement et le nom de ressource QnA que vous avez sélectionnés au moment de créer la ressource.

## <a name="steps-to-enable-private-endpoint"></a>Étapes pour activer un point de terminaison privé
1. Affectez le rôle *Contributeur* au service QnA Maker managé dans l’instance du service Recherche Azure. Cette opération nécessite un accès *Propriétaire* à l’abonnement. Ouvrez l’onglet Identité de la ressource de service pour accéder à l’identité.
![Managed Service Identity](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. Ajoutez l’identité ci-dessus comme *Contributeur* en accédant à l’onglet Azure Search Service IAM. ![IAM pour service gérée](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Cliquez sur *Ajouter des attributions de rôles*, ajoutez l’identité, puis cliquez sur *Enregistrer*.
![Attribution de rôle gérée](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. À présent, ouvrez l’onglet *Mise en réseau* de l’instance du service Recherche Azure, puis basculez les données de connectivité du point de terminaison de *Public* vers *Privé*. Cette opération est un long processus qui peut prendre jusqu’à 30 minutes. 
![Mise en réseau Recherche Azure gérée](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Ouvrez l’onglet *Mise en réseau* du service QnA Maker managé, puis sous *Autoriser l’accès depuis*, sélectionnez l’option *Points de terminaison privés et réseaux sélectionnés* et cliquez sur *Enregistrer*. 
![Mise en réseau QnA Maker managé](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

Cela permet d’établir une connexion de point de terminaison privée entre le service QnA Maker et l’instance du service Recherche cognitive Azure. Vous pouvez vérifier la connexion au point de terminaison privé dans l’onglet *Mise en réseau* de l’instance du service Recherche Azure. Une fois l’intégralité de l’opération terminée, vous pouvez utiliser votre service QnA Maker. 
![Service de mise en réseau géré](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>Détails de la prise en charge
 * Nous ne prenons pas en charge la modification du service Recherche Azure une fois que vous avez activé l’accès privé à votre service QnAMaker. Si vous modifiez le service Recherche Azure via l’onglet « Configuration » après avoir activé l’accès privé, le service QnAMaker deviendra inutilisable.
 * Après avoir établi la connexion au point de terminaison privé, si vous basculez la mise en réseau du service Recherche Azure sur « Public », vous ne pourrez plus utiliser le service QnAMaker. La mise en réseau du service Recherche Azure doit être « Privée » pour que la connexion au point de terminaison privé fonctionne