---
title: Guide du développeur de coffre de clés Azure
description: Les développeurs peuvent utiliser le coffre de clés Azure pour gérer les clés de chiffrement dans l'environnement Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 275013b82866b7cb49488edfc0e63e71a04e364a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670345"
---
# <a name="azure-key-vault-developers-guide"></a>Guide du développeur de coffre de clés Azure

Key Vault vous permet d’accéder en toute sécurité aux informations sensibles à l’intérieur de vos applications :

- Les clés, secrets et certificats sont protégés sans que vous deviez écrire de code et vous pouvez facilement les utiliser dans vos applications.
- Vous pouvez autoriser les clients à posséder et gérer leurs propres clés, secrets et certificats afin que vous puissiez vous concentrer sur la fourniture des principales fonctionnalités du logiciel. Ainsi, vos applications ne vous exposent à aucune responsabilité en relation avec les clés, secrets et certificats de vos clients.
- Votre application peut utiliser des clés pour la signature et le chiffrement, tandis que la gestion des clés s’effectue à l’extérieur de votre application. Pour plus d'informations sur les clés, consultez [À propos des clés](../keys/about-keys.md)
- Vous pouvez gérer les informations d’identification comme les mots de passe, les clés d’accès et les jetons SAS en les stockant dans Key Vault sous forme de secrets, consultez [À propos des secrets](../secrets/about-secrets.md)
- Gérer des certificats. Pour plus d’informations, consultez [À propos des certificats](../certificates/about-certificates.md)

Pour des informations plus générales sur le coffre de clés Azure Key Vault, voir [Qu’est-ce qu’Azure Key Vault ?](overview.md).

## <a name="public-previews"></a>Préversions publiques

Nous publions régulièrement la préversion publique d’une nouvelle fonctionnalité de Key Vault. Essayez les fonctionnalités de préversion publique et envoyez votre avis à azurekeyvault@microsoft.com, notre adresse e-mail dédiée aux commentaires.

## <a name="creating-and-managing-key-vaults"></a>Création et gestion des coffres de clés

La gestion des Key Vault, comme d’autres services Azure, s’effectue via le service Azure Resource Manager. Azure Resource Manager est le service de déploiement et de gestion d’Azure. Il fournit une couche de gestion qui vous permet de créer, de mettre à jour et de supprimer des ressources dans votre compte Azure. Pour plus d’informations, consultez [Azure Resource Manager](../../azure-resource-manager/management/overview.md)

L’accès à la couche de gestion est contrôlé par le [contrôle d’accès en fonction du rôle Azure](../../role-based-access-control/overview.md). Dans Key Vault, la gestion de couche, connue également sous le nom de plan de gestion ou plan de contrôle, vous permet de créer et de gérer Key Vault et ses attributs qui incluent des stratégies d’accès mais pas des clés, secrets ni certificats qui sont gérés sur le plan de données. Vous pouvez utiliser un rôle `Key Vault Contributor` prédéfini pour accorder l’accès de gestion à Key Vault.     

**API et kits de développement logiciel (SDK) pour la gestion de coffre de clés :**

| Azure CLI | PowerShell | API REST | Gestionnaire de ressources | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Référence](/cli/azure/keyvault)<br>[Démarrage rapide](quick-create-cli.md)|[Référence](/powershell/module/az.keyvault)<br>[Démarrage rapide](quick-create-powershell.md)|[Référence](/rest/api/keyvault/)|[Référence](/azure/templates/microsoft.keyvault/vaults)<br>[Démarrage rapide](./vault-create-template.md)|[Référence](/dotnet/api/microsoft.azure.management.keyvault)|[Référence](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Référence](/java/api/com.microsoft.azure.management.keyvault)|[Référence](/javascript/api/@azure/arm-keyvault)|

Consultez les [bibliothèques clientes](client-libraries.md) pour obtenir les packages d’installation et le code source.

Pour plus d'informations sur le plan de gestion de Key Vault, consultez [Fonctionnalités de sécurité d'Azure Key Vault](security-features.md).

## <a name="authenticate-to-key-vault-in-code"></a>S’authentifier auprès de Key Vault dans le code

Key Vault utilise l’authentification Azure AD qui nécessite que le principal de sécurité Azure AD accorde l’accès. Un principal de sécurité Azure AD peut correspondre à un utilisateur, à un principal de service d’application ou à [une identité managée](../../active-directory/managed-identities-azure-resources/overview.md) ou à un groupe de tout type de principaux de sécurité.

### <a name="authentication-best-practices"></a>Bonnes pratiques pour l'authentification

Il est recommandé d’utiliser l’identité gérée pour les applications déployées sur Azure. Si vous utilisez des services Azure qui ne prennent pas en charge l’identité gérée ou si les applications sont déployées localement, le [principal de service avec un certificat](../../active-directory/develop/howto-create-service-principal-portal.md) est une alternative possible. Dans ce scénario, le certificat doit être stocké dans Key Vault et faire l’objet d’une rotation fréquente. Le principal de service avec secret peut être utilisé pour les environnements de développement et de test, et l’utilisation locale ou dans Cloud Shell du principal d’utilisateur est recommandée.

Principaux de sécurité recommandés par environnement :
- **Environnement de production** :
  - Identité managée ou principal du service avec un certificat
- **Environnements de test et de développement** :
  - Identité managée, principal du service avec certificat ou principal du service avec secret
- **Développement local** :
  - Principal d’utilisateur ou principal du service avec secret

Les scénarios d’authentification ci-dessus sont pris en charge par la **bibliothèque cliente d’identité Azure** et intégrés aux kits de développement logiciel (SDK) Key Vault. La bibliothèque d’identité Azure peut être utilisée dans différents environnements et plateformes sans modifier votre code. L’identité Azure récupère également automatiquement le jeton d’authentification de la connexion à l’utilisateur Azure avec Azure CLI, Visual Studio, Visual Studio Code et d’autres. 

Pour plus d’informations sur la bibliothèque de client d’identité Azure, consultez :

**Bibliothèques clientes d’identité Azure**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[SDK de l’identité Azure .NET](/dotnet/api/overview/azure/identity-readme)|[SDK de l’identité Azure Python](/python/api/overview/azure/identity-readme)|[SDK de l’identité Azure Java](/java/api/overview/azure/identity-readme)|[SDK de l’identité Azure Javascript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> [Bibliothèque App Authentication](/dotnet/api/overview/azure/service-to-service-authentication) recommandée pour le Kit de développement logiciel (SDK) .NET Key Vault version 3, actuellement déconseillé. Veuillez suivre les instructions de la section [Guide de migration AppAuthentication vers Azure.Identity](/dotnet/api/overview/azure/app-auth-migration) pour migrer vers le Kit de développement logiciel (SDK) .NET Key Vault version 4.

Pour obtenir des didacticiels sur la façon de s’authentifier auprès de Key Vault dans les applications, consultez :
- [S’authentifier auprès de Key Vault dans l’application hébergée dans la machine virtuelle .NET](./tutorial-net-virtual-machine.md)
- [S’authentifier auprès de Key Vault dans l’application hébergée dans la machine virtuelle dans Python](./tutorial-python-virtual-machine.md)
- [S’authentifier auprès de Key Vault avec App Service](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Gérer des clés, certificats et secrets

L’accès aux clés, secrets et certificats est contrôlé par le plan de données. Le contrôle d’accès du plan de données peut être effectué à l’aide de stratégies d’accès au coffre locales ou d’Azure RBAC.

### <a name="keys-apis-and-sdks"></a>API et kits SDK de clés

| Azure CLI | PowerShell | API REST | Gestionnaire de ressources | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Référence](/cli/azure/keyvault/key)<br>[Démarrage rapide](../keys/quick-create-cli.md)|[Référence](/powershell/module/az.keyvault/)<br>[Démarrage rapide](../keys/quick-create-powershell.md)|[Référence](/rest/api/keyvault/#key-operations)|[Référence](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Démarrage rapide](../keys/quick-create-template.md)|[Référence](/dotnet/api/azure.security.keyvault.keys)<br>[Démarrage rapide](../keys/quick-create-net.md)|[Référence](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Démarrage rapide](../keys/quick-create-python.md)|[Référence](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Démarrage rapide](../keys/quick-create-java.md)|[Référence](/javascript/api/@azure/keyvault-keys/)<br>[Démarrage rapide](../keys/quick-create-node.md)|

### <a name="certificates-apis-and-sdks"></a>API et kits SDK de certificats

| Azure CLI | PowerShell | API REST | Gestionnaire de ressources | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Référence](/cli/azure/keyvault/certificate)<br>[Démarrage rapide](../certificates/quick-create-cli.md)|[Référence](/powershell/module/az.keyvault)<br>[Démarrage rapide](../certificates/quick-create-powershell.md)|[Référence](/rest/api/keyvault/#certificate-operations)|N/A|[Référence](/dotnet/api/azure.security.keyvault.certificates)<br>[Démarrage rapide](../certificates/quick-create-net.md)|[Référence](/python/api/overview/azure/keyvault-certificates-readme)<br>[Démarrage rapide](../certificates/quick-create-python.md)|[Référence](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Démarrage rapide](../certificates/quick-create-java.md)|[Référence](/javascript/api/@azure/keyvault-certificates/)<br>[Démarrage rapide](../certificates/quick-create-node.md)|

### <a name="secrets-apis-and-sdks"></a>API et kits SDK de secrets

| Azure CLI | PowerShell | API REST | Gestionnaire de ressources | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Référence](/cli/azure/keyvault/secret)<br>[Démarrage rapide](../secrets/quick-create-cli.md)|[Référence](/powershell/module/az.keyvault/)<br>[Démarrage rapide](../secrets/quick-create-powershell.md)|[Référence](/rest/api/keyvault/#secret-operations)|[Référence](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Démarrage rapide](../secrets/quick-create-template.md)|[Référence](/dotnet/api/azure.security.keyvault.secrets)<br>[Démarrage rapide](../secrets/quick-create-net.md)|[Référence](/python/api/overview/azure/keyvault-secrets-readme)<br>[Démarrage rapide](../secrets/quick-create-python.md)|[Référence](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Démarrage rapide](../secrets/quick-create-java.md)|[Référence](/javascript/api/@azure/keyvault-secrets/)<br>[Démarrage rapide](../secrets/quick-create-node.md)|

### <a name="secrets-usage"></a>Utilisation des secrets
Les secrets Azure Key Vault ne doivent être utilisés que pour stocker des secrets pour votre application. 

Exemples de secrets qui doivent être stockés dans Key Vault :
    - Secrets des applications clientes
    - Chaînes de connexion
    - Mots de passe
    - Clés d’accès partagé
    - Clés SSH

En outre, toutes les informations liées aux secrets, telles que les noms d’utilisateur et les ID d’application, peuvent être stockées sous la forme d’une étiquette dans un secret. Pour tous les autres paramètres de configuration sensibles, vous devez utiliser [Azure App Configuration](../../azure-app-configuration/overview.md).
 
### <a name="references"></a>References 

Consultez les [bibliothèques clientes](client-libraries.md) pour obtenir les packages d’installation et le code source.

Pour plus d’informations sur la sécurité du plan de données Key Vault, consultez [Fonctionnalités de sécurité d'Azure Key Vault](security-features.md).

### <a name="using-key-vault-in-applications"></a>Utilisation de Key Vault dans les applications

Nous vous recommandons d’utiliser les kits SDK Key Vault disponibles pour l’utilisation de secrets, de certificats et de clés dans votre application, afin de tirer parti des fonctionnalités les plus récentes de Key Vault. Les SDK Key Vault et l’API REST sont publiés par notre équipe au fur et à mesure que nous publions de nouvelles fonctionnalités pour le produit, et suivent nos bonnes pratiques et recommandations.

#### <a name="libraries-and-integration-solutions-for-limited-usage-scenarios"></a>Bibliothèques et solutions d’intégration pour les scénarios d’usage limité
Pour les scénarios de base, il existe d’autres solutions pour un usage simplifié avec la prise en charge fournie par les équipes partenaires ou les communautés open source.

##### <a name="certificates"></a>Certificats :
- L’extension de machine virtuelle Key Vault, qui assure l’actualisation automatique des certificats stockés dans un coffre de clés Azure 
    - [Extension de machine virtuelle Key Vault pour Windows](../../virtual-machines/extensions/key-vault-windows.md)
    - [Extension de machine virtuelle Key Vault pour Linux](../../virtual-machines/extensions/key-vault-linux.md)
    - [Extension de machine virtuelle Key Vault pour les serveurs avec Azure Arc](../../azure-arc/servers/manage-vm-extensions.md#azure-key-vault-vm-extension)
 - Intégration des certificats Azure App Service Key Vault, qui peut importer et actualiser automatiquement des certificats à partir de Key Vault
     - [Déploiement d’un certificat Azure Web App via Azure Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)

##### <a name="secrets"></a>Secrets :
- Utiliser des secrets Key Vault avec les paramètres d’application App Service
    - [Utiliser des références Key Vault pour App Service et Azure Functions](../../app-service/app-service-key-vault-references.md)
- Utiliser des secrets Key Vault avec le service App Configuration pour les applications hébergées dans une machine virtuelle Azure
    - [Configurer des applications avec App Configuration et Key Vault](/samples/azure/azure-sdk-for-net/app-secrets-configuration/)

## <a name="code-examples"></a>Exemples de code

Pour obtenir des exemples complets d’utilisation de Key Vault avec vos applications, voir :

- [Exemples de code Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) : exemples de code pour Azure Key Vault. 

## <a name="how-tos"></a>Procédures

Les articles et scénarios suivants fournissent des conseils spécifiques sur l’utilisation d’Azure Key Vault :

- [Accès à Key Vault derrière un pare-feu](access-behind-firewall.md) - Pour accéder à un coffre de clés, votre application cliente Key Vault doit être en mesure d’accéder à plusieurs points de terminaison pour différentes fonctionnalités.
- Comment déployer des certificats sur des machines virtuelles à partir de Key Vault - [Windows](../../virtual-machines/extensions/key-vault-windows.md), [Linux](../../virtual-machines/extensions/key-vault-linux.md) - Une application cloud s’exécutant sur une machine virtuelle sur Azure a besoin d’un certificat. Comment obtenir ce certificat sur cette machine virtuelle dès aujourd’hui ?
- Attribuer une stratégie d’accès ([CLI](assign-access-policy-cli.md) | [PowerShell](assign-access-policy-powershell.md) | [Portail](assign-access-policy-portal.md)). 
- [Guide pratique pour utiliser la suppression réversible Key Vault avec l’interface CLI](./key-vault-recovery.md) décrit l’utilisation et le cycle de vie d’un coffre de clés et des différents objets du coffre de clés quand la suppression réversible est activée.
- [Transmission de valeurs sécurisées (par exemple des mots de passe) lors du déploiement](../../azure-resource-manager/templates/key-vault-parameter.md) - Lorsque vous devez passer une valeur sécurisée (par exemple un mot de passe) en tant que paramètre au cours du déploiement, vous pouvez stocker cette valeur en tant que clé secrète dans un coffre de clés Azure et faire référence à la valeur dans d'autres modèles Resource Manager.

## <a name="integrated-with-key-vault"></a>Intégration avec Key Vault

Ces articles concernent d’autres scénarios et services qui utilisent ou intègrent Key Vault.

- Le [chiffrement au repos](../../security/fundamentals/encryption-atrest.md) permet l’encodage (chiffrement) des données quand elles sont stockées. Les clés de chiffrement de données sont souvent chiffrées avec une clé de chiffrement de clé dans Azure Key Vault pour limiter davantage l’accès.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) vous permet de gérer votre propre clé de locataire. Par exemple, plutôt que Microsoft gère votre clé de client (par défaut), vous pouvez gérer votre propre clé pour vous conformer aux réglementations spécifiques à votre organisation. La gestion de votre propre clé est également appelée BYOK, ou Bring your own key.
- Le [service Azure Private Link](private-link-service.md) vous permet d’accéder aux services Azure (par exemple, Azure Key Vault, Stockage Azure et Azure Cosmos DB) ainsi qu’aux services de partenaires ou de clients hébergés par Azure via un point de terminaison privé de votre réseau virtuel.
- L’intégration entre Key Vault et [Event Gri](../../event-grid/event-schema-key-vault.md)d permet aux utilisateurs d’être avertis en cas de modification de l’état d’un secret stocké dans le coffre de clés. Vous pouvez distribuer une nouvelle version de secrets aux applications ou appliquer une rotation aux secrets proches de l’expiration pour éviter les pannes.
- Vous pouvez protéger vos secrets [Azure DevOps](/azure/devops/pipelines/release/azure-key-vault) contre tout accès indésirable dans Key Vault.
- [Utiliser le secret stocké dans Key Vault dans DataBricks pour la connexion au stockage Azure](./integrate-databricks-blob-storage.md)
- Configurer et exécuter le fournisseur Azure Key Vault pour le [pilote CSI du magasin des secrets](./key-vault-integrate-kubernetes.md) sur Kubernetes

## <a name="key-vault-overviews-and-concepts"></a>Concepts et présentations des coffres de clés

- [Comportement de suppression réversible de Key Vault](soft-delete-overview.md) : décrit une fonctionnalité qui permet la récupération des objets supprimés, que la suppression soit accidentelle ou intentionnelle.
- [Limitation du client Key Vault](overview-throttling.md) : vous oriente vers les concepts de base de la limitation et propose une approche pour votre application.
- [Mondes de sécurité Key Vault](overview-security-worlds.md) : décrit les relations entre les régions et les zones de sécurité.

## <a name="social"></a>Réseaux sociaux

- [Blog de Key Vault](/archive/blogs/kv/)
- [Forum de Key Vault](https://aka.ms/kvforum)
- [Stack Overflow pour Key Vault](https://stackoverflow.com/questions/tagged/azure-keyvault)
