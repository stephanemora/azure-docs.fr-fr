---
title: Résolution des problèmes de stratégie d’accès au coffre de clés Azure
description: Résolution des problèmes de stratégie d’accès au coffre de clés Azure
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 270e5ba1879b229fbe9f5e6c8692bd8b4e9eebc7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688638"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Résolution des problèmes de stratégie d’accès au coffre de clés Azure

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Comment faire pour identifier le mode et le moment d’accès aux coffres de clés ?
Une fois que vous avez créé un ou plusieurs coffres de clés, vous voulez probablement contrôler qui accède à ces derniers, par quel moyen et quand. À cette fin, activez la journalisation pour Azure Key Vault. Pour en savoir plus l’activation de la journalisation, consultez ce [guide pas à pas](https://docs.microsoft.com/azure/key-vault/general/logging).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Comment faire pour surveiller la disponibilité du coffre, les périodes de latence du service ou d’autres métriques de performances pour le coffre de clés ?
Dès que vous entamerez la mise à l'échelle votre service, le nombre de requêtes envoyées à votre coffre de clés augmentera. Cela risque d'augmenter la latence de vos requêtes et, dans les cas les plus extrêmes, de les limiter, ce qui aura un impact sur les performances de votre service. Vous pouvez surveiller les métriques de performances du coffre de clés et recevoir des alertes pour des seuils spécifiques. Pour en savoir plus sur la configuration de la surveillance, consultez ce [guide pas à pas](https://docs.microsoft.com/azure/key-vault/general/alert).

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Comment faire pour attribuer le contrôle d’accès par objet du coffre de clés ? 
La disponibilité de la fonctionnalité de contrôle d’accès par secret/clé/certificat sera notifiée ici. [En savoir plus](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Comment faire pour fournir une authentification auprès du coffre de clés à l’aide d’une stratégie de contrôle d’accès ?
Le moyen le plus simple d’authentifier une application cloud auprès de Key Vault consiste à utiliser une identité managée. Pour plus d’informations, consultez [Utiliser une identité managée App Service pour accéder à Azure Key Vault]( https://docs.microsoft.com/azure/key-vault/general/managed-identity).
Si vous créez une application locale, si vous effectuez du développement local ou si vous ne parvenez pas à utiliser une identité managée, vous pouvez à la place inscrire un principal de service manuellement et fournir l’accès au coffre de clés à l’aide d’une stratégie de contrôle d’accès. [En savoir plus](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps).


### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Comment faire pour autoriser le groupe AD à accéder au coffre de clés ?
Accordez les autorisations de groupe AD à votre coffre de clés à l’aide de la commande Azure CLI az keyvault set-policy ou de l’applet de commande Azure PowerShell Set-AzKeyVaultAccessPolicy. Pour obtenir des exemples, consultez la section qui explique comment [permettre à une application, à un groupe Azure AD ou à un utilisateur d’accéder à votre coffre de clés](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps#give-the-principal-access-to-your-key-vault).

L’application nécessite également l’affectation d’au moins un rôle IAM (gestion des identités et des accès) au coffre de clés. Sinon, elle ne pourra pas se connecter en raison de droits insuffisants pour accéder à l’abonnement. Les groupes Azure AD avec des identités managées peuvent nécessiter jusqu’à 8 heures pour actualiser le jeton et devenir effectifs.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Comment faire pour redéployer Key Vault avec un modèle ARM sans supprimer les stratégies d’accès existantes ?
Actuellement, un redéploiement du Key Vault avec ARM supprime toute stratégie d’accès dans Key Vault et la remplace par une stratégie d’accès dans un modèle ARM. Il n’existe pas d’option incrémentielle pour les stratégies d’accès du Key Vault. Pour conserver les stratégies d’accès du Key Vault, vous devez lire les stratégies d’accès existantes dans le Key Vault, puis remplir le modèle ARM avec ces stratégies afin d’éviter les interruptions d’accès.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Procédure de résolution des problèmes recommandée pour les types d’erreurs suivants
* HTTP 401 : Requête non authentifiée – [Étapes de résolution des problèmes](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-401-unauthenticated-request)
* HTTP 403 : Autorisations insuffisantes – [Étapes de résolution des problèmes](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-403-insufficient-permissions)
* HTTP 429 : Nombre de requêtes trop élevé – [Étapes de résolution des problèmes](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-429-too-many-requests)
* Vérifiez si vous êtes autorisé à supprimer l’accès au coffre de clés : [Stratégies d’accès à un coffre de clés](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)
* Si vous rencontrez un problème avec l’authentification auprès du coffre de clés dans le code, utilisez le [SDK d’authentification](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Quelles sont les meilleures pratiques à implémenter lorsque le coffre de clés est limité ?
Suivez les meilleures pratiques décrites [ici](https://docs.microsoft.com/azure/key-vault/general/overview-throttling#how-to-throttle-your-app-in-response-to-service-limits).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment résoudre les erreurs d’authentification de coffre de clés. [Guide de résolution des problèmes liés à Key Vault](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
