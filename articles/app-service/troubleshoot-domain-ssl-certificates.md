---
title: Résoudre les problèmes de domaines et de certificats SSL - Azure App Service | Microsoft Docs
description: Résoudre les problèmes de domaines et de certificats SSL dans Azure App Service
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: b367251e6799031ed96058d21e606446e14636c7
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407604"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-app-service"></a>Résoudre les problèmes de domaines et de certificats SSL dans Azure App Service

Cet article répertorie les problèmes courants que vous pouvez rencontrer lorsque vous configurez un certificat de domaine ou SSL pour vos applications web dans Azure App Service. Il décrit également les causes possibles et les solutions à ces problèmes.

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/), puis sélectionnez **Obtenir un support**.

## <a name="certificate-problems"></a>Problèmes de certificat

### <a name="you-cant-add-an-ssl-certificate-binding-to-an-app"></a>Vous n’arrivez pas à ajouter une liaison de certificat SSL à une application 

#### <a name="symptom"></a>Symptôme

Si vous ajoutez une liaison SSL, le message d’erreur suivant s’affiche :

« Échec de l’ajout d’une liaison SSL. Impossible de définir le certificat pour l’adresse IP virtuelle existante, car une autre adresse IP virtuelle utilise déjà ce certificat. »

#### <a name="cause"></a>Cause :

Ce problème peut se produire si vous disposez de plusieurs liaisons SSL basées sur IP pour une même adresse IP dans plusieurs applications. Par exemple, l’application A dispose du protocole SSL basé sur IP avec un ancien certificat. L’application B dispose du protocole SSL basé sur IP avec un nouveau certificat pour une même adresse IP. Si vous mettez à jour la liaison SSL des applications avec le nouveau certificat, cette opération échoue avec cette erreur, car la même adresse IP est utilisée pour une autre application. 

#### <a name="solution"></a>Solution 

Pour corriger ce problème, utilisez l’une des méthodes suivantes :

- Supprimez la liaison SSL basée sur IP dans l’application qui utilise l’ancien certificat. 
- Créez une liaison SSL basée sur IP qui utilise le nouveau certificat.

### <a name="you-cant-delete-a-certificate"></a>Vous n’arrivez pas à supprimer un certificat 

#### <a name="symptom"></a>Symptôme

Lorsque vous essayez de supprimer un certificat, le message d’erreur suivant s’affiche :

« Impossible de supprimer le certificat car il est en cours d'utilisation dans une liaison SSL. Vous devez supprimer la liaison SSL avant de pouvoir supprimer le certificat. »

#### <a name="cause"></a>Cause :

Ce problème peut se produire si le certificat est utilisé par une autre application.

#### <a name="solution"></a>Solution

Supprimez la liaison SSL pour ce certificat dans les applications, puis essayez de supprimer le certificat. Si vous ne pouvez toujours pas supprimer le certificat, effacez le cache du navigateur Internet, puis rouvrez le portail Azure dans une nouvelle fenêtre de navigateur. puis essayez de supprimer le certificat.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Vous n’arrivez pas à acheter un certificat App Service 

#### <a name="symptom"></a>Symptôme
Vous ne pouvez pas acheter un [certificat Azure App Service](./web-sites-purchase-ssl-web-site.md) sur le portail Azure.

#### <a name="cause-and-solution"></a>Cause et solution
Ce problème peut se produire pour l’une des raisons suivantes :

- Le plan App Service est Gratuit ou Partagé. Ces niveaux tarifaires ne prennent pas en charge l’utilisation du protocole SSL. 

    **Solution**: mettez à niveau le plan App Service de l’application vers le niveau Standard.

- L’abonnement n’est pas associé à une carte de crédit valide.

    **Solution**: ajoutez une carte de crédit à votre abonnement. 

- L’abonnement ne prend pas en charge l’achat d’un certificat App Service comme Microsoft Student.  

    **Solution**: Mettez à niveau votre abonnement. 

- L’abonnement a atteint le nombre maximal d’achats autorisés pour un abonnement.

    **Solution**: les certificats App Service présentent une limite de 10 achats de certificats pour les types d’abonnement Contrat Entreprise et Paiement à l’utilisation. Pour les autres types d’abonnement, la limite est fixée à 3. Pour augmenter la limite, contactez le [support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Le certificat App Service a été marqué comme une fraude. Vous recevez le message d’erreur suivant : « Votre certificat a été signalé comme étant une fraude potentielle. La demande est en cours de révision. Si le certificat n’est pas utilisable dans les 24 heures, contactez le Support Azure. »

    **Solution**: si le certificat est marqué comme Fraude et que le problème n’a pas été résolu après 24 heures, effectuez les étapes suivantes :

    1. Connectez-vous au [Portail Azure](https://portal.azure.com).
    2. Accédez à **App Service Certificates**, puis sélectionnez le certificat.
    3. Sélectionnez **Configuration du certificat** > **Étape 2 : Vérifier** > **Vérification du domaine**. Cette étape envoie une notification par e-mail au fournisseur de certificats Azure pour résoudre le problème.

## <a name="custom-domain-problems"></a>Problèmes de domaine personnalisé

### <a name="a-custom-domain-returns-a-404-error"></a>Un domaine personnalisé retourne une erreur 404 

#### <a name="symptom"></a>Symptôme

Si vous accédez au site à l’aide du nom de domaine personnalisé, le message d’erreur suivant s’affiche :

« Erreur 404 : application web introuvable »

#### <a name="cause-and-solution"></a>Cause et solution

**Cause 1** 

Un enregistrement CNAME ou A est manquant dans le domaine personnalisé que vous avez configuré. 

**Solution pour la cause 1**

- Si vous avez ajouté un enregistrement A, vérifiez qu’un enregistrement TXT a également été ajouté. Pour plus d’informations, consultez [Créer un enregistrement A](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Si vous n’avez pas à utiliser le domaine racine de votre application, nous vous recommandons d’utiliser un enregistrement CNAME au lieu d’un enregistrement A.
- N’utilisez pas à la fois un enregistrement CNAME et un enregistrement A pour un même domaine. Ce problème peut provoquer un conflit et empêcher que le domaine en cours de résolution. 

**Cause 2** 

Le navigateur Internet met peut-être en cache l’ancienne adresse IP de votre domaine. 

**Solution pour la cause 2**

Effacez le cache du navigateur. Pour les appareils Windows, vous pouvez exécuter la commande `ipconfig /flushdns`. Utilisez [WhatsmyDNS.net](https://www.whatsmydns.net/) pour vérifier que votre domaine pointe vers l’adresse IP de l’application. 

### <a name="you-cant-add-a-subdomain"></a>Vous n’arrivez pas à ajouter un sous-domaine 

#### <a name="symptom"></a>Symptôme

Vous n’arrivez pas à ajouter un nouveau nom d’hôte à une application pour affecter un sous-domaine.

#### <a name="solution"></a>Solution

- Vérifiez auprès de l’administrateur de l’abonnement que vous disposez des autorisations permettant d’ajouter un nom d’hôte à l’application.
- Si vous avez besoin de plusieurs sous-domaines, nous vous recommandons de modifier l’hébergement de domaine à Azure Service DNS (Domain Name). Azure DNS vous permet d’ajouter 500 noms d’hôte à votre application. Pour plus d’informations, consultez le blog [Mapping a custom subdomain to an Azure Website](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).

### <a name="dns-cant-be-resolved"></a>Résolution du DNS impossible

#### <a name="symptom"></a>Symptôme

Vous recevez le message d’erreur suivant :

« L’enregistrement DNS est introuvable. »

#### <a name="cause"></a>Cause :
Ce problème se produit pour l’une des raisons suivantes :

- La période de durée de vie (TTL) n’est pas arrivée à expiration. Vérifiez la configuration DNS de votre domaine pour déterminer la valeur TTL, puis attendez l’expiration de la période.
- La configuration DNS est incorrecte.

#### <a name="solution"></a>Solution
- Patientez 48 heures le temps que ce problème se résolve.
- Si vous pouvez modifier le paramètre TTL de votre configuration DNS, changez la valeur et définissez-la sur 5 minutes pour voir si cela résout le problème.
- Utilisez [WhatsmyDNS.net](https://www.whatsmydns.net/) pour vérifier que votre domaine pointe vers l’adresse IP de l’application. Si ce n’est pas le cas, configurez l’enregistrement A sur l’adresse IP appropriée de l’application.

### <a name="you-need-to-restore-a-deleted-domain"></a>Vous devez restaurer un domaine supprimé 

#### <a name="symptom"></a>Symptôme
Votre domaine n’est plus visible dans le portail Azure.

#### <a name="cause"></a>Cause : 
Le propriétaire du domaine l’a peut-être supprimé accidentellement.

#### <a name="solution"></a>Solution
Si votre domaine a été supprimé il y a moins de sept jours, le processus de suppression n’a pas encore démarré pour celui-ci. Dans ce cas, vous pouvez racheter le même domaine sur le portail Azure, sous le même abonnement (tapez le nom de domaine exact dans la zone de recherche). Ce domaine ne vous sera pas refacturé. Si le domaine a été supprimé il y a plus de sept jours, contactez [prise en charge Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour vous aider à la restauration du domaine.

## <a name="domain-problems"></a>Problèmes liés aux domaines

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Vous avez acheté un certificat SSL pour un autre domaine

#### <a name="symptom"></a>Symptôme

Vous avez acheté un certificat App Service pour un domaine autre que le vôtre. Vous ne pouvez pas mettre à jour le certificat de sorte qu’il utilise le bon domaine.

#### <a name="solution"></a>Solution

Supprimez ce certificat, puis achetez-en un nouveau.

Si le certificat actuel qui utilise le mauvais domaine est à l’état « Émis », il vous sera également facturé. Les certificats App Service ne sont pas remboursables, mais vous pouvez contacter le [support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour voir s’il existe d’autres options. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Le certificat App Service a été renouvelé, mais l’application affiche toujours l’ancien certificat 

#### <a name="symptom"></a>Symptôme

Le certificat App Service a été renouvelé, mais l’application qui l’utilise dispose toujours de l’ancienne version. En outre, vous avez reçu un avertissement indiquant que le protocole HTTPS est requis.

#### <a name="cause"></a>Cause : 
Azure App Service exécute un travail en arrière-plan toutes les huit heures et synchronise la ressource de certificat si des modifications sont effectuées. Lorsque vous assurez la rotation ou la mise à jour d’un certificat, il arrive que l’application récupère toujours l’ancien certificat et non le certificat qui vient d’être mis à jour. Cela est dû au fait que le travail de synchronisation de la ressource de certificat n’a pas encore été exécuté. 
 
#### <a name="solution"></a>Solution

Vous pouvez forcer une synchronisation du certificat :

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **App Service Certificates**, puis le certificat.
2. Sélectionnez **Recréer la clé et synchroniser**, puis **Synchroniser**. La synchronisation prend un certain temps. 
3. Une fois la synchronisation terminée, la notification suivante s’affiche : « Toutes les ressources ont été mises à jour avec succès avec le dernier certificat. »

### <a name="domain-verification-is-not-working"></a>La vérification du domaine ne fonctionne pas 

#### <a name="symptom"></a>Symptôme 
Le certificat App Service requiert une vérification du domaine avant que le certificat ne soit prêt à être utilisé. Le processus échoue lorsque vous sélectionnez **Vérifier**.

#### <a name="solution"></a>Solution
Vérifiez manuellement votre domaine en ajoutant un enregistrement TXT :
 
1.  Accédez au fournisseur de service de noms de domaine (DNS) qui héberge votre nom de domaine.
2.  Ajoutez à votre domaine un enregistrement TXT qui utilise la valeur du jeton de domaine affiché dans le portail Azure. 

Patientez quelques minutes pendant la propagation DNS, puis sélectionnez le bouton **Actualiser** pour déclencher la vérification. 

En guise d’alternative, vous pouvez utiliser la méthode de la page web HTML pour vérifier manuellement votre domaine. Cette méthode peut être utilisée pour permettre à l'autorité de certification de vérifier la propriété du domaine pour lequel le certificat est émis.

1.  Créez un fichier HTML nommé {Jeton de vérification du domaine}.html. Le contenu de ce fichier doit être la valeur du jeton de vérification du domaine.
3.  Chargez ce fichier à la racine du serveur web qui héberge votre domaine.
4.  Sélectionnez **Actualiser** pour vérifier l’état du certificat. Cette vérification peut prendre quelques minutes.

Par exemple, si vous achetez un certificat standard pour azure.com avec le jeton de vérification du domaine 1234abcd, une requête web effectuée auprès de https://azure.com/1234abcd.html doit retourner 1234abcd. 

> [!IMPORTANT]
> Une commande de certificat a 15 jours seulement pour terminer l’opération de vérification du domaine. Après 15 jours, l’autorité de certification refuse le certificat, et celui-ci ne vous est pas facturé. Dans ce cas, supprimez ce certificat, puis réessayez.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Vous n’arrivez pas à acheter un domaine

#### <a name="symptom"></a>Symptôme
Vous ne parvenez pas à acheter un domaine App Service dans le portail Azure.

#### <a name="cause-and-solution"></a>Cause et solution

Ce problème se produit pour l’une des raisons suivantes :

- Aucune carte de crédit n’est associée à l’abonnement Azure ou la carte n’est pas valide.

    **Solution**: ajoutez une carte de crédit à votre abonnement.

- Vous n’êtes pas le propriétaire de l’abonnement, vous n’êtes donc pas autorisé à acheter un domaine.

    **Solution**: [assignez le rôle Propriétaire](../role-based-access-control/role-assignments-portal.md) à votre compte. Vous pouvez également contacter l’administrateur de l’abonnement pour obtenir l’autorisation d’acheter un domaine.
- Vous avez atteint la limite d’achat de domaines pour votre abonnement. La limite actuelle est fixée à 20.

    **Solution**: pour demander une augmentation de la limite, contactez le [support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Votre type d’abonnement Azure ne prend pas en charge l’achat d’un domaine App Service.

    **Solution**: mettez à niveau votre abonnement Azure vers un autre type d’abonnement, par exemple un abonnement Paiement à l’utilisation.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Vous n’arrivez pas à ajouter un nom d’hôte à une application 

#### <a name="symptom"></a>Symptôme

Lorsque vous ajoutez un nom d’hôte, le processus ne parvient pas à valider le domaine, ni à le vérifier.

#### <a name="cause"></a>Cause : 

Ce problème se produit pour l’une des raisons suivantes :

- Vous n’êtes pas autorisé à ajouter un nom d’hôte.

    **Solution**: demandez à l’administrateur d’abonnement l’autorisation d’ajouter un nom d’hôte.
- La propriété du domaine n’a pas pu être vérifiée.

    **Solution**: vérifiez que votre enregistrement CNAME ou A est configuré correctement. Pour mapper un domaine personnalisé à une application, créez un enregistrement CNAME ou un enregistrement A. Si vous souhaitez utiliser un domaine racine, vous devez utiliser les enregistrements TXT et A :

    |Type d’enregistrement|Host|Pointe vers|
    |------|------|-----|
    |A|@|Adresse IP d’une application|
    |TXT|@|< nom_application >. azurewebsites.net|
    |CNAME|www|< nom_application >. azurewebsites.net|

## <a name="faq"></a>Forum Aux Questions

**Je dois configurer mon domaine personnalisé pour mon site Web une fois l’acheter ?**

Lorsque vous achetez un domaine à partir du portail Azure, l’application de Service de l’application est automatiquement configurée pour utiliser ce domaine personnalisé. Vous n’êtes pas obligé d’effectuer des étapes supplémentaires. Pour plus d’informations, regardez [aide libre-service d’Azure App Service : Ajouter un nom de domaine personnalisé](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) sur Channel 9.

**Puis-je utiliser un domaine acheté dans le portail Azure pour pointer vers une machine virtuelle Azure à la place ?**

Oui, vous pouvez pointer le domaine à une machine virtuelle. Pour plus d’informations, consultez [Use Azure DNS to provide custom domain settings for an Azure service](../dns/dns-custom-domain.md) (Utiliser DNS Azure pour fournir des paramètres de domaine personnalisé pour un service Azure).

**Mon domaine est hébergé par GoDaddy ou Azure DNS ?**

Domaines App Service utilisent GoDaddy pour l’inscription de domaine et Azure DNS pour héberger les domaines. 

**J’ai le renouvellement automatique est activée, mais toujours reçu une notification de renouvellement de mon domaine par courrier électronique. Que dois-je faire ?**

Si vous avez le renouvellement automatique est activé, il est inutile à intervenir. L’e-mail de notification est fournie pour indiquer que le domaine est le point d’expirer et renouveler manuellement le renouvellement automatique n’est pas activé.

**Suis-je facturé pour Azure DNS qui héberge mon domaine ?**

Le coût initial d’achat de domaine s’applique à l’inscription de domaine uniquement. Outre le coût de l’inscription, il existe acquitter des frais pour Azure DNS en fonction de votre utilisation. Pour plus d’informations, consultez [tarification Azure DNS](https://azure.microsoft.com/pricing/details/dns/) pour plus d’informations.

**J’ai acheté mon domaine précédemment à partir du portail Azure et que vous souhaitez déplacer à partir de GoDaddy d’hébergement à l’hébergement Azure DNS. Comment puis-je faire cela ?**

Il n’est pas obligatoire pour migrer vers Azure DNS d’hébergement. Si vous ne souhaitez pas migrer vers Azure DNS, l’expérience de gestion de domaine dans le portail Azure sur fournit des informations sur les étapes nécessaires pour passer à Azure DNS. Si le domaine a été acheté auprès d’App Service, migration à partir de GoDaddy d’hébergement à Azure DNS est une procédure relativement transparente.

**Je souhaite acheter mon domaine à partir du domaine App Service, mais puis-je héberger mon domaine sur GoDaddy au lieu d’Azure DNS ?**

À compter du 24 juillet 2017, les domaines App Service achetés dans le portail sont hébergés sur Azure DNS. Si vous préférez utiliser un autre fournisseur d’hébergement, vous devez accéder à son site Web pour obtenir un solution d’hébergement de domaine.

**Dois-je payer pour la protection des données personnelles de mon domaine ?**

Lorsque vous achetez un domaine via le portail Azure, vous pouvez choisir d’ajouter confidentialité sans coût supplémentaire. Il s’agit d’un des avantages de l’achat de votre domaine via Azure App Service.

**Si vous décidez de ne plus mon domaine, puis-je obtenir mon retour ?**

Lorsque vous achetez un domaine, vous n’êtes pas facturé pour une période de cinq jours, pendant cette période, vous pouvez décider que vous ne souhaitez pas que le domaine. Si vous décidez que vous ne souhaitez pas le domaine dans ce délai de cinq jours, vous n’êtes pas facturé. (les domaines .fr constituent une exception à cela. Si vous achetez un domaine .fr, vous êtes facturé immédiatement et vous ne peut pas être remboursés.)

**Puis-je utiliser le domaine dans une autre application Azure App Service dans mon abonnement ?**

Oui. Lorsque vous accédez à la panneau domaines personnalisés et SSL dans le portail Azure, vous consultez les domaines que vous avez achetées. Vous pouvez configurer votre application pour utiliser un de ces domaines.

**Puis-je transférer un domaine d’un abonnement vers un autre abonnement ?**

Vous pouvez déplacer un domaine vers un autre groupe ressources/abonnement à l’aide du [Move-AzureRmResource](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Move-AzureRmResource?view=azurermps-6.13.0) applet de commande PowerShell.

**Comment puis-je gérer mon domaine personnalisé si je n’ai pas actuellement une application Azure App Service ?**

Vous pouvez gérer votre domaine même si vous n’avez pas une application Web App Service. Domaine peut être utilisé pour les services Azure tels que machine virtuelle, etc. de stockage. Si vous avez l’intention d’utiliser le domaine pour App Service Web Apps, vous devez inclure une application Web qui n’est pas sur le plan App Service gratuits afin de lier le domaine à votre application web.

**Puis-je déplacer une application web avec un domaine personnalisé vers un autre abonnement ou à partir de l’environnement App Service v1 vers V2 ?**

Oui, vous pouvez déplacer votre application web entre des abonnements. Suivez les instructions de [comment déplacer des ressources dans Azure](../azure-resource-manager/resource-group-move-resources.md). Il existe quelques limitations lors du déplacement de l’application web. Pour plus d’informations, consultez [Limitations de déplacement des ressources d’App Service](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations
).

Après le déplacement de l’application web, les liaisons de nom d’hôte des domaines au sein des définition des domaines personnalisés doivent rester les mêmes. Aucune étape supplémentaire est nécessaire pour configurer les liaisons de nom d’hôte.
