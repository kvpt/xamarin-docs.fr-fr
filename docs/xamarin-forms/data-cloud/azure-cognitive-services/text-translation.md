---
title: Traduction de texte à l’aide de l’API Translator
description: L’API Microsoft Translator peut être utilisé pour la traduction vocale et texte via une API REST. Cet article explique comment utiliser l’API Microsoft Translator Text pour traduire un texte d’une langue à l’autre dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 5739246ec7804b58d900ec790f427dab37504b1f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655032"
---
# <a name="text-translation-using-the-translator-api"></a>Traduction de texte à l’aide de l’API Translator

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_L’API Microsoft Translator peut être utilisé pour la traduction vocale et texte via une API REST. Cet article explique comment utiliser l’API Microsoft Translator Text pour traduire un texte d’une langue à l’autre dans une application Xamarin.Forms._

## <a name="overview"></a>Vue d'ensemble

L’API Translator comporte deux composants :

- Une API REST pour traduire un texte d’une langue dans le texte d’une autre langue de traduction de texte. L’API détecte automatiquement la langue du texte qui a été envoyé avant la translation.
- Une traduction vocale de l’API REST pour la transcription vocale à partir d’une langue dans le texte d’une autre langue. L’API s’intègre également des fonctionnalités de synthèse vocale pour énoncer le texte traduit précédent.

Cet article se concentre sur la traduction de texte d’une langue à une autre à l’aide de l’API Translator Text.

Une clé API doit être obtenue pour utiliser l’API Translator Text. Cela peut être obtenue au [comment s’inscrire pour l’API Microsoft Translator Text](/azure/cognitive-services/translator/translator-text-how-to-signup/).

Pour plus d’informations sur l’API Microsoft Translator Text, consultez [Documentation de l’API Translator Text](/azure/cognitive-services/translator/).

## <a name="authentication"></a>Authentification

Chaque demande adressée à l’API Translator Text nécessite un jeton d’accès JSON Web Token (JWT), qui peut être obtenu à partir du service de jeton de cognitive services à `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Un jeton peut être obtenu en effectuant une demande POST vers le service de jeton, en spécifiant un `Ocp-Apim-Subscription-Key` en-tête qui contient la clé d’API en tant que sa valeur.

L’exemple de code suivant montre comment demander un accès à un jeton au service de jeton :

```csharp
public AuthenticationService(string apiKey)
{
    subscriptionKey = apiKey;
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
}
...
async Task<string> FetchTokenAsync(string fetchUri)
{
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";
    var result = await httpClient.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
}
```

Le jeton d’accès retourné, qui est un texte Base64, a un délai d’expiration de 10 minutes. Par conséquent, l’exemple d’application renouvelle le jeton d’accès 9 minutes.

Le jeton d’accès doit être spécifié dans chaque API Translator Text appeler comme un `Authorization` en-tête préfixé avec la chaîne `Bearer`, comme illustré dans l’exemple de code suivant :

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Pour plus d’informations sur le service de jeton de cognitive services, consultez [API de jeton d’authentification](http://docs.microsofttranslator.com/oauth-token.html).

## <a name="performing-text-translation"></a>Effectuer la traduction de texte

Traduction de texte peut être obtenue en effectuant une demande GET à le `translate` à l’API `https://api.microsofttranslator.com/v2/http.svc/translate`. Dans l’exemple d’application, le `TranslateTextAsync` méthode appelle le processus de traduction de texte :

```csharp
public async Task<string> TranslateTextAsync(string text)
{
  ...
  string requestUri = GenerateRequestUri(Constants.TextTranslatorEndpoint, text, "en", "de");
  string accessToken = authenticationService.GetAccessToken();
  var response = await SendRequestAsync(requestUri, accessToken);
  var xml = XDocument.Parse(response);
  return xml.Root.Value;
}
```

Le `TranslateTextAsync` méthode génère un URI de demande et récupère un jeton d’accès au service de jeton. La demande de traduction de texte est ensuite envoyée à la `translate` API, qui retourne une réponse XML qui contient le résultat. La réponse XML est analysée, et le résultat de la traduction est retourné à la méthode appelante pour l’affichage.

Pour plus d’informations sur les API REST de traduction de texte, consultez [API Microsoft Translator Text](http://docs.microsofttranslator.com/text-translate.html).

### <a name="configuring-text-translation"></a>Configuration de la traduction de texte

Le processus de traduction de texte peut être configuré en spécifiant les paramètres de requête HTTP :

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

Cette méthode définit le texte à traduire et la langue à traduire le texte. Pour obtenir la liste des langues prises en charge par Microsoft Translator, consultez [langues prises en charge dans l’API Microsoft Translator Text](/azure/cognitive-services/translator/languages/).

> [!NOTE]
> Si une application a besoin de savoir quelle langue le texte se trouve, le `Detect` API peut être appelée pour détecter la langue de la chaîne de texte.

### <a name="sending-the-request"></a>Envoi de la demande

Le `SendRequestAsync` méthode rend la requête GET à l’API REST de traduction de texte et renvoie la réponse :

```csharp
async Task<string> SendRequestAsync(string url, string bearerToken)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Cette méthode génère la requête GET en ajoutant le jeton d’accès à la `Authorization` en-tête, précédé de la chaîne `Bearer`. La demande GET est ensuite envoyée à la `translate` API, l’URL de requête spécifiant le texte à traduire et avec la langue à traduire le texte. La réponse est ensuite lire et retournée à la méthode appelante.

Le `translate` API enverra un code d’état HTTP 200 (OK) dans la réponse, à condition que la demande est valide, ce qui indique que la demande a réussi et que les informations demandées figurent dans la réponse. Pour obtenir la liste des réponses d’erreur possibles, consultez les Messages de réponse à [obtenir traduire](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate).

### <a name="processing-the-response"></a>Traitement de la réponse

La réponse de l’API est renvoyée au format XML. Les données XML suivantes affiche un message de réponse correcte classique :

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

Dans l’exemple d’application, la réponse XML est analysée dans un `XDocument` instance, avec la valeur de racine XML retournée à la méthode appelante pour l’affichage comme indiqué dans les captures d’écran suivante :

![](text-translation-images/text-translation.png "Traduction de texte en allemand")

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment utiliser l’API Microsoft Translator Text pour traduire un texte d’une langue dans le texte d’un autre langage dans une application Xamarin.Forms. En plus de traduire le texte, l’API Microsoft Translator peut aussi transcrire vocale à partir d’une langue dans le texte d’une autre langue.

## <a name="related-links"></a>Liens associés

- [Documentation des API Text Translator](/azure/cognitive-services/translator/).
- [Utilisation d’un service Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [TODO Cognitive Services (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [API Microsoft Translator Text](http://docs.microsofttranslator.com/text-translate.html).
