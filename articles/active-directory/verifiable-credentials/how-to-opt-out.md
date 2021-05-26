---
title: Refuser les justificatifs vérifiables Azure Active Directory (préversion)
description: Découvrez comment refuser les justificatifs vérifiables en préversion
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 1fe2d9755eac9a4048f7ed61388d0cdc7914552c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466092"
---
# <a name="opt-out-of-the-verifiable-credentials-preview"></a>Refuser la fonctionnalité des justificatifs vérifiables (préversion)

Contenu de cet article :

- Les motifs de refus de cette fonctionnalité.
- Les étapes requises.
- Qu’advient-il de vos données ?
- Effet sur les justificatifs vérifiables existants.

> [!IMPORTANT]
> Les justificatifs vérifiables Azure Active Directory sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

- Effectuer l’intégration des justificatifs vérifiables.

## <a name="potential-reasons-for-opting-out"></a>Motifs potentiels de refus de la fonctionnalité

À ce stade, nous n’avons pas la possibilité de modifier les informations du domaine. Par conséquent, si vous faites une erreur ou souhaitez effectuer une modification, la seule option consiste à refuser la fonctionnalité puis à redémarrer.

## <a name="the-steps-required"></a>Étapes requises

1. À partir du portail Azure, recherchez des justificatifs vérifiables.
2. Choisissez **Paramètres** dans le menu de gauche.
3. Dans la section, **Réinitialiser votre organisation**, sélectionnez **Supprimer tous les justificatifs et refuser la préversion**.

   ![Paramètres Réinitialiser l’organisation](media/how-to-opt-out/settings-reset.png)

4. Lisez le message d’avertissement et pour continuer, sélectionnez **Supprimer et refuser**.

   ![Paramètres Supprimer et refuser](media/how-to-opt-out/delete-and-opt-out.png)

Vous avez maintenant refusé la fonctionnalité des justificatifs vérifiables en préversion. Poursuivez votre lecture pour comprendre le fonctionnement sous-jacent.

## <a name="what-happens-to-your-data"></a>Qu’advient-il de vos données ?

Une fois que vous avez refusé le service des justificatifs vérifiables dans Azure Active Directory, les actions suivantes sont effectuées :

- Dans Key Vault, les clés DID ont été [supprimées de manière réversible](../../key-vault/general/soft-delete-overview.md).
- L’objet émetteur sera supprimé de notre base de données.
- L’identificateur de locataire sera supprimé de notre base de données. 
- Tous les objets de contrats seront supprimés de notre base de données.

Après un refus, vous ne pourrez pas récupérer votre DID ou effectuer des opérations sur votre DID. Cette étape est une opération unidirectionnelle et vous devez à nouveau accepter la fonctionnalité, ce qui entraîne la création d’un nouveau DID.  

## <a name="effect-on-existing-verifiable-credentials"></a>Effet sur les justificatifs vérifiables existants

Tous les justificatifs vérifiables déjà émis continuent d’exister. Ils ne seront pas invalidés par chiffrement, car votre DID continue d’être résolu par le biais du nom ION.
Toutefois, quand des parties de confiance appellent l’API d’état, elles reçoivent toujours un message d’échec.

## <a name="next-steps"></a>Étapes suivantes

- Configurer des justificatifs vérifiables sur votre [locataire Azure](get-started-verifiable-credentials.md)