---
title: Configurer des en-têtes de sécurité avec un ensemble de règles Azure Front Door Standard/Premium (préversion)
description: Cet article fournit des conseils sur l’utilisation d’un ensemble de règles pour configurer des en-têtes de sécurité.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c08ba57f43969bb2f0ee9c66b6cb4e92879ed258
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101097693"
---
# <a name="configure-security-headers-with-azure-front-door-standardpremium-preview-rule-set"></a>Configurer des en-têtes de sécurité avec un ensemble de règles Azure Front Door Standard/Premium (préversion)

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Cet article montre comment implémenter des en-têtes de sécurité pour empêcher les vulnérabilités des navigateurs, comme HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy ou X-Frame-Options. Les attributs basés sur la sécurité peuvent également être définis avec des cookies.

L’exemple suivant vous montre comment ajouter un en-tête Content-Security-Policy à toutes les demandes entrantes qui correspondent au chemin dans la route. Ici, nous autorisons uniquement les scripts de notre site de confiance, **https://apiphany.portal.azure-api.net** , à s’exécuter sur notre application.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

* Avant de pouvoir configurer les en-têtes de sécurité, vous devez créer une instance Front Door. Pour plus d’informations, consultez [Démarrage rapide : Créer une porte d’entrée](create-front-door-portal.md)
* Passez en revue la [configuration d’un ensemble de règles](how-to-configure-rule-set.md) si vous n’avez pas encore utilisé la fonctionnalité Ensemble de règles.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Ajouter un en-tête Content-Security-Policy dans le portail Azure

1. Accédez au profil Azure Front Door Standard/Premium, puis sélectionnez **Ensemble de règles** sous **Paramètres**.

1. Sélectionnez **Ajouter** pour ajouter un nouvel ensemble de règles. Attribuez un **Nom** à l’ensemble de règles, puis un **Nom** à la règle. Sélectionnez **Ajouter une action**, puis sélectionnez **En-tête de réponse**.

1. Pour l’opérateur, choisissez **Ajouter** afin d’ajouter cet en-tête en tant que réponse à toutes les demandes entrantes pour cette route.

1. Ajoutez le nom de l’en-tête : **Content-Security-Policy** et définissez les valeurs que cet en-tête doit accepter. Dans ce scénario, nous choisissons *« script-src 'self' https://apiphany.portal.azure-api.net  »* .

1. Après avoir ajouté toutes les règles que vous souhaitez pour votre configuration, n’oubliez pas d’associer l’ensemble de règles à une route. Cette étape est *nécessaire* pour permettre à l’ensemble de règles d’agir. 

> [!NOTE]
> Dans ce scénario, nous n’avons pas ajouté de [conditions de correspondance](concept-rule-set-match-conditions.md) à la règle. Cette règle est appliquée à toutes les demandes entrantes qui correspondent au chemin défini dans la route associée. Si vous voulez qu’elle s’applique seulement à un sous-ensemble de ces demandes, veillez à ajouter vos **conditions de correspondance** spécifiques à cette règle.

## <a name="clean-up-resources"></a>Nettoyer les ressources

### <a name="deleting-a-rule"></a>Suppression d’une règle

Dans les étapes précédentes, vous avez configuré l’en-tête Content-Security-Policy avec un ensemble de règles. Si vous n’avez plus besoin d’une règle, vous pouvez sélectionner le nom de l’ensemble de règles, puis sélectionner Supprimer la règle. 

### <a name="deleting-a-rule-set"></a>Suppression d’un ensemble de règles

Si vous souhaitez supprimer un ensemble de règles, veillez à le dissocier de toutes les routes au préalable. Pour obtenir des instructions détaillées sur la suppression d’un ensemble de règles, consultez [Configurer votre ensemble de règles](how-to-configure-rule-set.md).

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment configurer un pare-feu d’applications web pour votre instance Front Door, consultez [Web Application Firewall et Front Door](../../web-application-firewall/afds/afds-overview.md).
