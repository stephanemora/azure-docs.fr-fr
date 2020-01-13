---
title: Exécuter un service Azure Service Fabric sous un compte gMSA
description: Découvrez comment exécuter un service en tant que gMSA sur un cluster autonome Service Fabric Windows.
author: dkkapur
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: 99d8089bd12d05e46f91e55c933d58d50baa92f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464255"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Exécuter un service en tant que compte de service administré de groupe
Sur un cluster autonome Windows Server, vous pouvez exécuter un service en tant que compte de service administré de groupe (gMSA) avec une stratégie RunAs.  Par défaut, les applications Service Fabric s’exécutent sous le compte qui exécute le processus Fabric.exe. Les applications en cours d’exécution sous différents comptes sont plus sécurisées, même dans un environnement hébergé partagé. Remarque : cette fonctionnalité utilise Active Directory en local au sein de votre domaine et non Azure Active Directory (Azure AD). Si vous utilisez un compte gMSA, aucun mot de passe (chiffré ou non) n’est stocké dans le manifeste de l’application.  Vous pouvez également exécuter un service comme [utilisateur ou groupe Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

L’exemple suivant montre comment créer un compte gMSA nommé *svc-Test$* , comment déployer ce compte de service géré sur les nœuds de cluster et comment configurer le principal de l’utilisateur.

Conditions préalables :
- Le domaine a besoin d’une clé racine KDS.
- Il doit y avoir au moins un contrôleur de domaine Windows Server 2012 (ou R2) dans le domaine.

1. Avoir un administrateur de domaine Active Directory qui crée un compte de service géré de groupe à l’aide de la cmdlet `New-ADServiceAccount` et garantit que le `PrincipalsAllowedToRetrieveManagedPassword` inclut tous les nœuds du cluster Service Fabric. `AccountName`, `DnsHostName` et `ServicePrincipalName` doivent être uniques.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Sur chacun des nœuds de cluster Service Fabric (par exemple `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), installez et testez le compte gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configurez le principal de l’utilisateur et la stratégie RunAsPolicy pour faire référence à l’utilisateur.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE] 
> Si vous appliquez une stratégie Run-As à un service et que le manifeste de service déclare des ressources de point de terminaison avec le protocole HTTP, vous devez spécifier une **SecurityAccessPolicy**.  Pour plus d’informations, consultez [Assigner une stratégie d’accès de sécurité pour des points de terminaison HTTP et HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Comme prochaine étape, lisez les articles suivants :
* [Comprendre le modèle d'application](service-fabric-application-model.md)
* [Spécifier des ressources dans un manifeste de service](service-fabric-service-manifest-resources.md)
* [Déployer une application](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
