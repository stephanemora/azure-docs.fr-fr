---
title: Banque de secrets centrale Azure Service Fabric
description: Cet article explique comment utiliser la banque de secrets centrale (CSS, Central Secrets Store) dans Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: bc6ea6260bf50d5b4f8e294e0a3827426f90bee3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980938"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Banque de secrets centrale dans Azure Service Fabric 
Cet article explique comment utiliser la banque de secrets centrale (CSS, Central Secrets Store) dans Azure Service Fabric pour créer des secrets dans des applications Service Fabric. CSS est une banque de secrets locale qui garde chiffrées et en mémoire les données sensibles que sont notamment les mots de passe, les jetons et les clés.

## <a name="enable-central-secrets-store"></a>Activer la banque de secrets centrale
Ajoutez le script suivant à la configuration de votre cluster sous `fabricSettings` pour activer CSS. Nous vous recommandons d’utiliser un certificat autre qu’un certificat de cluster pour CSS. Vérifiez que le certificat de chiffrement est installé sur tous les nœuds et que `NetworkService` dispose d’une autorisation de lecture sur la clé privée du certificat.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>Déclarer une ressource de secret
Vous pouvez créer une ressource de secret à l’aide du modèle Azure Resource Manager ou de l’API REST.

### <a name="use-resource-manager"></a>Utiliser Resource Manager

Servez-vous du modèle suivant pour utiliser Resource Manager afin de créer la ressource de secret. Le modèle crée la ressource de secret `supersecret`, mais aucune valeur n’est encore définie pour celle-ci.


```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```

### <a name="use-the-rest-api"></a>Utiliser l’API REST

Pour créer une ressource de secret `supersecret` à l’aide de l’API REST, faites une demande PUT à `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Vous avez besoin du certificat du cluster ou du certificat client d’administration pour créer une ressource de secret.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-the-secret-value"></a>Définir la valeur du secret

### <a name="use-the-resource-manager-template"></a>Utiliser le modèle Resource Manager

Utilisez le modèle Resource Manager suivant pour créer et définir la valeur du secret. Ce modèle définit la valeur du secret pour la ressource de secret `supersecret` en tant que version `ver1`.
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
### <a name="use-the-rest-api"></a>Utiliser l’API REST

Utilisez le script suivant pour utiliser l’API REST afin de définir la valeur du secret.
```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="use-the-secret-in-your-application"></a>Utiliser le secret dans votre application

Effectuez les étapes suivantes pour utiliser le secret dans votre application Service Fabric.

1. Ajoutez une section dans le fichier **settings.xml** avec l’extrait de code suivant. Notez ici que la valeur est au format {`secretname:version`}.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importez la section dans **ApplicationManifest.xml**.
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   La variable d’environnement `SecretPath` pointe vers le répertoire où sont stockés tous les secrets. Chaque paramètre listé sous la section `testsecrets` est stocké dans un fichier séparé. L’application peut maintenant utiliser le secret comme ceci :
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Montez les secrets dans un conteneur. La seule modification nécessaire pour que les secrets soient disponibles à l’intérieur du conteneur consiste à `specify` un point de montage dans `<ConfigPackage>`.
L’extrait de code suivant correspond au fichier **ApplicationManifest.xml** modifié.  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   Les secrets sont disponibles sous le point de montage à l’intérieur de votre conteneur.

1. Vous pouvez lier un secret à une variable d’environnement de processus en spécifiant `Type='SecretsStoreRef`. L’extrait de code suivant est un exemple de liaison de la version de `supersecret` `ver1` à la variable d’environnement `MySuperSecret` dans **ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Étapes suivantes
Découvrez-en plus sur la [sécurité des applications et des services](service-fabric-application-and-service-security.md).
