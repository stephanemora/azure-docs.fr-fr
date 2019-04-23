---
title: Terminaison SSL avec certificats Key Vault
description: Découvrez comment vous pouvez intégrer passerelle d’application Azure Key Vault pour les certificats de serveur qui sont attachés aux écouteurs de protocole HTTPS est activé.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/19/2019
ms.author: victorh
ms.openlocfilehash: 9ab1a42578081d4e6537f221e3f3a8f4c7babde0
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014175"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Terminaison SSL avec certificats Key Vault

[Azure Key Vault](../key-vault/key-vault-whatis.md) est un magasin des secrets gérés par la plateforme que vous pouvez utiliser pour protéger les secrets, les clés et certificats SSL. Application Gateway prend en charge l’intégration avec Key Vault (en préversion publique) pour les certificats de serveur qui sont attachés aux écouteurs de protocole HTTPS est activé. Cette prise en charge est limitée à la référence (SKU) v2 d’Application Gateway.

> [!IMPORTANT]
> L’intégration d’Application Gateway Key Vault est actuellement en version préliminaire publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il existe deux modèles pour une terminaison SSL avec cette version préliminaire publique :

- Vous pouvez fournir explicitement des certificats SSL attachés à l’écouteur. Il s’agit du modèle traditionnel de transmettre des certificats SSL à Application Gateway pour la terminaison SSL.
- Vous pouvez éventuellement fournir une référence à un certificat de coffre de clés existant ou secret pendant HTTPS activé la création de l’écouteur.

Il existe de nombreux avantages avec l’intégration du coffre de clés, y compris :

- Renforcement de la sécurité dans la mesure où les certificats SSL ne sont pas gérées directement par l’équipe de développement d’application. Intégration à Key Vault permet une équipe de sécurité distincts configurer, contrôler le cycle de vie et accorder l’autorisation appropriée pour sélectionner les passerelles d’Application pour accéder aux certificats stockés dans Key Vault.
- Prend en charge pour importer des certificats existants dans le coffre de clés, ou utilisent des API Key Vault pour créer et gérer de nouveaux certificats avec un des partenaires de coffre de clés approuvés.
- Prise en charge des certificats stockés dans Key Vault renouveler automatiquement.

Application Gateway prend en charge uniquement les certificats logiciels validé. Certificats de module (HSM) validé de sécurité matériel ne sont pas pris en charge. Une fois la passerelle d’Application est configurée pour utiliser des certificats de Key Vault, ses instances de récupérer le certificat dans Key Vault et les installer localement pour la terminaison SSL. Les instances interrogent également régulièrement Key Vault à un intervalle de 24 heures pour récupérer une version de renouvellement du certificat, si elle existe. Si un certificat mis à jour est trouvé, le certificat SSL associé à l’écouteur HTTPS est automatiquement pivoter.

## <a name="how-it-works"></a>Fonctionnement

Intégration avec Key Vault nécessite un processus de configuration de trois étapes :

1. **Créer utilisateur attribué l’identité gérée**

   Vous devez créer ou réutiliser un assignée une identité gérée qui Application Gateway utilise pour récupérer des certificats de Key Vault en votre nom d’utilisateur existant. Pour plus d’informations, consultez [What ' s des identités gérées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md) Cette étape crée une nouvelle identité dans le locataire Azure AD, qui est approuvé par l’abonnement utilisé pour créer l’identité.
1. **Configurer le coffre de clés**

   Vous devez ensuite importer ou créez un nouveau certificat dans Key Vault utilisée par les applications en cours d’exécution via la passerelle d’Application. Un secret de coffre de clés stocké car sans mot de passe codé en PFX fichier en base 64 peut également être utilisée dans cette étape. À l’aide d’un type de certificat est recommandée en raison des capacités de renouvellement automatique disponibles avec les objets de type de certificat dans Key Vault. Une fois un certificat ou la clé secrète est créé, les stratégies d’accès doivent être définis dans le coffre de clés pour autoriser l’identité qui auront *obtenir* accès pour extraire la clé secrète.

1. **Configurer la passerelle d’Application**

   Une fois les deux étapes précédentes terminées, vous pouvez configurer ou modifier une passerelle d’Application existante pour utiliser l’identité gérée d’affectée par l’utilisateur. Vous configurez également certificat SSL de l’écouteur HTTP pour pointer vers du complet URI du coffre de clés certificat ou ID de secret.

![Certificats Key Vault](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Étapes suivantes

[Configurez la terminaison SSL avec certificats de Key Vault à l’aide d’Azure PowerShell](configure-keyvault-ps.md).