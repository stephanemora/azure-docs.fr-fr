---
title: Configurer ADDS LDAP sur TLS pour Azure NetApp Files | Microsoft Docs
description: Décrit comment configurer ADDS LDAP sur TLS pour Azure NetApp Files, y compris la gestion des certificats d’autorité de certification racine.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/05/2021
ms.author: b-juche
ms.openlocfilehash: e9a1b9148948319ebf05e2bcc9f5f306b2a21ec0
ms.sourcegitcommit: 89c4843ec85d1baea248e81724781d55bed86417
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108795970"
---
# <a name="configure-adds-ldap-over-tls-for-azure-netapp-files"></a>Configurer ADDS LDAP sur TLS pour Azure NetApp Files

Vous pouvez utiliser LDAP sur TLS pour sécuriser les communications entre un volume Azure NetApp Files et le serveur Active Directory LDAP.  Vous pouvez activer LDAP sur TLS pour les volumes NFS, SMB et double protocole d’Azure NetApp Files.  

## <a name="considerations"></a>Considérations

* LDAP sur TLS ne doit pas être activé si vous utilisez Azure Active Directory Domain Services (AADDS). AADDS utilise LDAPS (port 636) pour sécuriser le trafic LDAP au lieu d’utiliser LDAP sur TLS (port 389).  

## <a name="register-the-ldap-over-tls-feature"></a>Inscrire la fonctionnalité LDAP sur TLS 

La fonctionnalité LDAP sur TLS est actuellement disponible en préversion. Si vous utilisez cette fonctionnalité pour la première fois, inscrivez-la avant de l’utiliser.

1.  Inscrivez la fonctionnalité :

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapOverTls
    ```

2. Vérifiez l’état d’inscription de la fonctionnalité : 

    > [!NOTE]
    > **RegistrationState** peut rester à l’état `Registering` jusqu’à 60 minutes avant de passer à l’état `Registered`. Avant de continuer, attendez que l’état soit `Registered`.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapOverTls
    ```
Vous pouvez également utiliser les [commandes Azure CLI](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` et `az feature show` pour inscrire la fonctionnalité et afficher l’état de l’inscription. 

## <a name="generate-and-export-root-ca-certificate"></a>Générer et exporter un certificat d’autorité de certification racine 

Si vous n’avez pas de certificat d’autorité de certification racine, vous devez en générer un et l’exporter afin de pouvoir l’utiliser avec l’authentification LDAP sur TLS. 

1. Suivez [Installer l’autorité de certification](/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) pour installer et configurer l’autorité de certification AD DS. 

2. Suivez [Afficher les certificats avec le composant logiciel enfichable MMC](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) pour utiliser le composant logiciel enfichable MMC et l’outil Gestionnaire de certificats.  
    Utilisez le composant logiciel enfichable Gestionnaire de certificats pour localiser le certificat racine ou d’émission pour l’appareil local. Vous devez exécuter les commandes du composant logiciel enfichable Gestion des certificats à partir de l’un des paramètres suivants :  
    * Un client Windows joint au domaine et sur lequel le certificat racine est installé 
    * un autre ordinateur du domaine contenant le certificat racine.  

3. Exportez le certificat d’autorité de certification racine.  
    Les certificats d’autorité de certification racine peuvent être exportés à partir du répertoire Personnel ou Autorités de certification racines de confiance, comme indiqué dans les exemples suivants :   
    ![capture d’écran montrant les certificats personnels](../media/azure-netapp-files/personal-certificates.png)   
    ![capture d’écran montrant les autorités de certification racines de confiance](../media/azure-netapp-files/trusted-root-certification-authorities.png)    

    Assurez-vous que le certificat est exporté au format X.509 encodé en base 64 (.CER) : 

    ![Assistant Exportation de certificat](../media/azure-netapp-files/certificate-export-wizard.png)

## <a name="enable-ldap-over-tls-and-upload-root-ca-certificate"></a>Activer LDAP sur TLS et charger le certificat d’autorité de certification racine 

1. Accédez au compte NetApp utilisé pour le volume, puis cliquez sur **Connexions Active Directory**. Ensuite, cliquez sur **Joindre** pour créer une connexion Active Directory ou cliquez sur **Modifier** pour modifier une connexion Active Directory existante.  

2. Dans la fenêtre **Joindre Active Directory** ou **Modifier Active Directory** qui s’affiche, cochez la case **LDAP sur TLS** pour activer la fonctionnalité LDAP sur TLS sur le volume. Cliquez ensuite sur **Certificat d’autorité de certification racine de serveur** et chargez le [certificat d’autorité de certification racine généré](#generate-and-export-root-ca-certificate) à utiliser pour LDAP sur TLS.  

    ![Capture d’écran montrant l’option LDAP sur TLS](../media/azure-netapp-files/ldap-over-tls-option.png)

    Assurez-vous que le nom de l’autorité de certification peut être résolu par DNS. Ce nom figure dans le champ « Délivré par » ou « Émetteur » sur le certificat :  

    ![Capture d’écran montrant les informations du certificat](../media/azure-netapp-files/certificate-information.png)

Si vous avez chargé un certificat non valide et que vous avez des configurations AD, des volumes SMB ou des volumes Kerberos existants, une erreur similaire à ce qui suit se produit :

`Error updating Active Directory settings The LDAP client configuration "ldapUserMappingConfig" for Vservers is an invalid configuration.`

Pour résoudre l’erreur, chargez un certificat d’autorité de certification racine valide dans votre compte NetApp, conformément aux exigences du serveur LDAP Windows Active Directory pour l’authentification LDAP.

## <a name="next-steps"></a>Étapes suivantes  

* [Créer un volume NFS pour Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Créer un volume SMB pour Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Créer un volume double protocole pour Azure NetApp Files](create-volumes-dual-protocol.md)

