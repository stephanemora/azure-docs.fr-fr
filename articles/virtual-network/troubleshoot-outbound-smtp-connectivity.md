---
title: Résoudre les problèmes de connectivité SMTP sortante dans Azure | Microsoft Docs
description: Découvrez la méthode recommandée pour l’envoi des e-mails et la manière de résoudre les problèmes de connectivité SMTP sortante dans Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: genli
ms.openlocfilehash: c28790b2ef423a3d0f996d7c6030b04198756eb1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102607609"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Résoudre les problèmes de connectivité SMTP sortante dans Azure

Depuis le 15 novembre 2017, les e-mails sortants envoyés directement à des domaines externes (comme outlook.com et gmail.com) à partir d’une machine virtuelle sont accessibles uniquement à certains types d’abonnements dans Azure. Les connexions SMTP sortantes qui utilisent le port TCP 25 ont été bloquées. (Le port 25 est principalement utilisé pour la remise d’e-mails non authentifiés.)

Ce changement de comportement s’applique uniquement aux abonnements et aux déploiements qui ont été créés après le 15 novembre 2017.

## <a name="recommended-method-of-sending-email"></a>Méthode recommandée de l’envoi d’e-mails

Nous vous recommandons d’utiliser des services de relais SMTP authentifiés pour envoyer des e-mails à partir de machines virtuelles Azure ou d’Azure App Service. (Ces services de relais se connectent généralement via le port TCP 587, mais ils prennent en charge d'autres ports.) Ces services sont utilisés pour maintenir la réputation de l’IP ou du domaine afin de minimiser le risque que des fournisseurs de messagerie tiers ne rejettent les messages. [SendGrid](https://sendgrid.com/partners/azure/) est un service de relais SMTP, mais il en existe d’autres. Vous pouvez également utiliser un service de relais SMTP sécurisé qui s’exécute localement.

L’utilisation de ces services de remise d’e-mails n’est pas limitée dans Azure, quel que soit le type d’abonnement.

## <a name="enterprise-agreement"></a>Contrat Entreprise

Pour les utilisateurs Contrat Entreprise Azure, il n’y a aucune modification de la capacité technique à envoyer un e-mail sans utiliser un relais authentifié. Les utilisateurs Contrat Entreprise nouveaux et existants peuvent essayer la remise d’e-mails sortants à partir de machines virtuelles Azure directement à des fournisseurs de messagerie externes sans aucune restriction de la plateforme Azure. Il n’existe aucune garantie que les fournisseurs de messagerie acceptent les e-mails entrants de la part d’un utilisateur donné. Toutefois, la plateforme Azure ne bloque pas les tentatives de remise des machines virtuelles au sein des abonnements Contrat Entreprise. Vous devrez travailler directement avec les fournisseurs de messagerie pour corriger tout problème de remise de message ou de filtrage du courrier indésirable qui implique des fournisseurs spécifiques.

## <a name="pay-as-you-go"></a>Paiement à l’utilisation

Si vous vous êtes inscrit avant le 15 novembre 2017, pour un abonnement Paiement à l’utilisation, il n’y aura aucune modification de votre capacité technique à essayer la remise d’e-mails sortants. Vous pourrez toujours essayer la remise d’e-mails sortants à partir de machines virtuelles Azure directement depuis ces abonnements à des fournisseurs de messagerie externes sans aucune restriction de la plateforme Azure. Là encore, il n’existe aucune garantie que les fournisseurs de messagerie acceptent les e-mails entrants de la part d’un utilisateur donné. Les utilisateurs devront travailler directement avec les fournisseurs de messagerie pour corriger tout problème de remise de message ou de filtrage du courrier indésirable qui implique des fournisseurs spécifiques.

Pour les abonnements Paiement à l’utilisation créés après le 15 novembre 2017, des restrictions techniques bloquent les e-mails envoyés directement à partir de machines virtuelles se trouvant dans les abonnements. Si vous souhaitez être en mesure d’envoyer des e-mails à partir de machines virtuelles Azure directement à des fournisseurs de messagerie externes (sans utiliser de relais SMTP authentifié) et si vous avez un compte en règle avec un historique de paiement, vous pouvez demander la suppression de la restriction. Vous pouvez le faire dans la section **Connectivité** du panneau **Diagnostiquer et résoudre** pour une ressource Réseau virtuel Microsoft Azure dans le portail Azure. Si votre demande est acceptée, votre abonnement sera activé ou vous recevrez des instructions pour les étapes suivantes. 

Lorsqu’un abonnement Paiement à l’utilisation est exempté et que les machines virtuelles sont arrêtées et redémarrées dans le portail Azure, toutes les machines virtuelles de cet abonnement sont exemptées. L’exemption s’applique uniquement à l’abonnement demandé et au trafic de machine virtuelle acheminé directement vers Internet.

> [!NOTE]
> Microsoft se réserve le droit de révoquer ces exemptions s’il s’avère qu’une violation des conditions d’utilisation du service s’est produite.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Pass Azure, Azure dans Open, Azure for Education, Microsoft Azure for Students, Visual Studio et Essai gratuit

Si vous avez créé l’un des types d’abonnements suivants après le 15 novembre 2017, vous aurez des restrictions techniques qui bloquent les e-mails envoyés depuis les machines virtuelles de l’abonnement directement aux fournisseurs de messagerie :
- MSDN
- Pass Azure
- Azure dans Open
- Education
- Azure for Students
- Version d’évaluation gratuite
- Tout abonnement Visual Studio  

Les restrictions sont mises en place pour éviter les abus. Aucune demande de suppression de ces restrictions ne sera accordée.

Si vous utilisez ces types d’abonnements, nous vous invitons à utiliser des services de relais SMTP, comme indiqué plus haut dans cet article, ou à changer votre type d’abonnement.

## <a name="cloud-solution-provider"></a>Fournisseur de solutions cloud

Si vous utilisez des ressources Azure par le biais d’un fournisseur de solutions cloud, vous pouvez effectuer une demande de suppression de la restriction dans la section **Connectivité** du panneau **Diagnostiquer et résoudre** pour une ressource de réseau virtuel dans le portail Azure. Si votre demande est acceptée, votre abonnement sera activé ou vous recevrez des instructions pour les étapes suivantes.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network, BizSpark Plus ou Azure Sponsorship

Pour les abonnements des types suivants qui ont été créés après le 15 novembre 2017, des restrictions techniques bloquent les e-mails envoyés directement à partir de machines virtuelles se trouvant dans les abonnements :

- Microsoft Partner Network (MPN)
- BizSpark Plus
- Azure Sponsorship

Si vous souhaitez pouvoir envoyer des e-mails à partir de machines virtuelles Azure directement à des fournisseurs de messagerie externes (sans utiliser de relais SMTP authentifié), vous pouvez en faire la demande en ouvrant une demande de support en utilisant le type de problème suivant : **Technique** > **Réseau virtuel** > **Connectivité** > **Impossible d’envoyer un e-mail (SMTP/Port 25)** . Veillez à ajouter des informations supplémentaires sur la raison pour laquelle votre déploiement doit envoyer des e-mails directement aux fournisseurs de messagerie au lieu d’utiliser un relais authentifié. Les demandes seront revues et approuvées à la discrétion de Microsoft. Les demandes sont accordées uniquement après l’exécution de contrôles antifraude supplémentaires. 

Lorsqu’un abonnement est exempté et que les machines virtuelles ont été arrêtées et redémarrées dans le portail Azure, toutes les machines virtuelles de cet abonnement sont exemptées. L’exemption s’applique uniquement à l’abonnement demandé et au trafic de machine virtuelle acheminé directement vers Internet.

## <a name="changing-subscription-type"></a>Modification du type d’abonnement

Si vous modifiez le type d’abonnement ou si votre abonnement Fournisseur de solutions Azure Cloud ou de paiement à l’utilisation est approuvé, vous devez arrêter, libérer, puis redémarrer votre machine virtuelle pour que la nouvelle stratégie prenne effet. De même, si vous avez un type d’abonnement autorisé par défaut et que vous passez à un type d’abonnement non autorisé, le port 25 peut être bloqué en raison des modifications apportées au déploiement.

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez encore besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour résoudre rapidement votre problème. Utilisez ce type de problème : **Technique** > **Réseau virtuel** > **Connectivité** > **Impossible d’envoyer un e-mail (SMTP/Port 25)** .
