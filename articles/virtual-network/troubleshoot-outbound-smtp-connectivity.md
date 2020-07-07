---
title: Résoudre les problèmes de connectivité SMTP sortante dans Azure | Microsoft Docs
description: Découvrez comment résoudre les problèmes de connectivité SMTP sortante dans Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 148a8bf7626d8b8ee687658990fdaf2fce9f7de7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83590092"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Résoudre les problèmes de connectivité SMTP sortante dans Azure

À partir du 15 novembre 2017, les e-mails sortants envoyés directement à des domaines externes (comme outlook.com et gmail.com) à partir d’une machine virtuelle sont accessibles uniquement à certains types d’abonnements dans Microsoft Azure. Les connexions SMTP sortantes qui utilisent le port TCP 25 ont été bloquées. (Le port 25 est principalement utilisé pour la remise d’e-mails non authentifiés.)

Ce changement de comportement s’applique uniquement aux nouveaux abonnements et aux nouveaux déploiements depuis le 15 novembre 2017.

## <a name="recommended-method-of-sending-email"></a>Méthode recommandée de l’envoi d’e-mails
Nous vous recommandons d’utiliser les services de relais SMTP authentifiés (qui se connectent généralement via le port TCP 587 ou 443, mais prennent en charge d’autres ports aussi) pour envoyer un e-mail à partir de machines virtuelles Azure ou d’Azure App Services. Ces services sont utilisés pour maintenir la réputation de l’IP ou du domaine afin de minimiser le risque que des fournisseurs de messagerie tiers ne rejettent le message. Ces services de relais SMTP incluent, mais sans s’y limiter, [SendGrid](https://sendgrid.com/partners/azure/). Il est également possible que vous ayez un service de relais SMTP sécurisé en cours d’exécution en local que vous pouvez utiliser.

L’utilisation de ces services de remise d’e-mails n’est pas limitée dans Azure, quel que soit le type d’abonnement.

## <a name="enterprise-agreement"></a>Contrat Entreprise
Pour les utilisateurs Contrat Entreprise Azure, il n’y a aucune modification de la capacité technique à envoyer un e-mail sans utiliser un relais authentifié. Les utilisateurs Contrat Entreprise nouveaux et existants peuvent essayer la remise d’e-mails sortants à partir de machines virtuelles Azure directement à des fournisseurs de messagerie externes sans aucune restriction de la plateforme Azure. Bien qu’il n’est pas garanti que les fournisseurs de messagerie accepteront les e-mails entrants de n’importe quel utilisateur donné, les tentatives de remise ne seront pas bloquées par la plateforme Azure pour les machines virtuelles au sein d’abonnements Contrat Entreprise. Vous devrez travailler directement avec les fournisseurs de messagerie pour résoudre les problèmes de remise de message ou de filtrage du courrier indésirable qui impliquent des fournisseurs spécifiques.

## <a name="pay-as-you-go"></a>Pay-As-You-Go
Si vous vous êtes inscrit avant le 15 novembre 2017 aux offres d’abonnement Paiement à l’utilisation ou Microsoft Partner Network, il n’y aura aucune modification de la capacité technique à essayer la remise d’e-mails sortants. Vous pourrez toujours essayer la remise d’e-mails sortants à partir de machines virtuelles Azure directement depuis ces abonnements à des fournisseurs de messagerie externes sans aucune restriction de la plateforme Azure. Là encore, il n’est pas garanti que les fournisseurs de messagerie accepteront les e-mails entrants de n’importe quel utilisateur donné, et les utilisateurs devront travailler directement avec les fournisseurs de messagerie pour résoudre les problèmes de remise de message ou de filtrage du courrier indésirable qui impliquent des fournisseurs spécifiques.

Pour les abonnements de type Paiement à l’utilisation ou Microsoft Partner Network créés après le 15 novembre 2017, des restrictions techniques bloquent les e-mails envoyés directement à partir de machines virtuelles à l’intérieur de ces abonnements. Si vous voulez disposer de la capacité à envoyer des e-mails directement depuis des machines virtuelles Azure vers des fournisseurs de messagerie externes (sans utiliser des relais SMTP authentifiés), vous pouvez effectuer une demande de retrait de la restriction. Microsoft examinera et approuvera les demandes à sa meilleure convenance, et n’y accèdera qu’après avoir effectué des vérifications antifraude supplémentaires. Pour effectuer une requête, ouvrez un cas de support en utilisant le type de problème suivant : **Technique** > **Réseau virtuel** > **Connectivité** > **Impossible d’envoyer un e-mail (SMTP/Port 25)** . N’oubliez pas d’ajouter plus d’informations sur la raison pour laquelle votre déploiement doit envoyer des e-mails directement aux fournisseurs de messagerie au lieu d’utiliser un relais authentifié.

Lorsqu’un abonnement Paiement à l’utilisation ou Microsoft Partner Network est exempté et que les machines virtuelles ont été « arrêtées et démarrées » à partir du portail Azure, toutes les machines virtuelles au sein de cet abonnement seront exemptées à l’avenir. L’exemption est applicable uniquement à l’abonnement demandé, et s’applique uniquement au trafic de machine virtuelle routé directement vers Internet. Le routage du trafic du port 25 par le biais de services Azure PaaS tels que le [Pare-feu Azure](https://azure.microsoft.com/services/azure-firewall/) n’est pas pris en charge.

> [!NOTE]
> Microsoft se réserve le droit de révoquer cette exemption s’il s’avère qu’une violation des conditions d’utilisation du service s’est produite.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Pass Azure, Azure dans Open, Éducation, BizSpark et Essai gratuit
Si vous avez créé un abonnement MSDN, Pass Azure, Azure en licence Open, Éducation, BizSpark, Azure Sponsorship, Azure Étudiant, Essai gratuit ou tout abonnement Visual Studio après le 15 novembre 2017, des restrictions techniques bloqueront les e-mails envoyés à partir de machines virtuelles dans ces abonnements directement aux fournisseurs de courrier. Les restrictions sont là pour éviter les abus. Aucune demande pour supprimer cette restriction ne sera accordée.

Si vous utilisez ces types d’abonnements, vous êtes invité à utiliser des services de relais SMTP, comme indiqué plus haut dans cet article, ou à changer votre type d’abonnement.

## <a name="cloud-service-provider-csp"></a>Fournisseur de services cloud (CSP)

Si vous utilisez des ressources Azure via un CSP, vous pouvez demander à celui-ci d’introduire une demande de déblocage d’exemption auprès de Microsoft à votre place si un relais SMTP sécurisé est inutilisable.

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème en utilisant le type de problème suivant : **Gestion des abonnements** Type de problème : **Requête d’activation du port 25 de flux d’e-mails**.
