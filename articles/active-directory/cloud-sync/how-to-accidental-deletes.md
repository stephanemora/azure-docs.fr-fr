---
title: Suppressions accidentelles lors de la synchronisation cloud Azure AD Connect
description: Cette rubrique explique comment utiliser la fonctionnalité de suppression accidentelle pour empêcher les suppressions.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da54bd28c1d9ea933e88b6c86cf6092c10d036a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785183"
---
# <a name="accidental-delete-prevention"></a>Prévention des suppressions accidentelles

Le document suivant décrit la fonctionnalité de suppression accidentelle pour la synchronisation cloud Azure AD Connect.  La fonctionnalité de suppression accidentelle est conçue pour vous protéger contre les modifications accidentelles de la configuration et contre les modifications apportées à votre annuaire local qui auraient une incidence sur de nombreux utilisateurs et groupes.  Cette fonctionnalité vous permet d’effectuer les opérations suivantes :

- Configurer la possibilité d’empêcher automatiquement les suppressions accidentelles. 
- Définir le nombre d’objets (seuil) au-delà duquel la configuration prend effet. 
- Configurer une adresse e-mail pour qu’elle puisse recevoir une notification une fois que le travail de synchronisation en question est placé en quarantaine pour ce scénario. 

Pour utiliser cette fonctionnalité, vous définissez le seuil pour le nombre d’objets. Si ce nombre d’objets est supprimé, la synchronisation s’arrête.  Ainsi, si ce nombre est atteint, la synchronisation s’arrête et une notification est envoyée à l’adresse e-mail spécifiée.  Cette notification vous permet d’examiner ce qui se passe.


## <a name="configure-accidental-delete-prevention"></a>Configurer la prévention des suppressions accidentelles
Pour utiliser la nouvelle fonctionnalité, suivez les étapes ci-dessous.


1.  Dans le portail Azure, sélectionnez **Azure Active Directory**.
2.  Sélectionnez ensuite **Azure AD Connect**.
3.  Sélectionnez **Gérer la synchronisation cloud**.
4. Sous **Configuration**, sélectionnez votre configuration.
5. Sous **Paramètres**, renseignez les éléments suivants :
    - **E-mail de notification** : e-mail utilisé pour les notifications.
    - **Empêcher les suppressions accidentelles** : cochez cette case pour activer la fonctionnalité.
    - **Seuil de suppression accidentelle** : entrez le nombre d’objets à atteindre pour arrêter la synchronisation et envoyer une notification.

![Suppressions accidentelles](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>Récupération à partir d’une instance de suppression accidentelle
Si une suppression accidentelle se produit, vous la constatez dans l’état de la configuration de votre agent de provisionnement.  Celui-ci indique **Seuil de suppression dépassé**.
 
![État de suppression accidentelle](media/how-to-accidental-deletes/delete-1.png)

En cliquant sur **Seuil de suppression dépassé**, les informations d’état de la synchronisation apparaissent.  Celles-ci fournissent des détails supplémentaires. 
 
 ![État de synchronisation](media/how-to-accidental-deletes/delete-2.png)

En cliquant avec le bouton droit sur les points de suspension, vous obtenez les options suivantes :
 - Afficher le journal de provisionnement
 - Afficher l’agent
 - Autoriser les suppressions

 ![Clic droit](media/how-to-accidental-deletes/delete-3.png)

L’option **Afficher le journal de provisionnement** vous permet de voir les entrées **StagedDelete** et de passer en revue les informations fournies sur les utilisateurs qui ont été supprimés.
 
 ![Journaux d’approvisionnement](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>Autoriser les suppressions

L’action **Autoriser les suppressions** permet de supprimer les objets qui ont déclenché le seuil de suppression accidentelle.  Utilisez la procédure suivante pour accepter les suppressions.  

1. Cliquez avec le bouton droit sur les points de suspension et sélectionnez **Autoriser les suppressions**.
2. Cliquez sur **Oui** dans le message de confirmation pour autoriser les suppressions.
 
 ![Oui à la confirmation](media/how-to-accidental-deletes/delete-4.png)

3. Vous recevez une confirmation que les suppressions ont été acceptées et que l’état va redevenir sain lors du cycle suivant. 
 
 ![Accepter les suppressions](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>Refus des suppressions

Si vous ne voulez pas autoriser les suppressions, vous devez effectuer les étapes suivantes :
- Déterminez la source des suppressions.
- Résolvez le problème (par exemple, une unité d’organisation a été déplacée en dehors de l’étendue par inadvertance et vous l’avez rajoutée à l’étendue).
- Exécutez **Redémarrer la synchronisation** sur la configuration de l’agent.

## <a name="next-steps"></a>Étapes suivantes 

- [Résolution des problèmes de synchronisation cloud Azure AD Connect](how-to-troubleshoot.md)
- [Codes d’erreur de synchronisation cloud Azure AD Connect](reference-error-codes.md)
 

