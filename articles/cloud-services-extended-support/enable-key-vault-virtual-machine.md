---
title: Appliquer l’extension de machine virtuelle Key Vault dans Azure Cloud Services (prise en charge étendue)
description: Activer l’extension de machine virtuelle Key Vault pour Cloud Services (prise en charge étendue)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: shisriva
ms.author: shisriva
ms.reviewer: gachandw
ms.date: 05/12/2021
ms.custom: ''
ms.openlocfilehash: 27a25cbff54acda871530daca455045e5519c936
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110104121"
---
# <a name="apply-the-key-vault-vm-extension-to-azure-cloud-services-extended-support"></a>Appliquer l’extension de machine virtuelle Key Vault dans Azure Cloud Services (prise en charge étendue)

## <a name="what-is-the-key-vault-vm-extension"></a>Qu’est-ce que l’extension de machine virtuelle Key Vault ?
L’extension de machine virtuelle Key Vault assure l’actualisation automatique des certificats stockés dans un coffre de clés Azure Key Vault. Plus précisément, l’extension supervise une liste de certificats examinés qui sont stockés dans des coffres de clés et, lorsqu’une modification est détectée, récupère et installe les certificats correspondants. Pour plus d’informations, consultez [Extension de machine virtuelle Key Vault pour Windows](../virtual-machines/extensions/key-vault-windows.md).

## <a name="whats-new-in-the-key-vault-vm-extension"></a>Quelles sont les nouveautés de l’extension de machine virtuelle Key Vault ?
L’extension de machine virtuelle Key Vault est désormais prise en charge sur la plateforme Azure Cloud Services (prise en charge étendue) pour permettre la gestion des certificats de bout en bout. L’extension peut désormais extraire des certificats d’un coffre Key Vault configuré à un intervalle d’interrogation prédéfini et les installer pour une utilisation par le service. 

## <a name="how-can-i-leverage-the-key-vault-vm-extension"></a>Comment puis-je tirer parti de l’extension de machine virtuelle Key Vault ?
Le tutoriel suivant vous montre comment installer l’extension de machine virtuelle Key Vault sur les services PaaSV1 en créant d’abord un certificat bootstrap dans votre coffre pour obtenir un jeton d’AAD qui facilitera l’authentification de l’extension auprès du coffre. Une fois que le processus d’authentification est configuré et que l’extension est installée, tous les certificats les plus récents sont récupérés automatiquement à des intervalles d’interrogation réguliers. 


## <a name="prerequisites"></a>Prérequis 
Pour utiliser l’extension de machine virtuelle Azure Key Vault, vous devez disposer d’un locataire Azure Active Directory. Pour plus d’informations sur la configuration d’un nouveau locataire Active Directory, consultez [Configurer votre locataire AAD](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="enable-the-azure-key-vault-vm-extension"></a>Activer l’extension de machine virtuelle Azure Key Vault

1. [Générez un certificat](../key-vault/certificates/create-certificate-signing-request.md) dans votre coffre et téléchargez le fichier .cer pour ce certificat.

2. Dans le [portail Azure](https://portal.azure.com) accédez à **Inscriptions d’applications**.
    
    :::image type="content" source="media/app-registration-0.jpg" alt-text="Montre la sélection de l’inscription d’application dans le portail.":::
    

3. Dans la page Inscriptions d’applications, sélectionnez **Nouvelle inscription** dans le coin supérieur gauche de la page.
    
    :::image type="content" source="media/app-registration-1.png" alt-text="Montre l’inscription d’application dans le portail Azure.":::

4. Sur la page suivante, vous pouvez remplir le formulaire et terminer la création de l’application.

5. Chargez le fichier .cer du certificat dans le portail de l’application Azure Active Directory.

    - Si vous le souhaitez, vous pouvez également tirer parti de la [fonctionnalité de notification Key Vault Event Grid](https://azure.microsoft.com/updates/azure-key-vault-event-grid-integration-is-now-available/) pour charger le certificat.  

6. Accordez les autorisations list/get au secret d’application Azure Active Directory dans Key Vault :
    - Si vous utilisez la préversion RBAC, recherchez le nom de l’application AAD que vous avez créée et attribuez-lui le rôle Utilisateur de secrets de Key Vault (préversion).
    - Si vous utilisez des stratégies d’accès au coffre, attribuez les autorisations **Secret-Get** à l’application AAD que vous avez créée. Pour plus d’informations, consultez [Attribuer des stratégies d’accès](../key-vault/general/assign-access-policy-portal.md).

7. Installez la première version des certificats créés à la première étape et l’extension de machine virtuelle Key Vault à l’aide du modèle ARM, comme indiqué ci-dessous :

    ```json
        {
       "osProfile":{
          "secrets":[
             {
                "sourceVault":{
                   "id":"[parameters('sourceVaultValue')]"
                },
                "vaultCertificates":[
                   {
                      "certificateUrl":"[parameters('bootstrpCertificateUrlValue')]"
                   }
                ]
             }
          ]
       }{
          "name":"KVVMExtensionForPaaS",
          "properties":{
             "type":"KeyVaultForPaaS",
             "autoUpgradeMinorVersion":true,
             "typeHandlerVersion":"1.0",
             "publisher":"Microsoft.Azure.KeyVault",
             "settings":{
                "secretsManagementSettings":{
                   "pollingIntervalInS":"3600",
                   "certificateStoreName":"My",
                   "certificateStoreLocation":"LocalMachine",
                   "linkOnRenewal":false,
                   "requireInitialSync":false, 
                   "observedCertificates":"[parameters('keyVaultObservedCertificates']"
                },
                "authenticationSettings":{
                   "clientId":"Your AAD app ID",
                   "clientCertificateSubjectName":"Your boot strap certificate subject name [Do not include the 'CN=' in the subject name]"
                }
             }
          }
       }
    ```
    Vous devrez peut-être spécifier le magasin de certificats pour le certificat bootstrap dans fichier ServiceDefinition.csdef comme ci-dessous :
    
    ```xml
        <Certificates>
                 <Certificate name="bootstrapcert" storeLocation="LocalMachine" storeName="My" />
        </Certificates> 
    ```

## <a name="next-steps"></a>Étapes suivantes
Améliorez votre déploiement en [activant la supervision dans Cloud Services (prise en charge étendue)](enable-alerts.md)