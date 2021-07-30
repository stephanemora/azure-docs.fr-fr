---
title: Utiliser Twilio avec les fonctionnalités vocales et SMS (Python) | Microsoft Docs
description: Découvrez comment passer un appel téléphonique et envoyer un SMS avec le service d'API Twilio sur Azure. Exemples de code écrits en Python.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.custom: devx-track-python
ms.openlocfilehash: 6d2f2b9cd145a2472ccc9ed24fd78f30107db710
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110463806"
---
# <a name="use-twilio-for-voice-and-sms-capabilities-in-python"></a>Utiliser Twilio avec les fonctionnalités vocales et SMS dans Python
Cet article présente l'exécution de tâches de programmation courantes avec le service API Twilio sur Azure. Les scénarios couverts comprennent notamment les appels téléphoniques et l'envoi de SMS. 

Pour plus d'informations sur Twilio et sur l'utilisation des fonctionnalités vocales et de SMS de vos applications, consultez la section [Étapes suivantes](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Présentation de Twilio
Twilio permet aux développeurs d’incorporer la voix, la voix sur IP (VoIP) et la messagerie dans des applications. Les développeurs virtualisent toute l'infrastructure nécessaire dans un environnement global dans le cloud, en l'exposant au moyen de la plateforme de l'API de Twilio. Les applications sont faciles à générer et sont évolutives.

Les composants de Twilio incluent :

- **Twilio Voice** : permet à vos applications de passer et de recevoir des appels téléphoniques.
- **Twilio SMS** : permet à vos applications d'envoyer et de recevoir des messages texte.
- **Twilio Client** : permet de passer des appels VoIP à partir d'un téléphone, d'une tablette ou d'un navigateur. Il prend en charge la spécification WebRTC pour la communication en temps réel.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Tarification de Twilio et offres spéciales
Les clients Azure reçoivent une [offre spéciale][special_offer] : 10 $ en crédit Twilio lorsqu’ils mettent à niveau leur compte Twilio. Ce crédit peut être appliqué à toute utilisation de Twilio. Un crédit de 10 $ correspond à l'envoi de 1 000 SMS ou à la réception de 1 000 minutes vocales maximum, en fonction de l'emplacement de votre numéro de téléphone, du message ou de la destination de l'appel.

Twilio est un service de paiement à l'utilisation. Il n’y a pas de frais de configuration et vous pouvez fermer votre compte quand vous le souhaitez. Pour plus d'informations, consultez la page [Tarification de Twilio][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Concepts
L'API Twilio est une API RESTful qui offre des fonctionnalités vocales et de SMS aux applications. Les bibliothèques clientes sont disponibles dans plusieurs langues. Pour obtenir la liste, consultez [Bibliothèques de l’API Twilio][twilio_libraries].

Les éléments les plus importants de l'API Twilio sont les verbes Twilio et TwiML (Twilio Markup Language).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Verbes Twilio
L’API utilise des verbes comme ceux-ci qui indiquent à Twilio ce qu’il faut faire :

* **&lt;Composer&gt;**  : connecte l’appelant à un autre téléphone.
* **&lt;Rassembler&gt;**  : collecte les chiffres numériques entrés sur le clavier du téléphone.
* **&lt;Raccrocher&gt;**  : met fin à un appel.
* **&lt;Suspendre&gt;**  : patiente silencieusement pendant un nombre déterminé de secondes.
* **&lt;Lire&gt;**  : lit un fichier audio.
* **&lt;Mettre en attente&gt;** : ajoute l’appelant à une file d’attente d’appelants.
* **&lt;Enregistrer&gt;**  : Enregistre la voix de l’appelant et retourne l’URL d’un fichier contenant l’enregistrement.
* **&lt;Rediriger&gt;**  : transfère le contrôle d’un appel ou d’un SMS au TwiML à une autre URL.
* **&lt;Rejeter&gt;**  : Refuse un appel entrant sur votre numéro Twilio sans vous facturer.
* **&lt;Dire&gt;** : synthèse vocale lors d’un appel.
* **&lt;Sms&gt;**  : envoie un SMS.

Découvrez les autres verbes et fonctionnalités dans la page [Documentation de Twilio Markup Language][twiml].

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML est un jeu d'instructions XML reposant sur les verbes Twilio qui disent à Twilio la façon de traiter un appel ou un SMS.

À titre d'exemple, le code TwiML suivant convertit le texte **Hello World** en parole :

```xml
<?xml version="1.0" encoding="UTF-8" ?>
  <Response>
    <Say>Hello World</Say>
  </Response>
```

Lorsque votre application appelle [l'API Twilio][twilio_api], l'un des paramètres de l'API est l'URL qui renvoie la réponse TwiML. À des fins de développement, vous pouvez utiliser les URL Twilio pour fournir les réponses TwiML que vos applications utiliseront. Vous pouvez également héberger vos propres URL afin de produire les réponses TwiML, ou encore utiliser l’objet `TwiMLResponse`.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Créer un compte Twilio
Lorsque vous êtes prêt à créer votre compte Twilio, inscrivez-vous sur la page [Essayer Twilio][try_twilio]. Vous pouvez commencer avec un compte gratuit, avant de le mettre à niveau ultérieurement.

Quand vous vous inscrivez pour obtenir un compte Twilio, vous recevez un ID de sécurité (SID) de compte et un jeton d’authentification. Vous aurez besoin des deux pour effectuer des appels d’API Twilio. Pour éviter qu'une personne non autorisée n'accède à votre compte, conservez votre jeton d'authentification en lieu sûr. Vous pouvez consulter votre SID du compte et votre jeton d’authentification dans la [console Twilio][twilio_console], dans les champs intitulés **SID DU COMPTE** et **JETON D’AUTHENTIFICATION**.

## <a name="create-a-python-application"></a><a id="create_app"></a>Créer une application Python
Une application Python qui utilise Twilio et qui s’exécute dans Azure est identique aux autres applications Python qui utilisent Twilio. Bien que les services Twilio soient basés sur REST et puissent être appelés de différentes manières à partir de Python, cet article met l’accent sur l’utilisation des services Twilio avec [la bibliothèque Twilio pour Python à partir de GitHub][twilio_python]. Pour plus d’informations sur l’utilisation de cette bibliothèque, consultez la documentation de la [bibliothèque Python Twilio][twilio_lib_docs].

Tout d’abord, [configurez une nouvelle machine virtuelle Linux Azure][azure_vm_setup] afin qu’elle joue le rôle d’hôte pour votre nouvelle application web Python. Une fois que la machine virtuelle est en cours d’exécution, vous devez exposer votre application sur un port public.

Pour ajouter une règle entrante :
  1. Accédez à la page [Groupe de sécurité réseau][azure_nsg].
  2. Sélectionnez le groupe de sécurité réseau qui correspond à votre machine virtuelle.
  3. Ajoutez des informations sur les **règles sortantes** pour le **port 80**. Veillez à autoriser les appels entrant à partir de n’importe quelle adresse.

Définir l’étiquette du nom DNS :
  1. Accédez à la page [Adresses IP publiques][azure_ips].
  2. Sélectionnez l'adresse IP publique qui correspond à votre machine virtuelle.
  3. Définissez l’information **Étiquette du nom DNS** dans la section **Configuration**. Dans cet exemple, il est similaire à *\<your-domain-label\>.centralus.cloudapp.azure.com*.

Une fois que vous êtes en mesure de vous connecter via SSH à la machine virtuelle, vous pouvez installer l’infrastructure Web de votre choix. Les deux plus connues dans Python sont [Flask](http://flask.pocoo.org/) et [Django](https://www.djangoproject.com). Vous pouvez installer l’une des deux en exécutant simplement la commande `pip install`.

Gardez à l’esprit que nous avons configuré la machine virtuelle pour autoriser le trafic uniquement sur le port 80. Veillez à configurer l’application pour utiliser ce port.

## <a name="configure-your-application-to-use-the-twilio-library"></a><a id="configure_app"></a>Configurer votre application pour utiliser les bibliothèques Twilio
Vous pouvez configurer votre application pour utiliser la bibliothèque Twilio pour Python de deux manières :

* Installez la bibliothèque Twilio pour Python en tant que package Pip à l’aide de la commande suivante :
   
  `$ pip install twilio`

* Téléchargez la [bibliothèque Twilio pour Python à partir de GitHub][twilio_python] et installez-la comme suit :

  `$ python setup.py install`

Une fois que vous avez installé la bibliothèque Twilio pour Python, vous pouvez ensuite l’importer dans vos fichiers Python :

`import twilio`

Pour plus d’informations, consultez [Twilio GitHub readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a name="make-an-outgoing-call"></a><a id="howto_make_call"></a>passer un appel sortant
L’exemple suivant permet d'exécuter un appel sortant. Ce code utilise aussi un site Twilio afin de renvoyer la réponse TwiML. Remplacez les numéros de téléphone `from_number` et `to_number` par vos valeurs. Assurez-vous d’avoir vérifié le numéro de téléphone `from_number` pour votre compte Twilio avant d’exécuter le code.

```python
from urllib.parse import urlencode

# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified.
from_number = "NNNNNNNNNNN"

# The number of the phone receiving call.
to_number = "NNNNNNNNNNN"

# Use the Twilio-provided site for the TwiML response.
url = "https://twimlets.com/message?"

# The phone message text.
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url + urlencode({'Message': message}))
print(call.sid)
```

> [!IMPORTANT]
> Les numéros de téléphone doivent être mis en forme avec le signe plus et l’indicatif du pays. Un exemple est `+16175551212` (format E.164). Twilio accepte également les numéros des États-Unis non mis en forme, tels que `(415) 555-1212` ou `415-555-1212`.

Ce code utilise un site Twilio afin de renvoyer la réponse TwiML. Vous pouvez utiliser à la place votre propre site pour fournir la réponse TwiML. Pour plus d’informations, consultez [Envoi de réponses TwiML à partir de votre propre site web](#howto_provide_twiml_responses).

## <a name="send-an-sms-message"></a><a id="howto_send_sms"></a>Envoyer un message SMS
L’exemple suivant montre comment envoyer un SMS à l’aide de la classe `TwilioRestClient`. Twilio fournit le nombre `from_number` de comptes d’évaluation pour envoyer des messages SMS. Le numéro `to_number` doit être vérifié pour votre compte Twilio avant d’exécuter le code.

```python
# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

from_number = "NNNNNNNNNNN"  # With a trial account, texts can only be sent from your Twilio number.
to_number = "NNNNNNNNNNN"
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Send the SMS message.
message = client.messages.create(to=to_number,
                                    from_=from_number,
                                    body=message)
```

## <a name="provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Envoyer des réponses TwiML à partir de votre propre site web
Lorsque votre application démarre un appel à l'API Twilio, Twilio envoie votre requête à une URL qui est censée renvoyer une réponse TwiML. L'exemple ci-dessus utilise l'URL [https://twimlets.com/message][twimlet_message_url] fournie par Twilio. 

> [!NOTE]
> Même si TwiML est conçu pour être utilisé par Twilio, vous pouvez l’afficher dans votre navigateur. Par exemple, sélectionnez [https://twimlets.com/message][twimlet_message_url] pour afficher un élément `<Response>` vide. En guise d’autre exemple, sélectionnez [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] pour voir un élément `<Response>` qui contient un élément `<Say>`.

Au lieu de compter sur l'URL Twilio, vous pouvez créer votre propre site qui retourne des réponses HTTP. Vous pouvez créer le site dans tout langage qui renvoie des réponses XML. Cet article suppose que vous allez utiliser Python pour créer le TwiML.

Les exemples suivants génèrent en sortie une réponse TwiML prononçant **Hello World** lors de l’appel.

Avec Flask :

```python
from flask import Response
@app.route("/")
def hello():
    xml = '<Response><Say>Hello world.</Say></Response>'
    return Response(xml, mimetype='text/xml')
```

Avec Django :

```python
from django.http import HttpResponse
def hello(request):
    xml = '<Response><Say>Hello world.</Say></Response>'
    return HttpResponse(xml, content_type='text/xml')
```

Comme vous pouvez le constater dans l'exemple ci-dessus, la réponse TwiML est simplement un document XML. La bibliothèque Twilio pour Python contient des classes qui généreront TwiML pour vous. L’exemple ci-dessous produit la réponse équivalente à celle illustrée ci-dessus, mais utilise le module `twiml` dans la bibliothèque Twilio pour Python :

```python
from twilio import twiml

response = twiml.Response()
response.say("Hello world.")
print(str(response))
```

Pour plus d’informations sur TwiML, consultez la [référence TwiML][twiml_reference].

Une fois que votre application Python est configurée pour envoyer des réponses TwiML, utilisez son URL en tant qu’URL transmise à la méthode `client.calls.create`. Par exemple, si une application web intitulée *MyTwiML* est déployée sur un service hébergé Azure, vous pouvez utiliser son URL en tant que webhook, comme illustré dans l’exemple suivant :

```python
from twilio.rest import TwilioRestClient

account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"
from_number = "NNNNNNNNNNN"
to_number = "NNNNNNNNNNN"
url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

# Initialize the Twilio Client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url)
print(call.sid)
```

## <a name="use-additional-twilio-services"></a><a id="AdditionalServices"></a>Utiliser des services Twilio supplémentaires
En plus des exemples présentés ici, Twilio offre des API Web que vous pouvez utiliser pour obtenir d'autres fonctionnalités de Twilio à partir de votre application Azure. Pour plus d'informations, consultez la [documentation de l'API Twilio][twilio_api].

## <a name="next-steps"></a><a id="NextSteps"></a>Étapes suivantes
Maintenant que vous avez appris les bases du service Twilio, consultez ces liens pour en savoir plus :

* [Conseils de sécurité Twilio][twilio_security_guidelines]
* [Procédures et exemples de code Twilio][twilio_howtos]
* [Didacticiels de démarrage rapide Twilio][twilio_quickstarts]
* [Twilio sur GitHub][twilio_on_github]
* [Contacter le support Twilio][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/all
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
[azure_ips]: ./virtual-network/virtual-network-public-ip-address.md
[azure_vm_setup]: ./virtual-machines/linux/quick-create-portal.md
[azure_nsg]: ./virtual-network/manage-network-security-group.md