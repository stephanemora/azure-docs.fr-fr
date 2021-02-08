---
title: Azure Service Fabric – Utilisation des références KeyVault de l'application Service Fabric
description: Cet article explique comment utiliser la prise en charge de Service Fabric KeyVaultReference pour des secrets d’application.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898594"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>Prise en charge de KeyVaultReference pour les applications Service Fabric déployées dans Azure

Un défi courant lors de la création d’applications cloud est de savoir comment distribuer en toute sécurité les secrets à vos applications. Par exemple, vous souhaiterez peut-être déployer une clé de base de données dans votre application sans exposer la clé pendant le pipeline ou la transmettre à l’opérateur. La prise en charge de Service Fabric KeyVaultReference facilite le déploiement des secrets dans vos applications en référençant simplement l’URL du secret stocké dans Key Vault. Service Fabric gérera l’extraction de ce secret pour le compte de l’identité gérée de votre application ainsi que l’activation de l’application avec le secret.

> [!NOTE]
> La prise en charge de KeyVaultReference pour les applications Service Fabric est en disponibilité générale à partir de Service Fabric version 7.2 CU5. Il est recommandé d’effectuer la mise à niveau vers cette version avant d’utiliser cette fonctionnalité.

> [!NOTE]
> La prise en charge de KeyVaultReference pour les applications Service Fabric s’applique uniquement aux secrets [avec version](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning). Les secrets sans version ne sont pas pris en charge. Le coffre de clé doit se trouver dans le même abonnement que votre cluster Service Fabric. 

## <a name="prerequisites"></a>Prérequis

- Identité managée pour applications Service Fabric

    La prise en charge de Service Fabric KeyVaultReference utilise l’identité gérée d’une application pour extraire les secrets pour le compte de l’application, de sorte que votre application doit être déployée via une identité gérée et lui être attribuée. Suivez cet [document](concepts-managed-identity.md) pour activer l’identité managée pour votre application.

- Banque de secrets centrale (CSS, Central Secrets Store).

    Central Secrets Store (CSS) est le cache de secrets local chiffré de Service Fabric. Cette fonctionnalité utilise CSS pour protéger et conserver les secrets une fois qu’ils ont été extraits de Key Vault. L’activation de ce service système facultatif est également requise pour utiliser cette fonctionnalité. Consultez ce [document](service-fabric-application-secret-store.md) pour activer et configurer CSS.

- Accordez l’autorisation d’accès avec identité managée de l’application au coffre de clés

    Pour voir comment accorder un accès avec identité managée au coffre de clés, reportez-vous à ce [document](how-to-grant-access-other-resources.md). Notez également que, si vous utilisez une identité managée affectée par le système, l’identité managée est créée uniquement après le déploiement de l’application. Cela peut créer des conditions de concurrence lorsque l’application tente d’accéder au secret avant que l’identité ne soit autorisée à accéder au coffre. Le nom de l’identité affectée par le système sera `{cluster name}/{application name}/{service name}`.
    
## <a name="use-keyvaultreferences-in-your-application"></a>Utiliser KeyVaultReferences dans votre application
KeyVaultReferences peut être utilisé de plusieurs façons
- [En tant que variable d'environnement](#as-an-environment-variable)
- [Monté en tant que fichier dans votre conteneur](#mounted-as-a-file-into-your-container)
- [Comme référence à un mot de passe de référentiel de conteneur](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>En tant que variable d'environnement

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>Monté en tant que fichier dans votre conteneur

- Ajoutez une section à settings.xml

    Définissez le paramètre `MySecret` avec le type `KeyVaultReference` et la valeur `<KeyVaultURL>`

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- Référencez la nouvelle section du fichier ApplicationManifest.xml dans `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="MyServiceMI" ApplicationIdentityRef="MyApplicationMI" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="MySecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Consommer les secrets à partir du code de service

    Chaque paramètre répertorié sous `<Section  Name=MySecrets>` sera un fichier dans le dossier sur lequel pointe EnvironmentVariable SecretPath. L’extrait de code C# ci-dessous montre comment lire MySecret à partir de votre application.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > MountPoint contrôle le dossier dans lequel les fichiers contenant des valeurs secrètes seront montés.

### <a name="as-a-reference-to-a-container-repository-password"></a>Comme référence à un mot de passe de référentiel de conteneur

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>Étapes suivantes

* [Documentation sur le coffre de clés Azure](../key-vault/index.yml)
* [En savoir plus sur le magasin central de secrets](service-fabric-application-secret-store.md)
* [En savoir plus sur Identité managée pour les applications Service Fabric](concepts-managed-identity.md)
