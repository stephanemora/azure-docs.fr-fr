---
title: Arrêt TLS avec certificats Azure Key Vault
description: Découvrez comment intégrer Azure Application Gateway avec Key Vault pour les certificats de serveur associés à des écouteurs HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 55ad3c97fd70db854faf3d2181a55630d04f5e41
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215089"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Arrêt TLS avec certificats Key Vault

[Azure Key Vault](../key-vault/general/overview.md) est un magasin des secrets managé par une plateforme que vous pouvez utiliser pour protéger des secrets, des clés et des certificats TLS/SSL. Azure Application Gateway prend en charge l’intégration dans Key Vault des certificats de serveur associés à des écouteurs HTTPS. Cette prise en charge se limite à la référence SKU v2 d’Application Gateway.

L'intégration de Key Vault propose deux modèles d'arrêt TLS :

- Fournissez explicitement des certificats TLS/SSL associés à l'écouteur. Ce modèle constitue le moyen traditionnel de transmettre des certificats TLS/SSL à Application Gateway pour un arrêt TLS.
- Vous pouvez également fournir une référence à un certificat Key Vault existant ou un secret lorsque vous créez un écouteur HTTPS.

L’intégration d’Application Gateway avec Key Vault offre de nombreux avantages, notamment :

- Une sécurité renforcée, dans la mesure où les certificats TLS/SSL ne sont pas directement gérés par l'équipe de développement d'applications. L’intégration permet à une équipe de sécurité distincte de :
  * Configurer des passerelles d’application.
  * Contrôler les cycles de vie des passerelles d’application.
  * Accorder des autorisations à certaines passerelles d’application pour accéder aux certificats stockés dans votre coffre de clés.
- Prendre en charge l’importation de certificats existants dans votre coffre de clés. Ou utiliser les API Key Vault pour créer et gérer de nouveaux certificats avec un partenaire Key Vault approuvé.
- Prise en charge du renouvellement automatique des certificats stockés dans votre coffre de clés.

## <a name="supported-certificates"></a>Certificats pris en charge

À l’heure actuelle, Application Gateway prend uniquement en charge les certificats validés par logiciel. Les certificats validés par module de sécurité matériel ne sont pas pris en charge. 

Une fois qu'Application Gateway est configurée pour utiliser des certificats Key Vault, ses instances récupèrent le certificat dans Key Vault et l'installent localement pour l'arrêt TLS. Les instances interrogent également Key Vault à des intervalles de quatre heures pour récupérer une version renouvelée du certificat, le cas échéant. Si un certificat mis à jour est trouvé, le certificat TLS/SSL associé à l'écouteur HTTPS est automatiquement remplacé. 

Dans Key Vault, Application Gateway utilise un identificateur de secret pour référencer les certificats. Pour Azure PowerShell, Azure CLI ou Azure Resource Manager, nous vous recommandons vivement d’utiliser un identificateur de secret qui ne spécifie pas de version. Ainsi, Application Gateway effectue automatiquement une rotation du certificat, si une version plus récente est disponible dans votre coffre de clés. Voici un exemple d’URI de secret sans version : `https://myvault.vault.azure.net/secrets/mysecret/`.

Le portail Azure prend uniquement en charge les certificats Key Vault, et non les secrets. Application Gateway continue de prendre en charge le référencement des secrets à partir de Key Vault, mais uniquement via des ressources autres que le portail, telles que PowerShell, Azure CLI, les API et les modèles Azure Resource Manager (modèles ARM). 

> [!WARNING]
> Azure Application Gateway ne prend actuellement en charge que les comptes Key Vault dans le même abonnement que la ressource Application Gateway. Le choix d’un coffre de clés dans un autre abonnement que votre passerelle Application Gateway entraîne un échec.

## <a name="certificate-settings-in-key-vault"></a>Paramètres de certificat dans Key Vault

Pour la terminaison TLS, Application Gateway prend en charge les certificats au format PFX (Personal Information Exchange). Vous pouvez importer un certificat existant ou en créer un dans votre coffre de clés. Pour éviter tout échec, assurez-vous que l’état du certificat est défini sur **Activé** dans Key Vault.

## <a name="how-integration-works"></a>Fonctionnement de l’intégration

L’intégration d’Application Gateway avec Key Vault fait appel à un processus de configuration en trois étapes :

![Diagramme illustrant les trois étapes d’intégration d’Application Gateway avec Key Vault.](media/key-vault-certs/ag-kv.png)

### <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur

Vous pouvez créer une identité managée affectée par l’utilisateur existante ou en réutiliser une. Application Gateway utilise l’identité managée pour récupérer des certificats depuis Key Vault en votre nom. Pour plus d’informations, consultez [Créer, répertorier, supprimer ou affecter un rôle à une identité managée affectée par l’utilisateur à l’aide du portail Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

Cette étape crée une identité dans le tenant Azure Active Directory. L’identité est approuvée par l’abonnement utilisé pour créer l’identité.

### <a name="configure-your-key-vault"></a>Configurer votre coffre de clés

Définissez des stratégies d’accès pour utiliser l’identité managée affectée par l’utilisateur à votre coffre de clés :
    
1. Dans le portail Azure, accédez à **Key Vault**.
1. Ouvrez le volet **Stratégies d’accès**.
1. Si vous utilisez le modèle d’autorisation **Stratégie d’accès à Key Vault** : sélectionnez **+ Ajouter une stratégie d’accès**, sélectionnez **Obtenir** pour **Autorisations de secret**, puis choisissez votre identité managée affectée par l’utilisateur pour **Sélectionner le principal**. Ensuite, sélectionnez **Enregistrer**.
   
   Si vous utilisez le modèle d’autorisation **Contrôle d’accès en fonction du rôle Azure** : ajoutez une attribution de rôle pour l’identité managée affectée par l’utilisateur à Azure Key Vault pour le rôle **Utilisateur des secrets Key Vault**.

Depuis le 15 mars 2021, Key Vault reconnaît Application Gateway en tant que service approuvé en tirant parti des identités managées par l’utilisateur à des fins d’authentification auprès d’Azure Key Vault.  L’utilisation de points de terminaison de service et l’activation de l’option de services approuvés pour le pare-feu du coffre de clés vous permettent de créer une limite réseau sécurisée dans Azure. Vous pouvez refuser au trafic en provenance de tous les réseaux (y compris le trafic Internet) l’accès à Key Vault tout en le rendant accessible pour une ressource Application Gateway dans votre abonnement.

Lorsque vous utilisez un coffre de clés restreint, procédez comme suit pour configurer Application Gateway de manière à utiliser des pare-feu et des réseaux virtuels : 

1. Dans le portail Azure, dans votre coffre de clés, sélectionnez **Mise en réseau**.
1. Sous l’onglet **Pare-feu et réseaux virtuels**, sélectionnez **Réseaux et points de terminaison privés sélectionnés**.
1. Pour **Réseaux virtuels**, sélectionnez **+ Ajouter des réseaux virtuels existants**, puis ajoutez le réseau virtuel et le sous-réseau correspondant à votre instance Application Gateway. Lors du processus, configurez également le point de terminaison de service `Microsoft.KeyVault` en activant sa case à cocher.
1. Sélectionnez **Oui** pour autoriser les services approuvés à contourner le pare-feu du coffre de clés.
  
![Capture d’écran montrant les sélections permettant de configurer Application Gateway de manière à utiliser des pare-feu et des réseaux virtuels.](media/key-vault-certs/key-vault-firewall.png)

Si vous déployez l’instance Application Gateway à l’aide d’un modèle ARM, que ce soit en utilisant Azure CLI ou PowerShell, ou via une application Azure déployée à partir du portail Azure, le certificat SSL est stocké dans le coffre de clés en tant que fichier PFX codé en Base64. Vous devez effectuer les étapes décrites dans [Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement](../azure-resource-manager/templates/key-vault-parameter.md). 

Il est particulièrement important de définir `enabledForTemplateDeployment` sur `true`. Le certificat peut avoir ou non un mot de passe. Dans le cas d’un certificat avec mot de passe, l’exemple suivant montre une configuration possible pour l’entrée `sslCertificates` dans `properties` de la configuration du modèle ARM pour Application Gateway. 

```
"sslCertificates": [
    {
        "name": "appGwSslCertificate",
        "properties": {
            "data": "[parameters('appGatewaySSLCertificateData')]",
            "password": "[parameters('appGatewaySSLCertificatePassword')]"
        }
    }
]
```

Les valeurs de `appGatewaySSLCertificateData` et `appGatewaySSLCertificatePassword` sont recherchées à partir du coffre de clés, comme décrit dans [Référencement de secrets avec un ID dynamique](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id). Suivez les références en remontant à partir de `parameters('secretName')` pour voir comment la recherche se produit. Si le certificat est sans mot de passe, omettez l’entrée `password`.

### <a name="configure-application-gateway"></a>Configurer Application Gateway

Après avoir créé une identité managée affectée par l’utilisateur et configuré votre coffre de clés, vous pouvez affecter l’identité managée pour votre instance Application Gateway via la gestion des identités et des accès (IAM). Pour PowerShell, consultez [Set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity).

## <a name="investigating-and-resolving-key-vault-errors"></a>Examen et résolution des erreurs Key Vault

Azure Application Gateway ne se contente pas d’interroger la version du certificat renouvelé sur Key Vault toutes les quatre heures. Il journalise également les erreurs et est intégré à Azure Advisor pour faire apparaître toute configuration incorrecte sous forme de recommandation. La recommandation contient les détails relatifs au problème, ainsi que la ressource Key Vault associée. Vous pouvez utiliser ces informations, de même que le [Guide de résolution des problèmes](../application-gateway/application-gateway-key-vault-common-errors.md), pour résoudre rapidement une telle erreur de configuration. 

Nous vous recommandons vivement de [configurer les alertes Advisor](../advisor/advisor-alerts-portal.md) pour qu’elles restent à jour lorsqu’un problème est détecté. Pour définir une alerte pour ce cas spécifique, utilisez **Résoudre les problèmes liés à Azure Key Vault pour votre instance Application Gateway** comme type de recommandation. 

## <a name="next-steps"></a>Étapes suivantes

[Configurer l'arrêt TLS avec des certificats Key Vault à l'aide d'Azure PowerShell](configure-keyvault-ps.md)
