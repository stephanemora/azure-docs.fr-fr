---
title: Vue d’ensemble de l’authentification mutuelle sur Azure Application Gateway
description: Cet article est une vue d’ensemble de l’authentification mutuelle sur Application Gateway.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: caya
ms.openlocfilehash: a63697c2c9c32dfb48e77248a5e7a601677b8b3e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230868"
---
# <a name="overview-of-mutual-authentication-with-application-gateway-preview"></a>Vue d’ensemble de l’authentification mutuelle avec Application Gateway (préversion)

L’authentification mutuelle, ou authentification client, permet à Application Gateway d’authentifier les demandes d’envoi du client. En général, seul le client authentifie Application Gateway ; l’authentification mutuelle permet à la fois au client et à Application Gateway de s’authentifier mutuellement. 

> [!NOTE]
> Nous vous recommandons d’utiliser TLS 1.2 avec l’authentification mutuelle, car TLS 1.2 sera obligatoire à l’avenir. 

## <a name="mutual-authentication"></a>Authentification mutuelle

Application Gateway prend en charge l’authentification mutuelle basée sur les certificats, qui vous permet de charger un ou plusieurs certificats d’autorité de certification de client approuvé dans Application Gateway. La passerelle utilisera ce certificat pour authentifier le client envoyant une demande à la passerelle. Avec l’augmentation des cas d’utilisation d’IoT et les exigences de sécurité accrues dans divers secteurs, l’authentification mutuelle vous permet de gérer et contrôler les clients qui peuvent communiquer avec votre Application Gateway. 

Pour configurer l’authentification mutuelle, vous devez télécharger un certificat d’autorité de certification de client approuvé dans le cadre de la partie authentification du client d’un profil SSL. Le profil SSL devra ensuite être associé à un écouteur afin de terminer la configuration de l’authentification mutuelle. Il doit toujours s’agir d’un certificat d’autorité de certification racine dans le certificat client que vous téléchargez. Vous pouvez également télécharger une chaîne de certificats, mais la chaîne doit inclure un certificat d’autorité de certification racine en plus du nombre de certificats d’autorité de certification intermédiaires que vous souhaitez. 

Par exemple, si votre certificat client contient un certificat d’autorité de certification racine, plusieurs certificats d’autorité de certification intermédiaires et un certificat feuille, assurez-vous que le certificat de l’autorité de certification racine et tous les certificats intermédiaires de l’autorité de certification sont téléchargés sur Application Gateway dans un fichier. Pour plus d’informations sur l’extraction d’un certificat d’autorité de certification de client approuvé, consultez [Comment extraire des certificats d’autorité de certification de client approuvé](./mutual-authentication-certificate-management.md).

Si vous téléchargez une chaîne de certificats avec une autorité de certification racine et des certificats d’autorité de certification intermédiaires, la chaîne de certificats doit être téléchargée en tant que fichier PEM ou CER sur la passerelle. 

> [!NOTE] 
> L’authentification mutuelle est disponible uniquement sur les références SKU Standard_v2 et WAF_v2. 

### <a name="certificates-supported-for-mutual-authentication"></a>Certificats pris en charge pour l’authentification mutuelle

Application Gateway prend en charge les types de certificat suivants :

- Certificat délivré par une autorité de certification (CA) : Un certificat CA est un certificat numérique émis par une autorité de certification (CA).
- Certificats d’autorité de certification auto-signés : Les navigateurs clients ne font pas confiance à ces certificats et avertiront l’utilisateur que le certificat du service virtuel ne fait pas partie d’une chaîne d’approbation. Les certificats d’autorité de certification auto-signés conviennent pour les tests ou les environnements dans lesquels les administrateurs contrôlent les clients et peuvent ignorer en toute sécurité les alertes de sécurité du navigateur. Les charges de travail de production ne doivent jamais utiliser de certificats d’autorité de certification auto-signés.

Pour plus d’informations sur la configuration de l’authentification mutuelle, consultez la section [Configurer l’authentification mutuelle avec Application Gateway](./mutual-authentication-portal.md).

> [!IMPORTANT]
> Veillez à charger l’intégralité de la chaîne de certificats de l’autorité de certification de client approuvé dans Application Gateway lors de l’utilisation de l’authentification mutuelle. 

## <a name="additional-client-authentication-validation"></a>Validation d’authentification de client supplémentaire

### <a name="verify-client-certificate-dn"></a>Vérifier le DN du certificat client

Vous avez la possibilité de vérifier l’émetteur immédiat du certificat client et d’autoriser uniquement Application Gateway à approuver cet émetteur. Cette option est désactivée par défaut, mais vous pouvez l’activer via le portail, PowerShell ou Azure CLI. 

Si vous choisissez de permettre à Application Gateway de vérifier l’émetteur immédiat du certificat client, voici comment déterminer le nom unique de l’émetteur du certificat client extrait des certificats téléchargés. 
* **Scénario 1 :** La chaîne de certificats comprend : le certificat racine - le certificat intermédiaire - le certificat feuille 
    * Le nom d’objet du *certificat intermédiaire* correspond à ce qu’Application Gateway extraira en tant que DN de l’émetteur du certificat client et sera vérifié. 
* **Scénario 2 :** La chaîne de certificats comprend : le certificat racine - le certificat intermédiaire 1 - le certificat intermédiaire 2 - le certificat feuille
    * Le nom de sujet du *certificat intermédiaire 2* sera extrait en tant que nom unique de l’émetteur du certificat client et sera vérifié. 
* **Scénario 3 :** La chaîne de certificats comprend : le certificat racine - le certificat feuille 
    * Le nom de sujet du *certificat racine* sera extrait et utilisé comme DN de l’émetteur du certificat client.
* **Scénario 4 :** Plusieurs chaînes de certificats de la même longueur dans le même fichier. La chaîne 1 comprend : certificat racine - certificat intermédiaire 1 - certificat feuille. La chaîne 2 comprend : certificat racine - certificat intermédiaire 2 - certificat feuille. 
    * Le nom de sujet du *certificat intermédiaire 1* sera extrait comme DN de l’émetteur du certificat client.  
* **Scénario 5 :** Plusieurs chaînes de certificats de longueurs différentes dans le même fichier. La chaîne 1 comprend : certificat racine - certificat intermédiaire 1 - certificat feuille. La chaîne 2 comprend : certificat racine - certificat intermédiaire 2 - certificat intermédiaire 3 - certificat feuille. 
    * Le nom de sujet du *certificat intermédiaire 3* sera extrait comme DN de l’émetteur du certificat client. 

> [!IMPORTANT]
> Nous vous recommandons de charger uniquement une chaîne de certificats par fichier. Cela est particulièrement important si vous activez la vérification du DN du certificat client. En chargeant plusieurs chaînes de certificats dans un même fichier, vous vous retrouverez dans le scénario quatre ou cinq et risquez de rencontrer des problèmes plus tard lorsque le certificat client présenté ne correspond pas au DN de l’émetteur du certificat client Application Gateway extrait des chaînes. 

Pour plus d’informations sur l’extraction de chaînes de certificats d’autorité de certification de client approuvé, consultez [Comment extraire des chaînes de certificats d’autorité de certification de client approuvé](./mutual-authentication-certificate-management.md).

## <a name="server-variables"></a>Variables de serveur 

Avec l’authentification mutuelle, il existe des variables serveur supplémentaires que vous pouvez utiliser pour transmettre des informations sur le certificat client aux serveurs principaux situés derrière Application Gateway. Pour plus d’informations sur les variables serveur disponibles et comment les utiliser, consultez [Variables serveur](./rewrite-http-headers-url.md#mutual-authentication-server-variables-preview).

## <a name="next-steps"></a>Étapes suivantes

Après avoir découvert l’authentification mutuelle, accédez à [Configurer Application Gateway avec l’authentification mutuelle dans PowerShell](./mutual-authentication-powershell.md) pour créer une instance Application Gateway avec l’authentification mutuelle. 

