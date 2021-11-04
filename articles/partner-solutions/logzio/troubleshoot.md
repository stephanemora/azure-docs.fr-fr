---
title: Résolution des problèmes de logz.io – Solutions de partenaires Azure
description: Découvrez comment résoudre les problèmes liés à l’intégration d’Azure avec Logz.io.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 10/25/2021
ms.author: tomfitz
ms.openlocfilehash: 4322cdfead345aec836760199ce7d6f1f9654c59
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057511"
---
# <a name="troubleshoot-logzio-integration-with-azure"></a>Résolution des problèmes d’intégration de Logz.io avec Azure

Cet article explique comment résoudre les problèmes d’intégration d’Azure avec Logz.io.

## <a name="owner-role-needed-to-create-resource"></a>Rôle de propriétaire nécessaire pour créer une ressource

Pour configurer Logz.io, vous devez attribuer le [Rôle de propriétaire](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) dans l’abonnement Azure. Avant de commencer cette intégration, [vérifiez votre accès](../../role-based-access-control/check-access.md).

## <a name="single-sign-on-errors"></a>Erreurs d’authentification unique

Des erreurs peuvent se produire lors de la configuration de l’authentification unique. Pour plus d’informations sur la configuration de l’authentification unique (SSO), consultez [Configurer l’authentification unique de Logz.IO](setup-sso.md).

### <a name="unable-to-save-single-sign-on-settings"></a>Impossible d’enregistrer les paramètres d’authentification unique

Cette erreur signifie qu’une autre application d’entreprise utilise l’identificateur Security Assertion Markup Language (SAML) Logz.io. Pour trouver l’application en question, sélectionnez **Modifier** dans la section **Configuration SAML de base**.

Pour résoudre ce problème, désactivez l’application d’entreprise qui utilise SAML ou utilisez une autre application d’entreprise pour configurer l’authentification unique SAML avec Logz.io. Assurez-vous que l’application présente les paramètres requis.

### <a name="application-not-shown-in-single-sign-on"></a>Application non affichée dans l’authentification unique

Essayez de rechercher avec l’_ID d’application_. Si aucun résultat ne s’affiche, vérifiez les paramètres SAML de l’application. La grille affiche uniquement les applications existantes dont les paramètres SAML sont corrects. L’**Identificateur** et l’**URL de réponse** doivent être rigoureusement identiques à ceux présentés dans la figure suivante.

Les zones de texte vides vous permettent d’ajouter de nouvelles valeurs pour l’**identificateur** et l’**URL de réponse**.

Utilisez les modèles suivants pour ajouter de nouvelles valeurs :

- **Identificateur** : `urn:auth0:logzio:<Application ID>`
- **URL de réponse** : `https://logzio.auth0.com/login/callback?connection=<Application ID>`

:::image type="content" source="./media/troubleshoot/basic-saml-config.png" alt-text="Paramètre de la configuration SAML de base.":::

## <a name="logs-not-being-sent-to-logzio"></a>Journaux non envoyés à Logz.io

Seules les ressources répertoriées dans les [catégories de journaux de ressources Azure Monitor](../../azure-monitor/essentials/resource-logs-categories.md) envoient des journaux à Logz.io.

Pour vérifier si une ressource envoie des journaux à Logz.io :

1. Accédez au [paramètre de diagnostic Azure ](../../azure-monitor/essentials/diagnostic-settings.md) pour la ressource spécifique.
1. Vérifiez la présence du paramètre de diagnostic Logz.io.

:::image type="content" source="./media/troubleshoot/diagnostics.png" alt-text="Paramètres de diagnostic.":::

## <a name="limit-reached-in-monitored-resources"></a>Limite atteinte dans les ressources surveillées

Azure Monitor Diagnostics prend en charge au maximum cinq paramètres de diagnostic par ressource ou abonnement. Lorsque vous atteignez cette limite, la ressource indique **Limite atteinte** dans **Ressources surveillées**. Vous ne pouvez pas ajouter la surveillance avec Logz.io.

:::image type="content" source="./media/troubleshoot/limit-monitored-resources.png" alt-text="Limite atteinte pour les ressources surveillées.":::

## <a name="vm-extension-installation-failed"></a>Échec de l’installation de l’extension de machine virtuelle

Une machine virtuelle ne peut être surveillée que par un seul compte Logz.io (main ou sub). Si vous tentez d’installer l’agent sur une machine virtuelle déjà surveillée par un autre compte, l’erreur suivante s’affiche :

:::image type="content" source="./media/troubleshoot/vm-agent-fail.png" alt-text="Échec de l’installation de l’agent de machine virtuelle.":::

## <a name="purchase-errors"></a>Erreurs d’achat

L’achat échoue parce qu’aucune carte de crédit valide n’est connectée à l’abonnement Azure. Ou bien aucun mode de paiement n’est associé à l’abonnement.

Pour résoudre une erreur d’achat :

- Utilisez un autre abonnement Azure.
- Ajouter ou mettez à jour la carte de crédit ou le mode de paiement de l’abonnement. Pour plus d’informations, consultez [Ajouter ou mettre à jour une carte de crédit pour Azure](../../cost-management-billing/manage/change-credit-card.md).

Vous pouvez afficher la sortie de l’erreur à partir de la page de déploiement de la ressource en sélectionnant **Détails de l’opération**.

```json
{
  "status": "Failed",
  "error": {
    "code": "BadRequest",
    "message": "{\"message\":\"Purchase has failed because we couldn't find a valid credit card nor
               a payment method associated with your Azure subscription. Please use a different
               Azure subscription or add\\\\update current credit card or payment method for this
               subscription and retry.\",\"code\":\"BadRequest\"}"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer](manage.md) l’intégration de votre Logz.io.
- Pour en savoir plus sur la SSO, consultez [Configurer l’authentification unique de Logz.io](setup-sso.md).
