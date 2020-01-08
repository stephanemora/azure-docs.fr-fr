---
title: Service Fabric Secrets Store
description: Cet article explique comment utiliser Service Fabric Secrets Store.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 16608d9eaf12fc9abc535ef316d7b5e8b74a8b37
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457510"
---
#  <a name="service-fabric-secrets-store"></a>Service Fabric Secrets Store
Cet article explique comment créer et utiliser des secrets dans des applications Service Fabric à l’aide de Service Fabric Secrets Store (CSS). CSS est un cache de magasin des secrets local qui sert à garder chiffrées et en mémoire les données sensibles que sont notamment les mots de passe, les jetons et des clés chiffrées.

## <a name="enabling-secrets-store"></a>Activation de Secrets Store
 Ajoutez le code ci-dessous à la configuration de votre cluster sous `fabricSettings` pour activer CSS. Pour CSS, il est recommandé d’utiliser un certificat différent de celui du cluster. Vérifiez que le certificat de chiffrement est installé sur tous les nœuds et que `NetworkService` dispose d’une autorisation de lecture sur la clé privée du certificat.
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
## <a name="declare-secret-resource"></a>Déclarer la ressource de secret
Vous pouvez créer une ressource de secret à l’aide du modèle Resource Manager ou de l’API REST.

* Utilisation du modèle Resource Manager
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
Le modèle ci-dessus crée la ressource de secret `supersecret`, mais aucune valeur n’est encore définie pour celle-ci.

* Utilisation de l’API REST

Pour créer une ressource de secret, `supersecret` adresse une requête PUT à `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Vous avez besoin du certificat du cluster ou du certificat client d’administration pour créer un secret.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-secret-value"></a>Définir la valeur du secret
* Utilisation du modèle Resource Manager

Le modèle Resource Manager ci-dessous crée et définit la valeur du secret `supersecret` avec la version `ver1`.
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
* Utilisation de l’API REST

```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="using-the-secret-in-your-application"></a>Utilisation du secret dans votre application

1.  Ajoutez une section dans le fichier settings.xml avec le contenu ci-dessous. Notez ici que Value est au format {`secretname:version`}.

```xml
  <Section Name="testsecrets">
   <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
  </Section>
```
2. À présent, importez la section du fichier ApplicationManifest.xml.
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

La variable d’environnement « SecretPath » pointe vers le répertoire où sont stockés tous les secrets. Chaque paramètre figurant sous la section `testsecrets` est stocké dans un fichier séparé. L’application peut maintenant utiliser le secret comme illustré ci-dessous.
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
3. Montage des secrets dans un conteneur

La seule modification à apporter aux secrets disponibles à l’intérieur du conteneur consiste à spécifier un point de montage (« MountPoint ») dans `<ConfigPackage>`.
Voici le fichier ApplicationManifest. xml modifié :  

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
Les secrets seront disponibles sous le point de montage à l’intérieur de votre conteneur.

4. Liaison du secret à une variable d’environnement 

Vous pouvez lier le secret à une variable d’environnement de processus en spécifiant Type='SecretsStoreRef'. Voici un exemple qui montre comment lier `supersecret` version `ver1` à la variable d’environnement `MySuperSecret` dans ServiceManifest.xml.

```xml
<EnvironmentVariables>
  <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
</EnvironmentVariables>
```
## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [la sécurité des applications et des services](service-fabric-application-and-service-security.md)
