---
title: Terminaison SSL avec certificats Azure Key Vault
description: Découvrez comment vous pouvez intégrer Azure Application Gateway Key Vault pour les certificats de serveur qui sont attachés aux écouteurs HTTPS activé.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 18af315c58c838a7237acfbcc32f622a0edbd3b3
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827626"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Terminaison SSL avec certificats Key Vault

[Azure Key Vault](../key-vault/key-vault-whatis.md) est un secret de plateforme gérés stocker que vous pouvez utiliser pour protéger des secrets, clés et certificats SSL. Azure Application Gateway prend en charge l’intégration avec Key Vault (en préversion publique) pour les certificats de serveur qui sont attachés aux écouteurs HTTPS activé. Cette prise en charge est limitée à la référence (SKU) v2 d’Application Gateway.

> [!IMPORTANT]
> Intégration de la passerelle d’Application avec Key Vault est actuellement en version préliminaire publique. Cette préversion est fournie sans contrat de niveau de service (SLA) et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cette version préliminaire publique propose deux modèles pour un arrêt SSL :

- Vous pouvez fournir explicitement des certificats SSL attachés à l’écouteur. Ce modèle est la façon traditionnelle de transmettre des certificats SSL pour la passerelle d’Application pour un arrêt SSL.
- Vous pouvez éventuellement fournir une référence à un certificat Key Vault existant ou un secret lorsque vous créez un écouteur HTTPS activé.

Intégration d’application Gateway avec Key Vault offre de nombreux avantages, notamment :

- Renforcement de la sécurité, étant donné que les certificats SSL ne sont pas gérées directement par l’équipe de développement d’application. Intégration permet une équipe de sécurité distincts pour :
  * Configurer les passerelles d’application.
  * Contrôle passerelle leur cycle de vie.
  * Accorder des autorisations à des passerelles d’application sélectionnée pour accéder aux certificats qui sont stockés dans votre coffre de clés.
- Prise en charge pour l’importation de certificats existants dans votre coffre de clés. Ou utilisez l’API Key Vault pour créer et gérer de nouveaux certificats avec un des partenaires de coffre de clés approuvés.
- Prise en charge du renouvellement automatique des certificats qui sont stockés dans votre coffre de clés.

Application Gateway prend en charge que les certificats validés de logiciels. Module de sécurité matériel (HSM)-certificats validés ne sont pas pris en charge. Une fois la passerelle d’Application est configurée pour utiliser des certificats de Key Vault, ses instances de récupérer le certificat dans Key Vault et les installer localement pour la terminaison SSL. Les instances interrogent également le coffre de clés à des intervalles de 24 heures pour récupérer une version de renouvellement du certificat, si elle existe. Si un certificat mis à jour est trouvé, le certificat SSL associé à l’écouteur HTTPS est automatiquement pivoter.

## <a name="how-integration-works"></a>Comment fonctionne l’intégration

Intégration d’application Gateway avec Key Vault nécessite un processus de configuration de trois étapes :

1. **Créer une identité gérée affectée à l’utilisateur**

   Vous créez ou réutilisez une affectée à l’utilisateur identité gérée existante, lequel Application Gateway utilise pour récupérer des certificats de Key Vault à votre place. Pour plus d’informations, consultez [Identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md). Cette étape crée une nouvelle identité dans le locataire Azure Active Directory. L’identité est approuvée par l’abonnement qui est utilisé pour créer l’identité.

1. **Configurer votre coffre de clés**

   Vous puis importez un certificat existant ou créez un dans votre coffre de clés. Le certificat sera utilisé par les applications qui s’exécutent via la passerelle d’application. Dans cette étape, vous pouvez également utiliser un secret de coffre de clés est stocké sous un sans mot de passe, base codé en base 64 de fichier PFX. Nous vous recommandons d’utiliser un type de certificat en raison de la fonctionnalité de renouvellement automatique est disponible avec les objets de type de certificat dans le coffre de clés. Une fois que vous avez créé un certificat ou une clé secrète, vous définissez des stratégies d’accès dans le coffre de clés pour autoriser l’identité qui auront *obtenir* accès à la clé secrète.

1. **Configurer la passerelle d’application**

   Après avoir terminé les deux étapes précédentes, vous pouvez configurer ou modifier une passerelle d’application existante pour utiliser l’identité gérée affectée à l’utilisateur. Vous pouvez également configurer le certificat SSL de l’écouteur HTTP pour pointer vers l’URI complet du certificat de coffre de clés ou ID de secret.

   ![Certificats de coffre de clés](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Étapes suivantes

[Configurez la terminaison SSL avec certificats Key Vault à l’aide d’Azure PowerShell](configure-keyvault-ps.md)
