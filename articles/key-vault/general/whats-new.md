---
title: Nouveautés d’Azure Key Vault
description: Mises à jour récemment apportées à Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 194b0122987d4fdc5d100112c60006588d28f96c
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826921"
---
# <a name="whats-new-for-azure-key-vault"></a>Nouveautés d’Azure Key Vault

Voici les nouveautés d’Azure Key Vault. Les nouvelles fonctionnalités et améliorations sont également annoncées sur le [canal des mises à jour d’Azure Key Vault](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="july-2020"></a>Juillet 2020

> [!WARNING]
> Ces deux mises à jour sont susceptibles d’avoir un impact sur les implémentations d’Azure Key Vault.

### <a name="soft-delete-on-by-default"></a>Suppression réversible activée par défaut

D’ici la fin de l’année 2020, la **suppression réversible sera activée par défaut pour tous les coffres de clés** (nouveaux et préexistants). Pour plus d’informations sur ce changement potentiellement cassant et pour connaître les étapes permettant de trouver les coffres de clés affectés et de les mettre à jour au préalable, consultez l’article [La suppression réversible sera activée sur tous les coffres de clés](soft-delete-change.md). 

### <a name="azure-tls-certificate-changes"></a>Changements des certificats Azure TLS  

Microsoft met à jour les services Azure pour qu’ils utilisent des certificats TLS issus d’un autre ensemble d’autorités de certification racines. Ce changement est dû au fait que les certificats d’autorité de certification actuels [ne sont pas conformes à l’une des exigences de base du CA/Browser Forum](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951).

### <a name="when-will-this-change-happen"></a>Quand cette modification aura-t-elle lieu ?

- Les services [Azure Active Directory](/azure/active-directory) (Azure AD) ont commencé cette transition le 7 juillet 2020.
- Tous les points de terminaison Azure TLS/SSL nouvellement créés contiennent des certificats mis à jour, liés aux nouvelles autorités de certification racines.
- Les points de terminaison Azure existants feront l’objet d’une transition par phases qui commencera le 13 août 2020 et s’achèvera le 26 octobre 2020.
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) et [DPS](/azure/iot-dps/) resteront dans l’autorité de certification racine Baltimore CyberTrust, mais leurs autorités de certification intermédiaires changeront. Pour plus d’informations, consultez le billet de blog [Azure IoT TLS: Changes are coming! (…and why you care)](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).
- [Stockage Azure](/azure/storage) restera dans l’autorité de certification racine Baltimore CyberTrust mais ses autorités de certification intermédiaires changeront. Pour plus d’informations, consultez le billet de blog [Azure Storage TLS: Changes are coming! (…and why you care)](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).

> [!IMPORTANT]
> Les clients devront peut-être mettre à jour leurs applications après cette modification pour éviter les échecs de connectivité quand ils tentent de se connecter aux services Azure.

### <a name="what-is-changing"></a>Qu’est-ce qui change ?

Aujourd’hui, la plupart des certificats TLS utilisés par les services Azure sont liés à l’autorité de certification racine suivante :

| Nom commun de l’autorité de certification | Empreinte (SHA1) |
|--|--|
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Les certificats TLS utilisés par les services Azure seront liés à l’une des autorités de certification racines suivantes :

| Nom commun de l’autorité de certification | Empreinte (SHA1) |
|--|--|
| [DigiCert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST Root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft EV ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20EV%20ECC%20Root%20Certificate%20Authority%202017.crt) | 6b1937abfd64e1e40daf2262a27857c015d6228d |

### <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Quand pourrai-je supprimer l’ancienne empreinte intermédiaire ?

Les certificats d’autorité de certification actuels ne seront *pas* révoqués avant le 15 février 2021. Après cette date, vous pourrez supprimer les anciennes empreintes de votre code.

Si cette date change, vous serez informé de la nouvelle date de révocation.

### <a name="will-this-change-affect-me"></a>Ce changement m’affecte-t-il ? 

Nous pensons que **la plupart des clients Azure ne seront pas impactés**.  Toutefois, votre application peut être impactée si elle spécifie explicitement une liste d’autorités de certification acceptables. Cette pratique est appelée « épinglage de certificat ».

Voici quelques méthodes permettant de déterminer si votre application est impactée :

- Recherchez dans votre code source l’empreinte, le nom commun et les autres propriétés de certificat de toutes les autorités de certification Microsoft IT TLS mentionnées [ici](https://www.microsoft.com/pki/mscorp/cps/default.htm). S’il existe une correspondance, votre application sera impactée. Pour résoudre ce problème, mettez à jour le code source en incluant les nouvelles autorités de certification. Une bonne pratique consiste à vous assurer que les autorités de certification peuvent être ajoutées ou changées dans un court délai. Les réglementations du secteur exigent que les certificats d’autorité de certification soient remplacés sous sept jours. Les clients qui s’appuient sur l’épinglage de certificats doivent donc réagir rapidement.

- Si vous disposez d’une application qui s’intègre à des API Azure ou d’autres services Azure et que vous ne savez pas si elle utilise l’épinglage de certificat, contactez le fournisseur de l’application.

- Les différents systèmes d’exploitation et runtimes de langage qui communiquent avec les services Azure peuvent nécessiter des étapes supplémentaires pour générer correctement la chaîne de certificats avec ces nouvelles racines :
    - **Linux** : de nombreuses distributions vous obligent à ajouter des autorités de certification à /etc/ssl/certs. Pour obtenir des instructions spécifiques, reportez-vous à la documentation de la distribution.
    - **Java** : assurez-vous que le magasin de clés Java contient les autorités de certification listées ci-dessus.
    - **Windows exécuté dans des environnements déconnectés** : pour les systèmes qui s’exécutent dans des environnements déconnectés, de nouvelles racines doivent être ajoutées au magasin d’autorités de certification racines de confiance et les intermédiaires au magasin d’autorités de certification intermédiaires.
    - **Android** : consultez la documentation de votre appareil et de votre version d’Android.
    - **Autres appareils, en particulier IoT** : contactez le fabricant de l’appareil.

- Si vous disposez d’un environnement dans lequel les règles de pare-feu sont configurées pour autoriser les appels sortants uniquement vers des emplacements de téléchargement de liste de révocation de certificats et/ou de vérification de protocole OCSP (Online Certificate Status Protocol) spécifiques, vous devez autoriser les URL correspondantes suivantes :

    - http://crl3&#46;digicert&#46;com
    - http://crl4&#46;digicert&#46;com
    - http://ocsp&#46;digicert&#46;com
    - http://www&#46;d-trust&#46;net
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com

## <a name="june-2020"></a>Juin 2020

Azure Monitor pour Key Vault est désormais en préversion.  Azure Monitor permet une supervision complète de vos coffres de clés en fournissant une vue unifiée des informations relatives aux requêtes, au niveau de performance, aux échecs et à la latence de Key Vault. Pour plus d’informations, consultez [Azure Monitor pour Key Vault (préversion).](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="may-2020"></a>Mai 2020

BYOK (« Bring Your Own Key ») pour Key Vault est désormais en disponibilité générale. Consultez la [spécification BYOK d’Azure Key Vault](../keys/byok-specification.md) et découvrez comment [importer des clés protégées par HSM dans Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Mars 2020

Points de terminaison privés désormais disponibles en préversion. Le service Azure Private Link vous permet d’accéder à Azure Key Vault et aux services clients/partenaires hébergés sur Azure via un point de terminaison privé de votre réseau virtuel.  Découvrez comment [intégrer Key Vault avec Azure Private Link](private-link-service.md).

## <a name="2019"></a>2019

- Mise en production des kits SDK Azure Key Vault de nouvelle génération. Pour obtenir des exemples de leur utilisation, consultez les guides de démarrage rapide des secrets Azure Key Vault pour [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md) et [Node.js](../secrets/quick-create-node.md)
- Nouvelles stratégies Azure pour la gestion des certificats de coffres de clés. Consultez les [définitions intégrées d’Azure Policy pour Key Vault](../policy-samples.md).
- L’extension de machine virtuelle Azure Key Vault est désormais en disponibilité générale.  Consultez [Extension de machine virtuelle Key Vault pour Linux](../../virtual-machines/extensions/key-vault-linux.md) et [Extension de machine virtuelle Key Vault pour Windows](../../virtual-machines/extensions/key-vault-windows.md).
- La gestion des secrets basée sur les événements pour Azure Key Vault est désormais disponible dans Azure Event Grid. Pour plus d’informations, consultez [le schéma Event Grid des événements dans Azure Key Vault] (../../event-grid/event-schema-key-vault.md], puis découvrez comment [recevoir et répondre aux notifications du coffre de clés avec Azure Event Grid](event-grid-tutorial.md).

## <a name="2018"></a>2018

Nouvelles fonctionnalités et intégrations mises en production cette année :

- Intégration avec Azure Functions. Pour obtenir un exemple de scénario montrant comment tirer profit d’[Azure Functions](../../azure-functions/index.yml) dans les opérations liées au coffre de clés, consultez [Automatiser la rotation d’un secret](../secrets/tutorial-rotation.md). 
- [Intégration avec Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Ainsi, Azure Databricks prend désormais en charge deux types d’étendue de secrets : Azure Key Vault et Databricks. Pour plus d’informations, consultez [Créer une étendue de secrets Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Points de terminaison de service de réseau virtuel pour Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Nouvelles fonctionnalités mises en production cette année :

- Clés de compte de stockage managé. Fonctionnalité des clés de compte de stockage ajoutée pour faciliter l’intégration avec le service Stockage Azure. Pour plus d’informations, consultez la rubrique relative à la [vue d’ensemble des clés de compte de stockage géré](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).
- Suppression réversible. Fonctionnalité de suppression réversible ajoutée pour améliorer la protection des données de vos coffres de clés et objets de coffre de clés. Pour plus d’informations, consultez la rubrique [Vue d’ensemble de la suppression réversible d’Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015"></a>2015

Nouvelles fonctionnalités mises en production cette année :
- Gestion de certificats. Ajoutée en tant que fonctionnalité à la version GA 2015-06-01 le 26 septembre 2016.

La disponibilité générale (version 2015-06-01) a été annoncée le 24 juin 2015. Les modifications suivantes ont été apportées à cette version : 
- Suppression d’une clé - champ « use » supprimé.
- Obtention des informations sur une clé - champ « use » supprimé.
- Importation d’une clé dans un coffre - champ « use » supprimé.
- Restauration d’une clé - champ « use » supprimé.     
- Remplacement de « RSA_OAEP » par « RSA-OAEP » dans les algorithmes RSA. Voir [Présentation des clés, des secrets et des certificats](about-keys-secrets-certificates.md).    
 
La deuxième préversion (version 2015-02-01-preview) a été annoncée le 20 avril 2015. Pour plus d’informations, consultez le billet de blog [REST API Update](https://docs.microsoft.com/archive/blogs/kv/rest-api-update) (Mise à jour de l’API REST). Les tâches suivantes ont été mises à jour :
 
- Liste des clés d’un coffre (ajout de la prise en charge de la pagination à l’opération).
- Liste des versions d’une clé (ajout de l’opération pour dresser la liste des versions d’une clé).  
- Liste des secrets d’un coffre (ajout de la prise en charge de la pagination).
- Liste des versions d’un secret (ajout pour dresser la liste des versions d’un secret).  
- Toutes les opérations (ajout de timestamps de création/mise à jour aux attributs).  
- Création d’un secret (ajout d’un élément Content-Type aux secrets).
- Création d’une clé (ajout d’étiquettes en tant qu’informations facultatives).
- Création d’un secret (ajout d’étiquettes en tant qu’informations facultatives).
- Mise à jour d’une clé (ajout d’étiquettes en tant qu’informations facultatives).
- Mise à jour d’un secret (ajout d’étiquettes en tant qu’informations facultatives).
- Modification de la taille maximale des secrets de 10 Ko à 25 Ko. Voir [Présentation des clés, des secrets et des certificats](about-keys-secrets-certificates.md).    

## <a name="2014"></a>2014
 
La première préversion (version 2014-12-08-preview) a été annoncée le 8 janvier 2015.  

## <a name="next-steps"></a>Étapes suivantes

Si vous avez d’autres questions, contactez-nous par le biais du [support](https://azure.microsoft.com/support/options/).  
