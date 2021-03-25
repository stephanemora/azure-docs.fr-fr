---
title: Fichier include
description: Fichier Include
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: db13884e3deed8e990d21d82e215a1e837371275
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98052825"
---
## <a name="lifecycle"></a>Cycle de vie

Dans l’onglet **Cycle de vie**, vous spécifiez l’expiration de l’attribution du package d’accès à un utilisateur. Vous pouvez également spécifier si les utilisateurs peuvent étendre leurs attributions.

1. Dans la section **Expiration**, définissez **L’affectation de package d’accès expire** sur **Date**, **Nombre de jours** ou **Jamais**.

    Pour **Date**, sélectionnez une date d’expiration.

    Pour **Nombre de jours**, spécifiez un nombre de jours compris entre 0 et 3 660.

    Selon votre sélection, l’attribution du package d’accès à un utilisateur expire à une date définie, un nombre de jours définis après approbation ou n’expire jamais.

1. Cliquez sur **Afficher les paramètres avancés d’expiration** pour afficher plus de paramètres.

    ![Package d’accès - Paramètres d’expiration du cycle de vie](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Pour autoriser un utilisateur à étendre son attribution, définissez **Autoriser un utilisateur à étendre son accès** sur **Oui**.

    Si les extensions sont autorisées dans la stratégie, l’utilisateur recevra un e-mail 14 jours ainsi qu’un jour avant l’expiration de son attribution l’invitant à étendre l’attribution. L’utilisateur doit toujours se trouver dans l’étendue de la stratégie au moment où il demande une prolongation. En outre, si la stratégie a une date de fin explicite pour les attributions et qu’un utilisateur envoie une demande de prolongation de l’accès, la date de prolongation de la demande doit être au plus tard la date d’expiration des attributions, comme défini dans la stratégie utilisée pour accorder à l’utilisateur l’accès au package d’accès. Par exemple, si la stratégie indique que les affectations sont configurées pour expirer le 30 juin, l’extension maximale qu’un utilisateur peut demander est le 30 juin.

    Si l’accès d’un utilisateur est étendu, il ne pourra pas demander le package d’accès après la date d’extension spécifiée (date définie dans le fuseau horaire de l’utilisateur qui a créé la stratégie).

1. Pour demander une approbation pour accorder une extension, définissez **Exiger une approbation pour accorder l'extension** sur **Oui**.

    Les mêmes paramètres d’approbation que ceux spécifiés sous l’onglet **Requêtes** seront utilisés.

1. Cliquez sur **Suivant** ou **Mettre à jour**.
