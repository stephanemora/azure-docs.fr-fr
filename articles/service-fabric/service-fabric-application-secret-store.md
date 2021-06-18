---
title: Service de secrets central Azure Service Fabric
description: Cet article explique comment utiliser le service CSS (Central Secret Service) dans Azure Service Fabric.
author: amenarde
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: anmenard
ms.openlocfilehash: 1c920631d671ad2af9fa2e723fc98bf96462b007
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950109"
---
# <a name="central-secret-service-in-azure-service-fabric"></a>Service CSS dans Azure Service Fabric 
CSS, également appelé « magasin de secrets central », est un service système Service Fabric destiné à protéger les secrets au sein d’un cluster. CSS facilite la gestion des secrets des applications SF, ce qui évite de devoir recourir à des paramètres chiffrés.

CSS est un cache de secrets en cluster durable et répliqué ; les secrets stockés dans ce service sont chiffrés au repos dans un certificat de chiffrement fourni par le client. CSS fournit des API clientes pour la gestion des secrets, accessibles aux entités qui s’authentifient en tant que cluster, ou à un utilisateur administrateur de cluster. Le modèle d’application de runtime Service Fabric s’intègre à CSS, ce qui permet la déclaration de paramètres d’application en tant que références de secret CSS. 

CSS permet également d’approvisionner des secrets d’application déclarés en tant qu'[URI de secret KeyVault](service-fabric-keyvault-references.md), en association avec l'[identité managée pour les applications Service Fabric déployées par Azure](concepts-managed-identity.md).

CSS n’a pas vocation à remplacer un service de gestion de secrets externe dédié, tel qu’Azure Key Vault. 

  > [!NOTE] 
  > En cas d’activation de CSS sur un cluster SF exécutant une version antérieure à la version [7.1. CU3](service-fabric-versions.md#service-fabric-version-name-and-number-reference), l’activation peut échouer et maintenir CSS dans un état permanent non sain si le cluster est configuré pour l’authentification Windows, si `EncryptionCertificateThumbprint` est déclaré de manière incorrecte ou si le certificat correspondant n’est pas installé. Dans ces différents cas, il est recommandé de mettre à niveau le cluster vers une version de runtime SF ultérieure à la version 7.1. CU3 avant de continuer. 
  
## <a name="enable-central-secrets-service"></a>Activer le service CSS
Pour activer le service CSS, mettez à jour la configuration du cluster comme décrit ci-dessous. Nous vous recommandons d’utiliser un certificat de chiffrement différent de votre certificat de cluster. Ce certificat doit être installé sur tous les nœuds.
```json
{ 
    "fabricSettings": [
        {
            "name":  "CentralSecretService",
            "parameters":  [
                {
                    "name":  "DeployedState",
                    "value":  "enabled"
                },
                {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "1"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                }
            ]
        }
    ]
}
```
  > [!NOTE] 
  > Le paramètre de configuration « DeployedState », introduit dans Service Fabric [version 8.0](service-fabric-versions.md#service-fabric-version-name-and-number-reference), est le mécanisme préféré pour activer ou désactiver CSS. Cette fonction était fournie dans les versions précédentes par le paramètre de configuration « IsEnabled », désormais considéré comme obsolète.

## <a name="central-secret-service-secret-model"></a>Modèle de secret du service CSS
L’API de CSS expose deux types : la ressource de secret et la version de secret. Le type de ressource de secret représente, en termes conceptuels, une famille de versions de secret unique utilisée à des fins spécifiques, dont voici quelques exemples : chaîne de connexion, mot de passe, certificat de point de terminaison. Un objet du type de ressource de secret contient les métadonnées associées à ce secret, notamment le genre, le type de contenu et la description. Le type de version de secret représente une instance particulière du secret associé et stocke le texte en clair du secret (chiffré). En se référant aux exemples ci-dessus, une version de secret contient la valeur du mot de passe actuel, un objet de certificat valide jusqu’à la fin du mois, etc. Lors du renouvellement de ces secrets, de nouvelles versions de secret doivent être générées (et ajoutées à CSS).

En formalisant le modèle, les règles implémentées et appliquées dans l’implémentation CSS sont les suivantes :

- Une ressource de secret peut avoir zéro ou plusieurs versions
- Chaque version de secret est un enfant d’une ressource de secret spécifique ; une version ne peut avoir qu’une seule ressource parente
- Une version de secret individuelle peut être supprimée sans affecter les autres versions du même secret
- La suppression d’une ressource de secret entraîne la suppression de toutes ses versions  
- La valeur d’une version de secret est immuable
    
L’ensemble complet d’API de gestion REST correspondant aux ressources de secret est disponible [ici](/rest/api/servicefabric/sfclient-index-meshsecrets), et pour les versions de secret, [ici](/rest/api/servicefabric/sfclient-index-meshsecretvalues).

### <a name="declare-a-secret-resource"></a>Déclarer une ressource de secret
Vous pouvez créer une ressource de secret à l’aide de l’API REST.

  > [!NOTE] 
  > Si le cluster utilise l’authentification Windows sans certificat HttpGateway, la requête REST est envoyée via un canal HTTP non sécurisé. Pour activer TLS pour ce canal, vous devez mettre à jour la définition du cluster afin de spécifier un certificat de serveur de passerelle HTTP.

Pour créer une ressource de secret `supersecret` à l’aide de l’API REST, faites une demande PUT à `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Vous devrez vous authentifier à l’aide d’un certificat de cluster ou d’un certificat client d’administration pour créer une ressource de secret.
```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

### <a name="set-the-secret-value"></a>Définir la valeur du secret
Utilisez le script suivant pour utiliser l’API REST afin de définir la valeur du secret.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```

### <a name="examine-the-secret-value"></a>Examiner la valeur du secret
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```

## <a name="use-the-secret-in-your-application"></a>Utiliser le secret dans votre application
Une application peut utiliser un secret à partir de CSS en le déclarant en tant que variable d’environnement, ou en spécifiant un chemin d’accès où le texte en clair du secret doit être sérialisé. Pour référencer un secret CSS, procédez comme suit :

1. Ajoutez une section dans le fichier **settings.xml** avec l’extrait de code suivant. Notez ici que la valeur est au format {`secretname:version`}.
```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
```

2. Importez la section dans **ApplicationManifest.xml**.
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
   
Exemple 1 : Monter les secrets dans un conteneur. La seule modification nécessaire pour que les secrets soient disponibles à l’intérieur du conteneur consiste à `specify` un point de montage dans `<ConfigPackage>`.
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

Exemple 2 : Lier un secret à une variable d’environnement de processus en spécifiant `Type='SecretsStoreRef`. L’extrait de code suivant est un exemple de liaison de la version de `supersecret` `ver1` à la variable d’environnement `MySuperSecret` dans **ServiceManifest.xml**.

```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
```

La variable d’environnement `SecretPath` pointe vers le répertoire où sont stockés tous les secrets. Chaque paramètre listé sous la section `testsecrets` est stocké dans un fichier séparé. L’application peut maintenant utiliser le secret comme ceci :
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
   
## <a name="rotating-the-central-secret-service-encryption-certificate"></a>Rotation du certificat de chiffrement CSS
Il est important de noter que les certificats demeurent valides à des fins de déchiffrement au-delà de leur expiration. À ce stade, nous vous recommandons de continuer à approvisionner des certificats de chiffrement antérieurs après la rotation et ce, afin de réduire le risque de verrouillage. Pour effectuer la rotation du certificat de chiffrement CSS, procédez comme suit :

1. Approvisionnez le nouveau certificat sur chaque nœud du cluster. À ce stade, veillez à ne pas supprimer ou approvisionner le certificat de chiffrement précédent.
2. Démarrez une mise à niveau de configuration du cluster pour modifier la valeur de `EncryptionCertificateThumbprint` sur l’empreinte SHA-1 du nouveau certificat.
Au terme de la mise à niveau, le code CSS commence à rechiffrer son contenu existant sur le nouveau certificat de chiffrement. Tous les secrets ajoutés ensuite à CSS sont directement chiffrés dans le nouveau certificat de chiffrement. La convergence de protection de tous les secrets par le nouveau certificat étant asynchrone, il est important que le certificat de chiffrement précédent reste installé sur tous les nœuds et mis à la disposition de CSS.

## <a name="removing-central-secret-service-from-your-cluster"></a>Suppression de CSS de votre cluster
La suppression sécurisée de CSS d’un cluster nécessite deux mises à niveau. La première mise à niveau désactive de manière fonctionnelle CSS, tandis que la seconde supprime le service de la définition de cluster, ce qui comprend la suppression définitive de son contenu. Ce processus en deux étapes empêche la suppression accidentelle du service et permet de s’assurer qu’il n’existe aucune dépendance orpheline sur CSS lors du processus de suppression. Cette fonctionnalité est disponible à partir de la version 8.0 de SF.

### <a name="step-1-update-css-deployedstate-to-removing"></a>Étape 1 : mettre à jour l’état DeployedState de CSS pour la suppression
Mettre à niveau la définition du cluster à partir de `"IsEnabled" = "true"` ou de `"DeployedState" = "enabled"` vers
```json
{
    "name":  "DeployedState",
    "value":  "removing"
}
```
Lorsque CSS accède à l’état déployé `Removing`, il rejette tous les appels d’API de secret entrants, qu’il s’agisse d’appels REST directs ou via des activations de services incluant SecretStoreRefs ou KeyVaultReferences. À ce stade, les applications ou composants du cluster qui dépendent toujours de CSS passent à l’état d’avertissement. Si tel est le cas, la mise à niveau vers l’état déployé `Removing` doit être annulée ; si cette mise à niveau a déjà réussi, une nouvelle mise à niveau doit être lancée pour reconvertir le code CSS sur DeployedState = `Enabled` . Si CSS reçoit une requête alors qu’il est dans l’état déployé `Removing`, il renvoie le code HTTP 401 (non autorisé) et se met lui-même dans un état d’avertissement.

### <a name="step-2-update-css-deployedstate-to-disabled"></a>Étape 2 : mettre à jour l’état DeployedState de CSS sur désactivé
Mettre à niveau la définition du cluster à partir de `"DeployedState" = "removing"` vers
```json
{
    "name":  "DeployedState",
    "value":  "disabled"
}
```
CSS ne doit plus s’exécuter dans le cluster, et ne figurera pas dans la liste des services système. Le contenu de CSS est irrémédiablement perdu. 

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur la [sécurité des applications et des services](service-fabric-application-and-service-security.md).
- Découvrir l’[Identité managée pour les applications Service Fabric](concepts-managed-identity.md)
- Développer la fonctionnalité de CSS avec [KeyVaultReferences](service-fabric-keyvault-references.md)