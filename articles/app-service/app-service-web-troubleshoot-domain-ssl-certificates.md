---
title: Résoudre les problèmes de domaines et de certificats SSL dans les applications web Azure | Microsoft Docs
description: Résoudre les problèmes de domaines et de certificats SSL dans les applications web Azure
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
ms.date: 04/18/2018
ms.author: genli
ms.openlocfilehash: ba21475b771f1688c0a3f2f34c8d961fba5cd182
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Résoudre les problèmes de domaines et de certificats SSL dans les applications web Azure

Cet article répertorie les problèmes courants que vous pouvez rencontrer lorsque vous configurez un certificat de domaine ou SSL pour vos applications web Azure. Il décrit également les causes possibles et les solutions de ces problèmes.

Si vous avez besoin d'aide supplémentaire concernant n'importe quel point de cet article, contactez les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.

## <a name="certificate-problems"></a>Problèmes de certificat

### <a name="unable-to-add-bind-ssl-certificate-to-a-web-app"></a>Impossible d’ajouter un certificat de liaison SSL à une application web 

### <a name="symptom"></a>Symptôme

Si vous ajoutez une liaison SSL, le message d’erreur suivant s’affiche :

**Échec de l’ajout d’une liaison SSL. Impossible de définir le certificat pour l’adresse IP virtuelle existante, car une autre adresse IP virtuelle utilise déjà ce certificat.**

### <a name="cause"></a>Cause :

Ce problème peut se produire si vous disposez de plusieurs liaisons SSL basées sur IP pour une même adresse IP dans plusieurs applications web. Par exemple, l’application web A dispose du protocole SSL basé sur IP avec un ancien certificat. L’application web B dispose du protocole SSL basé sur IP avec un nouveau certificat pour une même adresse IP. Si vous mettez à jour la liaison SSL des applications web avec le nouveau certificat, cette opération échoue avec cette erreur, car une même adresse IP est utilisée pour une autre application. 

### <a name="solution"></a>Solution 

Pour corriger ce problème, utilisez l’une des méthodes suivantes :

- Supprimez la liaison SSL basée sur IP dans l’application web qui utilise l’ancien certificat. 
- Créez une liaison SSL basée sur IP qui utilise le nouveau certificat.

### <a name="unable-to-delete-a-certificate"></a>Impossible de supprimer un certificat 

### <a name="symptom"></a>Symptôme

Lorsque vous essayez de supprimer un certificat, le message d’erreur suivant s’affiche :

**Unable to delete the certificate because it is currently being used in an SSL binding. (Impossible de supprimer le certificat, car il est actuellement utilisé dans une liaison SSL.) The SSL binding must be removed before you can delete the certificate. (Vous devez supprimer la liaison SSL pour pouvoir supprimer le certificat.)**

### <a name="cause"></a>Cause :

Ce problème peut se produire si le certificat est utilisé par une autre application web.

### <a name="solution"></a>Solution

Supprimez la liaison SSL pour ce certificat dans les applications web, puis essayez de supprimer le certificat. Si vous ne pouvez toujours pas supprimer le certificat, effacez le cache du navigateur Internet, puis rouvrez le portail Azure dans une nouvelle fenêtre de navigateur. Essayez alors de supprimer le certificat.

### <a name="unable-to-purchase-an-app-service-certificate"></a>Impossible d’acheter un certificat App Service 

### <a name="symptom"></a>Symptôme
Vous ne pouvez pas acheter un [certificat App Service](./web-sites-purchase-ssl-web-site.md) dans le portail Azure.

### <a name="cause-and-solution"></a>Cause et solution
Ce problème peut se produire pour l’une des raisons suivantes :

- Le plan App Service est « Gratuit » ou « Partagé ». Nous ne prenons pas en charge le protocole SSL pour ces niveaux tarifaires. 

    **Solution** : mettez à niveau le plan App Service de l’application web vers le niveau « Standard ».

- L’abonnement est dépourvu d’une carte de crédit valide.

    **Solution** : ajoutez une carte de crédit à votre abonnement. 

- L’abonnement ne prend pas en charge l’achat d’un certificat App Service comme Microsoft Student.  

    **Solution** : mettez à niveau votre abonnement. 

- L’abonnement a atteint la limite maximale des achats autorisés pour un abonnement.

    **Solution** : les certificats App Service présentent une limite de 10 achats de certificats pour les types d’abonnement EA et Paiement à l’utilisation. Pour les autres types d’abonnement, la limite est fixée à 3. Pour augmenter la limite, contactez le [support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Le certificat App Service a été marqué comme une fraude. Vous recevez le message d’erreur suivant : « Your certificate has been flagged for possible fraud (Votre certificat a été marqué comme une éventuelle fraude). La demande est en cours de révision. Si le certificat n’est pas utilisable dans les 24 heures, consultez le support technique d’Azure. »

    **Solution** : si le certificat est marqué comme Fraude et que le problème n’a pas été résolu après 24 heures, procédez comme suit :

    1. Connectez-vous au [portail Azure](https://portal.azure.com).
    2. Accédez à **App Service Certificates**, puis sélectionnez le certificat.
    3. Sélectionnez **Configuration du certificat** > **Étape 2 : Vérifier** > **Vérification du domaine**. Cette opération envoie une notification par e-mail au fournisseur de certificats Azure pour résoudre le problème.

## <a name="domain-problems"></a>Problèmes liés aux domaines

### <a name="purchased-ssl-certificate-for-wrong-domain"></a>Achat d’un certificat SSL pour un domaine incorrect

### <a name="symptom"></a>Symptôme

Vous avez acheté un certificat App Service pour un domaine incorrect, que vous ne pouvez pas mettre à jour pour utiliser le domaine approprié.

### <a name="solution"></a>Solution

- Supprimez ce certificat, puis achetez-en un nouveau.
- Si le certificat en cours qui utilise le domaine incorrect est à l’état « Émis », il vous sera également facturé. Les certificats App Service ne sont pas remboursables, mais vous pouvez contacter le [support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour voir s’il existe d’autres options. 

### <a name="app-service-certificate-was-renewed-but-still-shows-the-old-certificate"></a>Le certificat App Service a été renouvelé, mais affiche toujours l’ancien certificat 

### <a name="symptom"></a>Symptôme

Le certificat App Service a été renouvelé, mais l’application web qui l’utilise dispose toujours de l’ancienne version. En outre, vous avez reçu un avertissement indiquant que le protocole HTTPS est requis.

### <a name="cause"></a>Cause : 
Le service d’application web exécute un travail en arrière-plan toutes les huit heures et synchronise la ressource de certificat si des modifications sont effectuées. Par conséquent, lorsque vous assurez la rotation ou la mise à jour d’un certificat, il arrive que l’application récupère toujours l’ancien certificat et non le certificat qui vient d’être mis à jour. Cela est dû au fait que le travail de synchronisation de la ressource de certificat n’a pas encore été exécuté. 
 
### <a name="solution"></a>Solution

Vous pouvez forcer une synchronisation du certificat :

1. Connectez-vous au [portail Azure](https://portal.azure.com). Sélectionnez **App Service Certificates**, puis le certificat.
2. Cliquez sur **Recréer la clé et synchroniser**, puis sur **Synchroniser**. Cette opération prend un certain temps. 
3. Lorsque la synchronisation est terminée, la notification suivante s’affiche : « Successfully updated all the resources with the latest certificate » (Mise à jour réussie de toutes les ressources avec le dernier certificat).

### <a name="domain-verification-is-not-working"></a>La vérification du domaine ne fonctionne pas 

### <a name="symptom"></a>Symptôme 
Le certificat App Service requiert une vérification du domaine avant que le certificat ne soit prêt à être utilisé. Le processus échoue lorsque vous cliquez sur **Vérifier**.

### <a name="solution"></a>Solution
Vérifiez manuellement votre domaine en ajoutant un enregistrement TXT :
 
1.  Accédez au fournisseur de service de noms de domaine (DNS) qui héberge votre nom de domaine.
2.  Ajoutez un enregistrement TXT pour votre domaine qui utilise la valeur du jeton de domaine affiché dans le portail Azure. 

Veuillez patienter quelques minutes pendant la propagation DNS, puis cliquez sur le bouton **Actualiser** pour déclencher la vérification. 

L’autre méthode de vérification manuelle est la « méthode Page web HTML » qui peut être utilisée pour permettre à l’autorité de certification de vérifier la propriété du domaine pour lequel le certificat est émis.

1.  Créez un fichier HTML nommé {Jeton de vérification du domaine}.html. 
2.  Le contenu de ce fichier doit être la valeur du jeton de vérification du domaine.
3.  Chargez ce fichier à la racine du serveur web qui héberge votre domaine.
4.  Cliquez sur **Actualiser** pour vérifier l’état du certificat. Cette vérification peut prendre quelques minutes.

Par exemple, si vous achetez un certificat standard pour azure.com avec le jeton de vérification du domaine « 1234abcd », une requête web effectuée auprès de http://azure.com/1234abcd.html doit retourner 1234abcd. 

> [!IMPORTANT]
> Une commande de certificat a 15 jours seulement pour terminer l’opération de vérification du domaine. Après 15 jours, le certificat est refusé par l’autorité de certification et il ne vous est pas facturé. Dans ce cas, supprimez ce certificat, puis réessayez.
>
> 

### <a name="unable-to-purchase-a-domain"></a>Impossible d’acheter un domaine

### <a name="symptom"></a>Symptôme
Vous ne pouvez pas acheter un domaine d’application web ou de domaine App Service dans le portail Azure.

### <a name="cause-and-solution"></a>Cause et solution

Ce problème se produit pour l’une des raisons suivantes :

- Aucune carte de crédit dans l’abonnement Azure ou carte de crédit non valide.

    **Solution** : ajoutez une carte de crédit valide à votre abonnement si vous en êtes dépourvu.

- Si vous n’êtes pas propriétaire de l’abonnement, vous n’êtes peut-être pas autorisé à acheter le domaine.

    **Solution** : [ajoutez le rôle de propriétaire](../billing/billing-add-change-azure-subscription-administrator.md) à votre compte ou prenez contact avec l’administrateur de l’abonnement pour obtenir les autorisations permettant d’acheter un domaine.
- Vous avez atteint la limite d’achat de domaines pour votre abonnement. La limite actuelle est fixée à 20.

    **Solution** : pour demander une augmentation de la limite, contactez le [support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Votre type d’abonnement Azure ne prend pas en charge l’achat d’un domaine App Service.

    **Solution** : mettez à niveau votre abonnement Azure vers d’autres types d’abonnement, par exemple un abonnement Paiement à l’utilisation.

### <a name="unable-to-add-a-hostname-to-web-app"></a>Impossible d’ajouter un nom d’hôte à l’application web 

### <a name="symptom"></a>Symptôme

Lorsque vous ajoutez un nom d’hôte, le processus ne parvient pas à valider le domaine ni à le vérifier.

### <a name="cause"></a>Cause : 

Ce problème se produit pour l’une des raisons suivantes :

- Vous n’êtes pas autorisé à ajouter un nom d’hôte.

    **Solution** : vérifiez auprès de l’administrateur de l’abonnement que vous disposez d’une autorisation permettant d’ajouter un nom d’hôte.
- La propriété du domaine n’a pas pu être vérifiée.

    **Solution** : vérifiez si votre enregistrement CNAME ou A est configuré correctement. Pour mapper un domaine personnalisé à une application web, créez un enregistrement CNAME ou un enregistrement A. Si vous souhaitez utiliser le domaine racine, vous devez utiliser des enregistrements TXT et A :

    |Type d’enregistrement|Host|Pointe vers|
    |------|------|-----|
    |A|@|Adresse IP de l’application web|
    |TXT|@|< nom_application >. azurewebsites.net|
    |CNAME|www|< nom_application >. azurewebsites.net|

### <a name="dns-cannot-be-resolved"></a>Impossible de résoudre DNS

### <a name="symptom"></a>Symptôme

Vous recevez un message d’erreur « The DNS record could not be located » (Impossible de localiser l’enregistrement DNS).

### <a name="cause"></a>Cause :
Ce problème se produit pour l’une des raisons suivantes :

- La période de durée de vie (TTL) n’est pas arrivée à expiration. Vérifiez la configuration DNS de votre domaine pour déterminer la valeur TTL, puis attendez l’expiration de la période.
- La configuration DNS est incorrecte.

### <a name="solution"></a>Solution
- Patientez 48 heures le temps que ce problème se résolve.
- Si vous pouvez modifier le paramètre TTL de votre configuration DNS, changez la valeur et définissez-la sur 5 minutes pour voir si cela résout le problème.
- Utilisez [WhatsmyDNS.net](https://www.whatsmydns.net/) pour vérifier que votre domaine pointe vers l’adresse IP de l’application web. Si tel n’est pas le cas, configurez l’enregistrement A sur l’adresse IP appropriée de l’application web.

### <a name="restore-a-deleted-domain"></a>Restaurer un domaine supprimé 

### <a name="symptom"></a>Symptôme
Votre domaine n’est plus visible dans le portail Azure.

### <a name="cause"></a>Cause : 
Le domaine a peut-être été supprimé accidentellement par le propriétaire de l’abonnement.

### <a name="solution"></a>Solution
Si votre domaine a été supprimé il y a moins de sept jours, le domaine n’a pas encore commencé le processus de suppression. Dans ce cas, vous pouvez acheter à nouveau le même domaine sur le portail Azure sous le même abonnement (veillez à taper le nom de domaine exact dans la zone de recherche). Ce domaine ne vous sera pas facturé une nouvelle fois. Si le domaine a été supprimé il y a plus de sept jours, contactez le [support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour restaurer le domaine.

### <a name="custom-domain-returns-404-or-site-inaccessible"></a>Le domaine personnalisé retourne l’erreur 404 ou « site inaccessible » 

### <a name="symptom"></a>Symptôme

Si vous accédez au site à l’aide du nom de domaine personnalisé, le message d’erreur suivant s’affiche :

**Error 404-Web app not found.** (Erreur 404 : application web introuvable.)


### <a name="cause-and-solution"></a>Cause et solution

**Cause 1** 

Un enregistrement CNAME ou A est manquant dans le domaine personnalisé que vous avez configuré. 

**Solution pour la cause 1**

- Si vous avez ajouté un enregistrement A, assurez-vous qu’un enregistrement TXT est également ajouté. Pour plus d’informations, consultez la section [Créer l’enregistrement A](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Si vous n’avez pas à utiliser le domaine racine de votre application web, il est recommandé d’utiliser un enregistrement CNAME au lieu d’un enregistrement A.
- N’utilisez pas à la fois un enregistrement CNAME et un enregistrement A pour un même domaine. Cela peut provoquer un conflit et empêcher la résolution du domaine. 

**Cause 2** 

Le navigateur Internet met peut-être en cache l’ancienne adresse IP de votre domaine. 

**Solution pour la cause 2**

Effacez le cache du navigateur. Pour les appareils Windows, vous pouvez exécuter la commande `ipconfig /flushdns`. Utilisez [WhatsmyDNS.net](https://www.whatsmydns.net/) pour vérifier que votre domaine pointe vers l’adresse IP de l’application web. 

### <a name="unable-to-add-subdomain"></a>Impossible d’ajouter un sous-domaine 

### <a name="symptom"></a>Symptôme

Vous ne pouvez pas ajouter un nouveau nom d’hôte à une application web pour affecter un sous-domaine.

### <a name="solution"></a>Solution

- Vérifiez auprès de l’administrateur de l’abonnement que vous disposez des autorisations permettant d’ajouter un nom d’hôte à l’application web.
- Si vous avez besoin d’autres sous-domaines, nous vous recommandons de modifier l’hébergement de domaine pour le définir sur Azure DNS. Si vous utilisez Azure DNS, vous pouvez ajouter 500 noms d’hôte à votre application web. Pour plus d’informations, consultez le blog [Mapping a custom subdomain to an Azure Website](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/) (Mappage d’un sous-domaine personnalisé à un site web Azure).











 


















