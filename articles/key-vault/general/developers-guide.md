---
title: Guide du développeur Azure Key Vault
description: Les développeurs peuvent utiliser le coffre de clés Azure pour gérer les clés de chiffrement dans l'environnement Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 4c1b4b6437b14039284fb938405fa7069b0e239c
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667551"
---
# <a name="azure-key-vault-developers-guide"></a>Guide du développeur Azure Key Vault

Azure Key Vault vous permet d’accéder en toute sécurité aux informations sensibles à l’intérieur de vos applications :

- Les clés, secrets et certificats sont protégés sans que vous deviez écrire de code et vous pouvez facilement les utiliser dans vos applications.
- Vous pouvez autoriser les clients à posséder et gérer leurs propres clés, secrets et certificats afin que vous puissiez vous concentrer sur la fourniture des principales fonctionnalités du logiciel. Ainsi, vos applications ne vous exposent à aucune responsabilité en relation avec les clés, secrets et certificats de vos locataires.
- Votre application peut utiliser des clés pour la signature et le chiffrement, tandis que la gestion des clés s’effectue à l’extérieur de votre application. Pour plus d’informations sur les clés, consultez [À propos des clés](../keys/about-keys.md).
- Vous pouvez gérer les informations d’identification comme les mots de passe, les clés d’accès et les jetons SAS en les stockant dans Key Vault sous forme de secrets. Pour plus d’informations, consultez [À propos des secrets](../secrets/about-secrets.md).
- Gérer des certificats. Pour plus d’informations, consultez [À propos des certificats](../certificates/about-certificates.md).

Pour obtenir des informations générales sur Azure Key Vault, consultez [À propos d’Azure Key Vault](overview.md).

## <a name="public-previews"></a>Préversions publiques

Nous publions régulièrement la préversion publique d’une nouvelle fonctionnalité de Key Vault. Essayez les fonctionnalités de préversion publique et envoyez votre avis à azurekeyvault@microsoft.com, notre adresse e-mail dédiée aux commentaires.

## <a name="create-and-manage-key-vaults"></a>Créer et gérer des coffres de clés

Comme pour les autres services Azure, Key Vault est géré par le biais d’[Azure Resource Manager](../../azure-resource-manager/management/overview.md). Azure Resource Manager est le service de déploiement et de gestion d’Azure. Vous pouvez l’utiliser pour créer, mettre à jour et supprimer des ressources dans votre compte Azure. 

Le [contrôle d’accès en fonction du rôle (RBAC) Azure](../../role-based-access-control/overview.md) contrôle l’accès à la couche de gestion, également appelée [plan de gestion](security-features.md#managing-administrative-access-to-key-vault). Le plan de gestion de Key Vault permet de créer et de gérer des coffres de clés et leurs attributs, notamment les stratégies d’accès. Le *plan de données* permet de gérer les clés, les certificats et les secrets. 

Vous pouvez utiliser le rôle de contributeur Key Vault prédéfini pour accorder l’accès de gestion à Key Vault.     

### <a name="apis-and-sdks-for-key-vault-management"></a>API et kits de développement logiciel (SDK) pour la gestion de coffre de clés

| Azure CLI | PowerShell | API REST | Gestionnaire de ressources | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Référence](/cli/azure/keyvault)<br>[Démarrage rapide](quick-create-cli.md)|[Référence](/powershell/module/az.keyvault)<br>[Démarrage rapide](quick-create-powershell.md)|[Référence](/rest/api/keyvault/)|[Référence](/azure/templates/microsoft.keyvault/vaults)<br>[Démarrage rapide](./vault-create-template.md)|[Référence](/dotnet/api/microsoft.azure.management.keyvault)|[Référence](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Référence](/java/api/com.microsoft.azure.management.keyvault)|[Référence](/javascript/api/@azure/arm-keyvault)|

Pour obtenir les packages d’installation et le code source, consultez les [bibliothèques clientes](client-libraries.md).

## <a name="authenticate-to-key-vault-in-code"></a>S’authentifier auprès de Key Vault dans le code

Key Vault utilise l’authentification Azure Active Directory (Azure AD), qui nécessite un principal de sécurité Azure AD pour accorder l’accès. Un principal de sécurité Azure AD peut être un utilisateur, un principal de service d’application, une [identité managée pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md) ou un groupe de l’un de ces types.

### <a name="authentication-best-practices"></a>Bonnes pratiques pour l'authentification

Nous vous recommandons d’utiliser l’identité managée pour les applications déployées sur Azure. Si vous utilisez des services Azure qui ne prennent pas en charge les identités managées ou si les applications sont déployées localement, le [principal de service avec un certificat](../../active-directory/develop/howto-create-service-principal-portal.md) est une alternative possible. Dans ce scénario, le certificat doit être stocké dans Key Vault et faire l’objet d’une rotation fréquente.

Utilisez un principal de service avec un secret pour les environnements de développement et de test. Utilisez un principal d’utilisateur pour le développement local et Azure Cloud Shell.

Nous vous recommandons d’utiliser ces principaux de sécurité dans chaque environnement :
- **Environnement de production** : identité managée ou principal de service avec un certificat.
- **Environnements de test et de développement** : identité managée, principal de service avec certificat ou principal du service avec un secret.
- **Développement local** : principal d’utilisateur ou principal de service avec un secret.

### <a name="azure-identity-client-libraries"></a>Bibliothèques clientes d’identité Azure

Les scénarios d’authentification précédents sont pris en charge par la *bibliothèque cliente d’identité Azure* et intégrés aux kits de développement logiciel (SDK) Key Vault. Vous pouvez utiliser la bibliothèque cliente d’identité Azure dans des environnements et des plateformes sans modifier votre code. La bibliothèque récupère automatiquement les jetons d’authentification des utilisateurs qui sont connectés à l’utilisateur Azure via Azure CLI, Visual Studio, Visual Studio Code et d’autres moyens. 

Pour plus d’informations sur la bibliothèque cliente d’identité Azure, consultez :

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[SDK de l’identité Azure .NET](/dotnet/api/overview/azure/identity-readme)|[SDK de l’identité Azure Python](/python/api/overview/azure/identity-readme)|[SDK de l’identité Azure Java](/java/api/overview/azure/identity-readme)|[SDK de l’identité Azure Javascript](/javascript/api/overview/azure/identity-readme)|     

> [!Note]
> Nous vous recommandons d’utiliser la [bibliothèque d’authentification des applications](/dotnet/api/overview/azure/service-to-service-authentication) pour le Kit de développement logiciel (SDK) Key Vault .NET version 3, mais elle est maintenant déconseillée. Pour migrer vers le Kit de développement logiciel (SDK) Key Vault .NET version 4, suivez l’[aide relative à la migration d’AppAuthentication vers Azure.Identity](/dotnet/api/overview/azure/app-auth-migration).

Pour obtenir des didacticiels sur la façon de s’authentifier auprès de Key Vault dans les applications, consultez :
- [Utiliser Azure Key Vault avec une machine virtuelle dans .NET](./tutorial-net-virtual-machine.md)
- [Utiliser Azure Key Vault avec une machine virtuelle dans Python](./tutorial-python-virtual-machine.md)
- [Utiliser une identité managée pour connecter Key Vault à une application web Azure dans .NET](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Gérer des clés, certificats et secrets

Le plan de données contrôle l’accès aux clés, certificats et secrets. Vous pouvez utiliser les stratégies d’accès au coffre local ou Azure RBAC pour le contrôle d’accès via le plan de données.

### <a name="apis-and-sdks-for-keys"></a>API et kits de développement logiciel (SDK) pour les clés

| Azure CLI | PowerShell | API REST | Gestionnaire de ressources | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Référence](/cli/azure/keyvault/key)<br>[Démarrage rapide](../keys/quick-create-cli.md)|[Référence](/powershell/module/az.keyvault/)<br>[Démarrage rapide](../keys/quick-create-powershell.md)|[Référence](/rest/api/keyvault/#key-operations)|[Référence](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Démarrage rapide](../keys/quick-create-template.md)|[Référence](/dotnet/api/azure.security.keyvault.keys)<br>[Démarrage rapide](../keys/quick-create-net.md)|[Référence](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Démarrage rapide](../keys/quick-create-python.md)|[Référence](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Démarrage rapide](../keys/quick-create-java.md)|[Référence](/javascript/api/@azure/keyvault-keys/)<br>[Démarrage rapide](../keys/quick-create-node.md)|

### <a name="apis-and-sdks-for-certificates"></a>API et kits de développement logiciel (SDK) pour les certificats

| Azure CLI | PowerShell | API REST | Gestionnaire de ressources | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Référence](/cli/azure/keyvault/certificate)<br>[Démarrage rapide](../certificates/quick-create-cli.md)|[Référence](/powershell/module/az.keyvault)<br>[Démarrage rapide](../certificates/quick-create-powershell.md)|[Référence](/rest/api/keyvault/#certificate-operations)|N/A|[Référence](/dotnet/api/azure.security.keyvault.certificates)<br>[Démarrage rapide](../certificates/quick-create-net.md)|[Référence](/python/api/overview/azure/keyvault-certificates-readme)<br>[Démarrage rapide](../certificates/quick-create-python.md)|[Référence](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Démarrage rapide](../certificates/quick-create-java.md)|[Référence](/javascript/api/@azure/keyvault-certificates/)<br>[Démarrage rapide](../certificates/quick-create-node.md)|

### <a name="apis-and-sdks-for-secrets"></a>API et kits de développement logiciel (SDK) pour les secrets

| Azure CLI | PowerShell | API REST | Gestionnaire de ressources | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Référence](/cli/azure/keyvault/secret)<br>[Démarrage rapide](../secrets/quick-create-cli.md)|[Référence](/powershell/module/az.keyvault/)<br>[Démarrage rapide](../secrets/quick-create-powershell.md)|[Référence](/rest/api/keyvault/#secret-operations)|[Référence](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Démarrage rapide](../secrets/quick-create-template.md)|[Référence](/dotnet/api/azure.security.keyvault.secrets)<br>[Démarrage rapide](../secrets/quick-create-net.md)|[Référence](/python/api/overview/azure/keyvault-secrets-readme)<br>[Démarrage rapide](../secrets/quick-create-python.md)|[Référence](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Démarrage rapide](../secrets/quick-create-java.md)|[Référence](/javascript/api/@azure/keyvault-secrets/)<br>[Démarrage rapide](../secrets/quick-create-node.md)|

### <a name="usage-of-secrets"></a>Utilisation des secrets
Azure Key Vault permet de ne stocker que des secrets pour votre application. Exemples de secrets qui doivent être stockés dans Key Vault :

- Secrets des applications clientes
- Chaînes de connexion
- Mots de passe
- Clés d’accès partagé
- Clés SSH

Toutes les informations liées aux secrets, telles que les noms d’utilisateur et les ID d’application, peuvent être stockées sous la forme d’une étiquette dans un secret. Pour tous les autres paramètres de configuration sensibles, vous devez utiliser [Azure App Configuration](../../azure-app-configuration/overview.md).
 
### <a name="references"></a>References 

Pour obtenir les packages d’installation et le code source, consultez les [bibliothèques clientes](client-libraries.md).

Pour obtenir des informations sur la sécurité du plan de données pour Key Vault, consultez [Fonctionnalités de sécurité d'Azure Key Vault](security-features.md).

## <a name="use-key-vault-in-applications"></a>Utiliser Key Vault dans les applications

Pour tirer parti des fonctionnalités les plus récentes de Key Vault, nous vous recommandons d’utiliser les kits de développement logiciel (SDK) Key Vault disponibles pour l’utilisation de secrets, de certificats et de clés dans votre application. Les kits de développement logiciel (SDK) Key Vault et l’API REST sont mis à jour, car de nouvelles fonctionnalités ont été publiées pour le produit et suivent nos bonnes pratiques et recommandations.

Pour les scénarios de base, il existe d’autres bibliothèques et solutions d’intégration pour un usage simplifié avec la prise en charge fournie par les partenaires de Microsoft ou les communautés open source.

Pour les certificats, vous pouvez utiliser :

- L’extension de machine virtuelle Key Vault, qui assure l’actualisation automatique des certificats stockés dans un coffre de clés Azure. Pour plus d'informations, consultez les pages suivantes : 
  - [Extension de machine virtuelle Key Vault pour Windows](../../virtual-machines/extensions/key-vault-windows.md)
  - [Extension de machine virtuelle Key Vault pour Linux](../../virtual-machines/extensions/key-vault-linux.md)
  - [Extension de machine virtuelle Key Vault pour les serveurs avec Azure Arc](../../azure-arc/servers/manage-vm-extensions.md#azure-key-vault-vm-extension)
- L’intégration d’Azure App Service, qui peut importer et actualiser automatiquement des certificats à partir de Key Vault. Pour plus d’informations, consultez [Importer un certificat à partir de Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault).

Pour les secrets, vous pouvez utiliser :

- Des secrets Key Vault avec les paramètres d’application App Service. Pour en savoir plus, consultez la section [Utiliser des références Key Vault pour App Service et Azure Functions](../../app-service/app-service-key-vault-references.md).
- Des secrets Key Vault avec le service App Configuration pour les applications hébergées dans une machine virtuelle Azure. Pour plus d’informations, consultez [Configurer des applications avec App Configuration et Key Vault](/samples/azure/azure-sdk-for-net/app-secrets-configuration/).

## <a name="code-examples"></a>Exemples de code

Pour obtenir des exemples complets d’utilisation de Key Vault avec des applications, consultez [Exemples de code Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault). 

## <a name="task-specific-guidance"></a>Aide spécifique relative aux tâches

Les articles et scénarios suivants fournissent des conseils spécifiques sur l’utilisation d’Azure Key Vault :

- Pour accéder à un coffre de clés, votre application cliente doit être en mesure d’accéder à plusieurs points de terminaison pour différentes fonctionnalités. Consultez [Accéder à Key Vault derrière un pare-feu](access-behind-firewall.md). 
- Une application cloud exécuté sur une machine virtuelle Azure a besoin d’un certificat. Comment obtenir ce certificat sur cette machine virtuelle ? Consultez [Extension de machine virtuelle Key Vault pour Windows](../../virtual-machines/extensions/key-vault-windows.md) et [Extension de machine virtuelle Key Vault pour Linux](../../virtual-machines/extensions/key-vault-linux.md).
- Pour affecter une stratégie d’accès à l’aide d’Azure CLI, de PowerShell ou du portail Azure, consultez [Affecter une stratégie d’accès Key Vault](assign-access-policy.md). 
- Pour obtenir de l’aide sur l’utilisation et le cycle de vie d’un coffre de clés et de divers objets de coffre de clés avec la suppression réversible activée, consultez [Gestion de la récupération Azure Key Vault avec la suppression réversible et la protection contre la suppression définitive](./key-vault-recovery.md).
- Lorsque vous devez transmettre une valeur sécurisée (par exemple, un mot de passe) en tant que paramètre au cours du déploiement, vous pouvez stocker cette valeur en tant que clé secrète dans un coffre de clés et faire référence à la valeur dans d'autres modèles Resource Manager. Consultez [Utiliser Azure Key Vault pour transmettre des valeurs de paramètre sécurisées pendant le déploiement](../../azure-resource-manager/templates/key-vault-parameter.md).

## <a name="integration-with-key-vault"></a>Intégration à Key Vault

Les services et scénarios suivants utilisent ou s’intègrent à Key Vault :

- Le [chiffrement au repos](../../security/fundamentals/encryption-atrest.md) permet l’encodage (chiffrement) des données quand elles sont stockées. Les clés de chiffrement de données sont souvent chiffrées avec une clé de chiffrement de clé dans Azure Key Vault pour limiter davantage l’accès.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) vous permet de gérer votre propre clé de locataire. Par exemple, plutôt que Microsoft gère votre clé de client (par défaut), vous pouvez gérer votre propre clé pour vous conformer aux réglementations spécifiques à votre organisation. La gestion de votre propre clé de locataire est également appelée *BYOK* (Bring Your Own Key).
- [Azure Private Link](private-link-service.md) vous permet d’accéder aux services Azure (par exemple, Azure Key Vault, Stockage Azure et Azure Cosmos DB) ainsi qu’aux services de partenaires ou de clients hébergés par Azure via un point de terminaison privé de votre réseau virtuel.
- L’intégration entre Key Vault et [Azure Event Grid](../../event-grid/event-schema-key-vault.md) permet aux utilisateurs d’être avertis en cas de modification de l’état d’un secret stocké dans Key Vault. Vous pouvez distribuer de nouvelles versions de secrets aux applications ou appliquer une rotation aux secrets proches de l’expiration pour éviter les pannes.
- Protégez vos secrets [Azure DevOps](/azure/devops/pipelines/release/azure-key-vault) contre tout accès indésirable dans Key Vault.
- Utilisez le secret stocké dans Key Vault pour la [connexion au stockage Azure à partir d’Azure DataBricks](./integrate-databricks-blob-storage.md).
- Configurez et exécutez le fournisseur Azure Key Vault pour le [pilote CSI du magasin des secrets](./key-vault-integrate-kubernetes.md) sur Kubernetes. 

## <a name="key-vault-overviews-and-concepts"></a>Concepts et présentations des coffres de clés

Pour en savoir plus sur :

- Une fonctionnalité qui permet la récupération des objets supprimés, que la suppression soit accidentelle ou intentionnelle, consultez [Vue d’ensemble de la suppression réversible d’Azure Key Vault](soft-delete-overview.md).
- Les concepts de base relatifs à la limitation et à l’approche de votre application, consultez [Aide sur la limitation d’Azure Key Vault](overview-throttling.md).
- Les relations entre les régions et les zones de sécurité, consultez [Les mondes de la sécurité et les limites géographiques d’Azure Key Vault](overview-security-worlds.md).

## <a name="social"></a>Réseaux sociaux

- [Blog de Key Vault](/archive/blogs/kv/)
- [Microsoft Q&A](https://docs.microsoft.com/answers/products/)
- [Stack Overflow pour Key Vault](https://stackoverflow.com/questions/tagged/azure-keyvault)
