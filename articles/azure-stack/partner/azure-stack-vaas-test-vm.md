---
title: Déployer l’agent local et tester les machines virtuelles dans le service Validation en tant que service pour Azure Stack | Microsoft Docs
description: Déployez l’agent local et testez les machines virtuelles pour le service Validation en tant que service pour Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 9d32c23f66563988d023df3bf6a33efa30237e57
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234134"
---
# <a name="deploy-the-local-agent-and-test-virtual-machines"></a>Déployer l’agent local et tester les machines virtuelles

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Découvrez comment utiliser l’agent local Validation en tant que service (VaaS) pour vérifier votre matériel. Vous devez déployer l’agent local sur la solution Azure Stack en cours de validation avant d’exécuter les tests de validation.

> [!Note]  
> Vous devez vous assurer que la machine sur laquelle s’exécute l’agent local ne perd pas l’accès à Internet. La machine doit uniquement être accessible aux utilisateurs qui sont autorisés à utiliser le service VaaS pour Azure Stack.

Pour tester vos machines virtuelles :

1. Installez l’agent local.
2. Injectez des erreurs dans votre système.
3. Exécutez l’agent local.

## <a name="download-and-start-the-local-agent"></a>Télécharger et démarrer l’agent local

Téléchargez l’agent sur une machine présentant la configuration requise dans votre centre de données qui ne fait pas partie intégrante du système Azure Stack, mais qui a accès à tous les points de terminaison Azure Stack.

### <a name="machine-prerequisites"></a>Configuration requise pour les machines

Vérifiez que votre machine répond aux critères suivants :

- Accès à tous les points de terminaison Azure Stack
- .NET 4.6 et PowerShell 5.0 installés
- 8 Go de RAM au minimum
- 8 processeurs Core au minimum
- Espace disque de 200 Go au minimum
- Connectivité réseau à Internet stable

Azure Stack est le système testé. La machine ne doit pas faire partie intégrante d’Azure Stack ni être hébergée dans le cloud Azure Stack.

### <a name="download-and-install-the-agent"></a>Téléchargement et installation de l’agent

1. Ouvrez Windows PowerShell dans une invite de commandes avec élévation de privilèges sur la machine que vous allez utiliser pour exécuter les tests.
2. Exécutez la commande ci-après pour télécharger l’agent local :

    ```PowerShell  
        Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.3.2.0.nupkg" -outfile "OnPremAgent.zip"
        Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent.3.2.0 -Force
        Set-Location VaaSOnPremAgent.3.2.0\lib\net46
    ````

3. Exécutez la commande ci-après pour installer les dépendances de l’agent local :

    ```PowerShell  
        $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-VaaSPrerequisites -AadTenantId <AadTenantId> `
        -ServiceAdminCreds <ServiceAdminCreds> `
        -ArmEndpoint https://adminmanagement.<ExternalFqdn> `
        -Region <Region>
    ````

    **Paramètres**

    | Paramètre | Description |
    | --- | --- |
    | aadServiceAdminUser | Utilisateur administrateur général pour votre locataire Azure AD. Par exemple, il peut s’agir de vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | Mot de passe de l’utilisateur administrateur général. |
    | AadTenantId | ID de locataire Azure AD pour le compte Azure inscrit auprès du service Validation en tant que service. |
    | ExternalFqdn | Vous pouvez obtenir le nom de domaine complet à partir du fichier de configuration. Pour obtenir les instructions correspondantes, consultez l’article [Test parameters for validation as a service Azure Stack](azure-stack-vaas-parameters-test.md) (Paramètres de test du service Validation en tant que service pour Azure Stack). |
    | Région | Région de votre locataire Azure AD. |

La commande télécharge une image (disque dur virtuel de système d’exploitation) du référentiel d’images public et la copie à partir d’un service Stockage Blob Azure dans votre stockage Azure Stack. 

![Télécharger les composants requis](media/installingprereqs.png)

> [!Note]  
> Si vous rencontrez un problème de lenteur du réseau lorsque vous téléchargez ces images, téléchargez ces dernières séparément dans un partage local et spécifiez le paramètre **-LocalPackagePath** *FileShareOrLocalPath*. Vous trouverez des conseils supplémentaires sur le téléchargement du référentiel d’images public dans la section [Handle slow network connectivity](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) (Gérer les problèmes de lenteur de connectivité réseau) de l’article [Troubleshoot validation as a service](azure-stack-vaas-troubleshoot.md) (Résoudre les problèmes du service Validation en tant que service).

## <a name="fault-injection"></a>Injection d’erreurs

Microsoft a conçu Azure Stack pour garantir la résilience et pour tolérer plusieurs types d’erreurs logicielles et matérielles. L’injection d’erreurs augmente le taux d’erreurs dans le système. Cette approche vous permet de déceler les problèmes de manière anticipée afin de réduire le nombre d’incidents qui entraînent un arrêt du système.

Exécutez les commandes ci-après pour injecter des erreurs dans votre système.

1. Ouvrez Windows PowerShell dans une invite de commandes avec élévation des privilèges.

2. Exécutez la commande suivante :

    ```PowerShell  
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-ServiceFabricSDK Install-ServiceFabricSDK
    ```

## <a name="run-the-agent"></a>Exécuter l’agent

1. Ouvrez Windows PowerShell dans une invite de commandes avec élévation des privilèges.

2. Exécutez la commande suivante :

    ````PowerShell  
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ````

      **Paramètres**  
    
    | Paramètre | Description |
    | --- | --- |
    | VaaSUserId | Identificateur d’utilisateur permettant de se connecter au portail VaaS (par exemple, UserName@Contoso.com). |
    | VaaSTenantId | ID de locataire Azure AD pour le compte Azure inscrit auprès du service Validation en tant que service. |

    > [!Note]  
    > Lorsque vous exécutez l’agent, le répertoire de travail actuel doit être l’emplacement de l’exécutable de l’hôte du moteur de tâches, **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Si aucune erreur ne vous est signalée, l’agent local a réussi. L’exemple de texte ci-après s’affiche dans la fenêtre de console.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Agent démarré](media/startedagent.png)

Un agent est identifié de manière unique par son nom. Par défaut, il utilise le nom de domaine complet (FQDN) de la machine sur laquelle il a été démarré. Vous devez minimiser la fenêtre pour éviter les clics accidentels dans cette dernière, car le basculement vers cette fenêtre interrompt toutes les autres actions.

## <a name="next-steps"></a>Étapes suivantes

- [Valider une nouvelle solution Azure Stack](azure-stack-vaas-validate-solution-new.md)  
- Si vous rencontrez des problèmes de lenteur ou d’instabilité de la connectivité Internet, vous pouvez télécharger l’image du référentiel d’images public. Pour plus d’informations, consultez la section [Handle slow network connectivity](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) (Gérer les problèmes de lenteur de connectivité réseau).
- Apprenez-en davantage sur le service [Validation en tant que service pour Azure Stack](https://docs.microsoft.com/azure/azure-stack/partner).
