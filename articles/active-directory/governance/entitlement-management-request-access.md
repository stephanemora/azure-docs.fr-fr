---
title: Demander l’accès à un package d’accès dans la gestion des droits d’utilisation de Azure Active Directory (préversion) - Azure Active Directory
description: Découvrez comment utiliser le portail Mon Accès pour demander l’accès à un package d’accès pour la gestion des droits d’utilisation de Azure Active Directory (préversion).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39a50240b4360c5b4adcd6020c2b80b0f06315f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541554"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Demander l’accès à un package d’accès dans la gestion des droits d’utilisation de Azure Active Directory (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sign-in-to-the-my-access-portal"></a>Se connecter au portail Mon Accès

La première étape consiste à se connecter au portail Mon Accès à partir duquel vous pouvez demander l’accès à un package d’accès.

**Rôle prérequis :** Demandeur

1. Recherchez un e-mail ou un message de la part du chef de projet ou du directeur commercial avec lequel vous travaillez. L’e-mail doit inclure un lien vers le package d’accès auquel vous devrez accéder. Le lien commence par :

    `https://myaccess.microsoft.com`

1. Ouvrez le lien.

1. Connectez-vous au portail Mon Accès.

    Assurez-vous de bien utiliser le compte de votre entreprise. Si vous n’en êtes pas sûr, vérifiez auprès de votre chef de projet ou de votre directeur commercial.

## <a name="request-an-access-package"></a>Demander un package d’accès

Une fois que vous avez trouvé le package d’accès dans le portail Mon Accès, vous pouvez soumettre une demande.

**Rôle prérequis :** Demandeur

1. Cochez la case pour sélectionner le package d’accès.

    ![Portail Mon Accès - Packages d’accès](./media/entitlement-management-shared/my-access-access-packages.png)

1. Cliquez sur **Demander l’accès** pour ouvrir le volet Demander l’accès.

1. Si la case **Justification métier** s’affiche, fournissez une justification pour obtenir l’accès.

1. Si la case **Demander pendant une période spécifique ?** est activée, sélectionnez **Oui** ou **Non**.

1. Si nécessaire, spécifiez la date de début et la date de fin.

    ![Portail Mon Accès - Demander l’accès](./media/entitlement-management-shared/my-access-request-access.png)

1. Lorsque vous avez terminé, cliquez sur **Envoyer** pour envoyer votre demande.

1. Cliquez sur **Historique des demandes** pour afficher la liste de vos demandes et leur état.

    Si le package d’accès nécessite une approbation, l’état de la demande est désormais en attente d’approbation.

## <a name="cancel-a-request"></a>Annuler une demande

Si vous envoyez une demande d’accès et que l’état de la demande est toujours **En attente d’approbation**, vous pouvez annuler la demande.

**Rôle prérequis :** Demandeur

1. Dans le portail Mon Accès, cliquez sur **Historique des demandes**, sur la gauche, pour afficher la liste de vos demandes et leur état.

1. Cliquez sur le lien **Affichage** pour la demande que vous souhaitez annuler.

1. Si l’état de la demande est toujours **En attente d’approbation**, vous pouvez cliquer sur **Annuler la demande** pour annuler la demande.

    ![Portail Mon Accès - Annuler la demande](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Cliquez sur **Historique des demandes** pour confirmer que la demande a bien été annulée.

## <a name="select-a-policy"></a>Sélectionner une stratégie

Si vous demandez l’accès à un package d’accès qui contient plusieurs stratégies qui s’appliquent, vous devrez peut-être sélectionner une stratégie. Par exemple, un gestionnaire de package d’accès peut configurer un package d’accès avec deux stratégies pour deux groupes d’employés internes. La première stratégie peut autoriser l’accès pendant 60 jours et nécessiter une approbation. La deuxième stratégie peut autoriser l’accès pendant 2 jours et ne nécessiter aucune approbation. Si vous rencontrez ce scénario, vous devez sélectionner la stratégie que vous souhaitez utiliser.

**Rôle prérequis :** Demandeur

## <a name="next-steps"></a>Étapes suivantes

- [Approuver ou refuser les demandes d’accès](entitlement-management-request-approve.md)
- [Processus de demande et notifications par e-mail](entitlement-management-process.md)
