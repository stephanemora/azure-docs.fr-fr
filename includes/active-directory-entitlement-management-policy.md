---
title: Fichier Include
description: Fichier Include
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/16/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: ef8155cda6c31a63204af80ca091a13bb16a687d
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866433"
---
### <a name="policy-for-users-in-your-directory"></a>Stratégie : Pour les utilisateurs dans votre répertoire

Si vous souhaitez que votre stratégie pour les utilisateurs et groupes dans votre annuaire qui peuvent demander à ce package de l’accès, procédez comme suit.

1. Dans le **les utilisateurs qui peuvent demander l’accès** section, sélectionnez **pour les utilisateurs dans votre répertoire**.

1. Dans le **sélectionner des utilisateurs et groupes** , cliquez sur **ajouter des utilisateurs et groupes**.

1. Dans Sélectionnez les utilisateurs et volet groupes, sélectionnez les utilisateurs et groupes que vous souhaitez ajouter.

    ![Package d’accès - stratégie - sélectionnez utilisateurs et groupes](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Cliquez sur **sélectionnez** pour ajouter les utilisateurs et groupes.

1. Passez à la [stratégie : Demande](#policy-request) section.

### <a name="policy-for-users-not-in-your-directory"></a>Stratégie : Pour les utilisateurs non inclus dans votre répertoire

Si vous souhaitez que votre stratégie pour les utilisateurs non inclus dans votre annuaire qui peuvent demander à ce package de l’accès, procédez comme suit. Répertoires doivent être configurés pour être autorisé dans le **restrictions de collaboration de relations organisationnelles** paramètres.

1. Dans le **les utilisateurs qui peuvent demander l’accès** section, sélectionnez **pour les utilisateurs non inclus dans votre répertoire**.

1. Dans le **sélectionnez externe Azure AD directory** , cliquez sur **ajouter des répertoires**.

1. Entrez un nom de domaine et de la recherche pour un externe annuaire Azure AD.

1. Vérifiez qu’il est le répertoire approprié par le nom de répertoire fourni et du domaine initial.

    > [!NOTE]
    > Tous les utilisateurs à partir du répertoire sera en mesure de demander ce package de l’accès. Cela inclut les utilisateurs à partir de tous les sous-domaines associés au répertoire, pas seulement le domaine utilisé dans la recherche.

    ![Package d’accès - répertoires de la stratégie de sélection](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Cliquez sur **ajouter** pour ajouter le répertoire.

1. Répétez cette étape pour ajouter des répertoires de plus.

1. Une fois que vous avez ajouté tous les répertoires vous souhaitez inclure dans la stratégie, cliquez sur **sélectionnez**.

1. Passez à la [stratégie : Demande](#policy-request) section.

### <a name="policy-none-administrator-direct-assignments-only"></a>Stratégie : None (affectations directes administrateur uniquement)

Si vous souhaitez que votre stratégie de contourner les demandes d’accès et les administrateurs peuvent attribuer directement des utilisateurs spécifiques pour le package de l’accès, procédez comme suit. Les utilisateurs n’auront demander le package de l’accès. Vous pouvez toujours définir des paramètres d’expiration, mais aucun paramètre de requête.

1. Dans le **les utilisateurs qui peuvent demander l’accès** section, sélectionnez **None (uniquement les attributions directes administrateur**.

    Après avoir créé le package de l’accès, vous pouvez directement affecter des utilisateurs internes et externes spécifiques pour le package de l’accès. Si vous spécifiez un utilisateur externe, un compte d’utilisateur invité est créé dans votre répertoire.

1. Passez à la [stratégie : Expiration](#policy-expiration) section.

### <a name="policy-request"></a>Stratégie : Requête

Dans la section Request, vous spécifiez les paramètres d’approbation lorsque des utilisateurs demandent le package de l’accès.

1. Pour demander l’approbation des demandes des utilisateurs sélectionnés, définissez le **nécessitent une approbation** activer/désactiver pour **Oui**. Pour que les demandes approuvées automatiquement, la valeur est le bouton bascule **non**.

1. Si vous avez besoin d’approbation, dans le **sélectionner des approbateurs** , cliquez sur **ajouter des approbateurs**.

1. Dans le volet Sélectionner des approbateurs, sélectionnez un ou plusieurs utilisateurs et/ou groupes approbateurs.

    Un seul des approbateurs sélectionnés doit approuver une demande. L’approbation de tous les approbateurs n’est pas nécessaire. La décision d’approbation repose sur quelle que soit l’approbateur examine la demande tout d’abord.

    ![Package d’accès - approbateurs de sélection de stratégie](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Cliquez sur **sélectionnez** pour ajouter des approbateurs.

1. Cliquez sur **afficher les paramètres de requête avancés** pour afficher les paramètres supplémentaires.

    ![Package d’accès - répertoires de la stratégie de sélection](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Pour demander aux utilisateurs de fournir une justification pour demander le package de l’accès, définissez **demander une justification** à **Oui**.

1. Pour exiger que l’approbateur de fournir une justification pour approuver une demande pour le package de l’accès, définissez **demander une justification approbateur** à **Oui**.

1. Dans le **délai d’expiration de la demande d’approbation (jours)** , spécifiez la quantité de temps les approbateurs ont pour passer en revue une demande. Si aucun approbateur n’Examinez-la dans ce nombre de jours, la demande n’expire et l’utilisateur devra soumettre une autre requête pour le package de l’accès.

### <a name="policy-expiration"></a>Stratégie : Expiration

Dans la section d’Expiration, vous spécifiez lorsque attribution d’un utilisateur pour le package d’accès arrive à expiration.

1. Dans le **Expiration** section, définissez **Access package expire** à **sur date**, **nombre de jours**, ou **jamais**.

    Pour **sur date**, sélectionnez une date d’expiration à l’avenir.

    Pour **nombre de jours**, spécifiez un nombre compris entre 0 et 3660 jours.

    Selon votre sélection, attribution d’un utilisateur pour le package d’accès expire à une certaine date, un certain nombre de jours lorsqu’elles sont approuvées, voire jamais.

1. Cliquez sur **afficher les paramètres d’expiration avancés** pour afficher les paramètres supplémentaires.

1. Pour autoriser l’utilisateur d’étendre leurs attributions, définissez **permettre aux utilisateurs d’étendre l’accès** à **Oui**.

    Si les extensions sont autorisées dans la stratégie, l’utilisateur recevra un e-mail 14 jours et également 1 jour avant leur attribution de package d’accès est définie pour expirer les invitant à étendre l’attribution.

    ![Package d’accès - paramètres d’Expiration de stratégie :](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Stratégie : Activer la stratégie

1. Si vous souhaitez que le package de l’accès à rendre immédiatement disponibles pour les utilisateurs dans la stratégie, cliquez sur **Oui** pour activer la stratégie.

    Vous pouvez toujours l’activer ultérieurement une fois que vous avez créé le package de l’accès.

    ![Package d’accès - activer la stratégie de paramètre de stratégie](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Cliquez sur **suivant** ou **créer**.
