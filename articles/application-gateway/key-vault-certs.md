---
title: Arrêt SSL avec certificats Azure Key Vault
description: Découvrez comment intégrer Azure Application Gateway avec Key Vault pour les certificats de serveur associés à des écouteurs HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 5633dd7b72f4de22cd34b7d093e8ec4d9cb411f1
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137698"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Arrêt SSL avec certificats Key Vault

[Azure Key Vault](../key-vault/key-vault-overview.md) est un magasin des secrets managé par une plateforme, que vous pouvez utiliser pour protéger des secrets, des clés et des certificats SSL. Azure Application Gateway prend en charge l’intégration dans Key Vault des certificats de serveur associés à des écouteurs HTTPS. Cette prise en charge se limite à la référence SKU v2 d’Application Gateway.

L’intégration de Key Vault propose deux modèles d’arrêt SSL :

- Vous pouvez fournir explicitement des certificats SSL associés à l’écouteur. Ce modèle constitue la façon traditionnelle de transmettre des certificats SSL à Application Gateway pour un arrêt SSL.
- Vous pouvez éventuellement fournir une référence à un certificat Key Vault existant ou un secret lorsque vous créez un écouteur HTTPS.

L’intégration d’Application Gateway avec Key Vault offre de nombreux avantages, notamment :

- Une sécurité renforcée, étant donné que les certificats SSL ne sont pas gérés directement par l’équipe de développement d’application. L’intégration permet à une équipe de sécurité distincte de :
  * Configurer des passerelles d’application.
  * Contrôler les cycles de vie des passerelles d’application.
  * Accorder des autorisations à certaines passerelles d’application pour accéder aux certificats stockés dans votre coffre de clés.
- Prendre en charge l’importation de certificats existants dans votre coffre de clés. Ou utiliser les API Key Vault pour créer et gérer de nouveaux certificats avec un partenaire Key Vault approuvé.
- Prise en charge du renouvellement automatique des certificats stockés dans votre coffre de clés.

À l’heure actuelle, Application Gateway prend uniquement en charge les certificats validés par logiciel. Les certificats validés par module de sécurité matériel ne sont pas pris en charge. Une fois qu’Application Gateway est configurée pour utiliser des certificats Key Vault, ses instances récupèrent le certificat dans Key Vault et l’installent localement pour l’arrêt SSL. Les instances interrogent également Key Vault à des intervalles de 24 heures pour récupérer une version renouvelée du certificat, le cas échéant. Si un certificat mis à jour est trouvé, le certificat SSL associé à l’écouteur HTTPS est automatiquement remplacé.

> [!NOTE]
> Le portail Azure prend en charge seulement les certificats KeyVault Certificate, pas les secrets. Application Gateway prend néanmoins toujours en charge le référencement des secrets KeyVault, mais seulement via des ressources autres que celles du portail, comme PowerShell, l’interface CLI, les API, les modèles ARM, etc. 

## <a name="how-integration-works"></a>Fonctionnement de l’intégration

L’intégration d’Application Gateway avec Key Vault nécessite un processus de configuration en trois étapes :

1. **Créer une identité managée attribuée par l’utilisateur**

   Vous créez ou réutilisez une identité managée existante affectée à l’utilisateur, qu’Application Gateway utilise pour récupérer les certificats de Key Vault à votre place. Pour plus d’informations, consultez [Identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md). Cette étape crée une identité dans le tenant Azure Active Directory. L’identité est approuvée par l’abonnement utilisé pour créer l’identité.

1. **Configurer votre coffre de clés**

   Vous importez ensuite un certificat existant ou vous en créez un dans votre coffre de clés. Ce certificat sera utilisé par les applications qui s’exécutent au moyen de la passerelle d’application. Dans cette étape, vous pouvez également utiliser un secret de coffre de clés, qui est stocké dans un fichier PFX en base 64 et sans mot de passe. Nous vous recommandons d’utiliser un type de certificat en raison de la fonctionnalité de renouvellement automatique disponible avec les objets de type de certificat dans le coffre de clés. Une fois que vous avez créé un certificat ou un secret, vous définissez des stratégies d’accès dans le coffre de clés pour autoriser l’identité qui disposera d’un accès *get* au secret.

1. **Configurer la passerelle d’application**

   Après avoir terminé les deux étapes précédentes, vous pouvez configurer ou modifier une passerelle d’application existante pour utiliser l’identité managée affectée à l’utilisateur. Vous pouvez également configurer le certificat SSL de l’écouteur HTTP pour pointer vers l’URI complet du certificat ou l’ID de secret Key Vault.

   ![Certificats Key Vault](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Étapes suivantes

[Configurer l’arrêt SSL avec les certificats Key Vault à l’aide d’Azure PowerShell](configure-keyvault-ps.md)
