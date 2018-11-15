---
title: Configurer Azure Active Directory pour l’authentification des clients dans Service Fabric | Microsoft Docs
description: Découvrez comment configurer Azure Active Directory (Azure AD) pour authentifier les clients des clusters Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/15/2018
ms.author: aljo
ms.openlocfilehash: 75ba2ee378e9eddfeaeb2346b4d5bb584844afe2
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636661"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Configuration d’Azure Active Directory pour l’authentification des clients

Pour les clusters exécutés sur Azure, il est recommandé d’utiliser Azure Active Directory (Azure AD) pour sécuriser l’accès aux points de terminaison de gestion.  Cet article explique comment configurer Azure AD pour authentifier les clients d’un cluster Service Fabric, ce qui doit être effectué avant de [créer le cluster](service-fabric-cluster-creation-via-arm.md).  Azure AD permet aux organisation (appelées locataires) de gérer l’accès utilisateur aux applications. Ces dernières se composent d’applications avec une interface utilisateur de connexion web et d’applications avec une expérience client natif. Dans cet article, nous partons du principe que vous avez déjà créé un locataire. Si ce n’est pas le cas, commencez par lire [Obtention d’un client Azure Active Directory][active-directory-howto-tenant].

## <a name="create-azure-ad-applications"></a>Créer des applications Azure AD
Un cluster Service Fabric offre différents points d’entrée pour leurs fonctionnalités de gestion, notamment les outils [Service Fabric Explorer][service-fabric-visualizing-your-cluster] et [Visual Studio][service-fabric-manage-application-in-visual-studio]. Par conséquent, vous allez créer deux applications Azure AD pour contrôler l’accès au cluster : une application web et une application native.  Après avoir créé les applications, vous devez affecter les utilisateurs aux rôles en lecture seule et administrateur.

Pour simplifier certaines des étapes impliquées dans la configuration d’Azure AD avec un cluster Service Fabric, nous avons créé un ensemble de scripts Windows PowerShell.

> [!NOTE]
> Vous devez exécuter les étapes suivantes avant de créer le cluster. Étant donné que les scripts attendent des noms de cluster et des points de terminaison, ces valeurs doivent être des valeurs planifiées et non celles que vous avez déjà créées.

1. [Téléchargez les scripts][sf-aad-ps-script-download] sur votre ordinateur.
2. Cliquez avec le bouton sur le fichier zip, sélectionnez **Propriétés**, activez la case à cocher **Débloquer**, puis cliquez sur **Appliquer**.
3. Extrayez le fichier zip.
4. Exécutez `SetupApplications.ps1` et indiquez les valeurs de TenantId, ClusterName et WebApplicationReplyUrl en tant que paramètres. Par exemple : 

```PowerShell
.\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
```

> [!NOTE]
> Pour les clouds nationaux (Azure Government, Azure - Chine, Azure - Allemagne), vous devez également spécifier le paramètre `-Location`.

Vous pouvez trouver votre TenantId en exécutant la commande PowerShell `Get-AzureSubscription`. L’exécution de cette commande permet d’afficher le TenantId de chaque abonnement.

Le paramètre ClusterName est utilisé pour préfixer les applications Azure AD créées par le script. Il ne doit pas forcément correspondre précisément au nom du cluster. Il sert uniquement à simplifier le mappage des artefacts Azure AD au cluster Service Fabric avec lequel ils sont utilisés.

Le paramètre WebApplicationReplyUrl est le point de terminaison par défaut renvoyé par Azure AD aux utilisateurs une fois qu’ils se sont connectés. Définissez ce point de terminaison en tant que point de terminaison Service Fabric Explorer pour votre cluster, qui est par défaut :

https://&lt;cluster_domain&gt;:19080/Explorer

Vous êtes invité à vous connecter à un compte qui dispose de privilèges d’administration pour le locataire Azure AD. Une fois que vous vous êtes connecté, le script crée les applications web et native pour représenter votre cluster Service Fabric. Si vous examinez les applications du client dans le [Portail Azure][azure-portal], vous devez voir deux nouvelles entrées :

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

Comme le script imprime le JSON exigé par le modèle Azure Resource Manager lorsque vous créez le cluster (dans la section suivante), il est judicieux de garder la fenêtre PowerShell ouverte.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Affecter des utilisateurs aux rôles
Une fois que vous avez créé les applications pour représenter votre cluster, affectez les utilisateurs aux rôles pris en charge par Service Fabric : lecture seule et administrateur. Vous pouvez affecter les rôles à l’aide du [portail Azure][azure-portal].

1. Dans le portail Azure, sélectionnez votre locataire dans l’angle supérieur droit.

    ![Bouton Sélectionner le locataire][select-tenant-button]
2. Sélectionnez **Azure Active Directory** sous l’onglet gauche, puis « Applications d’entreprise ».
3. Sélectionnez « Toutes les applications », puis recherchez et sélectionnez l’application web qui porte un nom semblable à celui-ci : `myTestCluster_Cluster`.
4. Cliquez sur l’onglet **Utilisateurs et groupes**.

    ![Onglet Utilisateurs et groupes][users-and-groups-tab]
5. Cliquez sur le bouton **Ajouter un utilisateur** dans la nouvelle page, sélectionnez un utilisateur et le rôle à affecter, puis cliquez sur le bouton **Sélectionner** situé en bas de la page.

    ![Page Affecter des utilisateurs aux rôles][assign-users-to-roles-page]
6. Cliquez sur le bouton **Affecter** en bas de la page.

    ![Confirmation de l’ajout de l’affectation][assign-users-to-roles-confirm]

> [!NOTE]
> Pour plus d’informations sur les rôles dans Service Fabric, consultez [Contrôle d’accès en fonction du rôle pour les clients de Service Fabric](service-fabric-cluster-security-roles.md).
>
>

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Aide pour la résolution des problèmes de configuration d’Azure Active Directory
La configuration et l’utilisation d’Azure AD peuvent être difficiles, voici donc quelques conseils sur ce que vous pouvez faire pour résoudre le problème.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer vous demande de sélectionner un certificat
#### <a name="problem"></a>Problème
Une fois que vous vous êtes connecté à Azure AD dans Service Fabric Explorer, le navigateur revient à la page d’accueil, mais un message vous invite à sélectionner un certificat.

![Boîte de dialogue Certificat SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motif
Aucun rôle n’a été affecté à l’utilisateur dans l’application Azure AD en cluster. Par conséquent, l’authentification Azure AD échoue sur le cluster Service Fabric. Service Fabric Explorer revient à l’authentification par certificat.

#### <a name="solution"></a>Solution
Suivez les instructions de configuration d’Azure AD et affectez des rôles utilisateur. Nous vous recommandons également d’activer l’option Affectation de l’utilisateur requise pour accéder à l’application, comme le fait `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>La connexion avec PowerShell échoue avec un message d’erreur : « Les informations d’identification spécifiées ne sont pas valides »
#### <a name="problem"></a>Problème
Lorsque vous utilisez PowerShell pour vous connecter au cluster à l’aide du mode de sécurité « AzureActiveDirectory », une fois que vous vous êtes connecté à Azure AD, la connexion échoue avec un message d’erreur : « Les informations d’identification spécifiées ne sont pas valides. »

#### <a name="solution"></a>Solution
La solution est la même que pour le problème précédent.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Lorsque vous vous connectez, Service Fabric Explorer renvoie un message d’échec : « AADSTS50011 »
#### <a name="problem"></a>Problème
Lorsque vous essayez de vous connecter à Azure AD dans Service Fabric Explorer, la page renvoie un message d’échec : « AADSTS50011 : l’adresse de réponse &lt;url&gt; ne correspond pas aux adresses de réponse configurées pour l’application : &lt;guid&gt;. »

![L’adresse de réponse SFX ne correspond pas][sfx-reply-address-not-match]

#### <a name="reason"></a>Motif
L’application en cluster (web) représentant Service Fabric Explorer tente de s’authentifier auprès d’Azure AD. Dans le cadre de cette demande, elle fournit l’URL de redirection. Cette dernière ne figure cependant pas dans la liste **URL DE RÉPONSE** de l’application Azure AD.

#### <a name="solution"></a>Solution
Sélectionnez « Inscriptions des applications » dans la page AAD, sélectionnez votre application en cluster, puis appuyez sur le bouton **URL de réponse**. Dans la page « URL de réponse », ajoutez l’URL de Service Fabric Explorer à la liste ou remplacez l’un des éléments dans la liste. Lorsque vous avez terminé, enregistrez vos modifications.

![URL de réponse d’application web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Connecter le cluster à l’aide de l’authentification Azure AD via PowerShell
Pour connecter le cluster Service Fabric, utilisez l’exemple de commande PowerShell suivant :

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Pour plus d’informations, consultez l’applet de commande [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Puis-je utiliser le même locataire Azure AD pour plusieurs clusters ?
Oui. Cependant, n’oubliez pas d’ajouter l’URL de Service Fabric Explorer à votre application en cluster (web). Si vous ne le faites pas, Service Fabric Explorer ne fonctionnera pas.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Pourquoi dois-je disposer d’un certificat de serveur lorsqu’Azure AD est activé ?
FabricClient et FabricGateway effectuent une authentification mutuelle. Pendant l’authentification Azure AD, l’intégration Azure AD fournit une identité de client au serveur et le certificat de serveur est utilisé pour vérifier l’identité du serveur. Pour plus d’informations sur les certificats Service Fabric, consultez [Certificats X.509 et Service Fabric][x509-certificates-and-service-fabric]

## <a name="next-steps"></a>Étapes suivantes
Après avoir configuré les applications Azure Active Directory et défini les rôles des utilisateurs, [configurez et déployez un cluster](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[select-tenant-button]: ./media/service-fabric-cluster-creation-setup-aad/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-setup-aad/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-setup-aad/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-setup-aad/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
