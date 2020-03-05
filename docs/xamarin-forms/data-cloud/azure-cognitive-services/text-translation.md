---
title: Traduction de texte à l’aide de l’API Translator
description: L’API Microsoft Translator peut être utilisé pour la traduction vocale et texte via une API REST. Cet article explique comment utiliser l’API Microsoft Translator Text pour traduire un texte d’une langue à l’autre dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 841b1d4abab5e4c09249174b221da20794771a86
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291991"
---
# <a name="text-translation-using-the-translator-api"></a>Traduction de texte à l’aide de l’API Translator

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_L’API Microsoft Translator peut être utilisée pour traduire la parole et le texte via une API REST. Cet article explique comment utiliser l’API de traduction de texte Translator Text Microsoft pour traduire du texte d’une langue à l’autre dans une application Xamarin. Forms._

## <a name="overview"></a>Vue d’ensemble

L’API Translator comporte deux composants :

- Une API REST pour traduire un texte d’une langue dans le texte d’une autre langue de traduction de texte. L’API détecte automatiquement la langue du texte qui a été envoyé avant la translation.
- Une traduction vocale de l’API REST pour la transcription vocale à partir d’une langue dans le texte d’une autre langue. L’API intègre également des fonctionnalités de synthèse vocale pour prononcer le texte traduit.

Cet article se concentre sur la traduction de texte d’une langue à une autre à l’aide de l’API Translator Text.

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer.

Une clé API doit être obtenue pour utiliser l’API Translator Text. Cela peut être obtenu à [la rubrique Comment s’inscrire à Microsoft API de traduction de texte Translator Text](/azure/cognitive-services/translator/translator-text-how-to-signup/).

Pour plus d’informations sur le API de traduction de texte Translator Text Microsoft, consultez [API de traduction de texte Translator Text documentation](/azure/cognitive-services/translator/).

## <a name="authentication"></a>Authentication

Chaque demande adressée à l’API de traduction de texte Translator Text nécessite un jeton d’accès JSON Web Token (JWT), qui peut être obtenu à partir du service d’appel de jeton cognitive services sur `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Un jeton peut être obtenu en effectuant une demande de publication auprès du service de jeton, en spécifiant un en-tête de `Ocp-Apim-Subscription-Key` qui contient la clé API comme valeur.

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

Le jeton d’accès doit être spécifié dans chaque appel de API de traduction de texte Translator Text comme un en-tête `Authorization` préfixé avec la chaîne `Bearer`, comme indiqué dans l’exemple de code suivant :

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Pour plus d’informations sur le service d’jetons cognitive services, consultez [authentification](/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="performing-text-translation"></a>Effectuer la traduction de texte

La traduction de texte peut être obtenue en effectuant une demande d’accès à l’API `translate` sur `https://api.microsofttranslator.com/v2/http.svc/translate`. Dans l’exemple d’application, la méthode `TranslateTextAsync` appelle le processus de traduction de texte :

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

La méthode `TranslateTextAsync` génère un URI de demande et récupère un jeton d’accès à partir du service de jeton. La demande de traduction de texte est ensuite envoyée à l’API `translate`, qui retourne une réponse XML contenant le résultat. La réponse XML est analysée, et le résultat de la traduction est retourné à la méthode appelante pour l’affichage.

Pour plus d’informations sur les API REST de traduction de texte, consultez [API de traduction de texte Translator Text](/azure/cognitive-services/translator/reference/v3-0-reference).

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

Cette méthode définit le texte à traduire et la langue à traduire le texte. Pour obtenir la liste des langues prises en charge par Microsoft Translator, consultez [langues prises en charge dans le API de traduction de texte Translator Text Microsoft](/azure/cognitive-services/translator/languages/).

> [!NOTE]
> Si une application a besoin de connaître la langue du texte, l’API `Detect` peut être appelée pour détecter la langue de la chaîne de texte.

### <a name="sending-the-request"></a>Envoi de la demande

La méthode `SendRequestAsync` effectue la demande d’extraction à l’API REST de traduction de texte et retourne la réponse :

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

Cette méthode génère la requête d’extraction en ajoutant le jeton d’accès à l’en-tête `Authorization`, préfixé avec la chaîne `Bearer`. La requête d’extraction est ensuite envoyée à l’API `translate`, avec l’URL de demande qui spécifie le texte à traduire et la langue vers laquelle le texte doit être converti. La réponse est ensuite lire et retournée à la méthode appelante.

L’API `translate` enverra le code d’état HTTP 200 (OK) dans la réponse, à condition que la demande soit valide, ce qui indique que la demande a réussi et que les informations demandées se trouvent dans la réponse. Pour obtenir la liste des réponses d’erreur possibles, consultez messages de réponse à la page [obtenir la traduction](/azure/cognitive-services/translator/reference/v3-0-translate).

### <a name="processing-the-response"></a>Traitement de la réponse

La réponse de l’API est renvoyée au format XML. Les données XML suivantes affiche un message de réponse correcte classique :

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

Dans l’exemple d’application, la réponse XML est analysée dans une instance de `XDocument`, avec la valeur racine XML retournée à la méthode d’appel pour l’affichage, comme illustré dans les captures d’écran suivantes :

![](text-translation-images/text-translation.png "Text Translation to German")

## <a name="summary"></a>Résumé

Cet article a expliqué comment utiliser l’API Microsoft Translator Text pour traduire un texte d’une langue dans le texte d’un autre langage dans une application Xamarin.Forms. En plus de traduire le texte, l’API Microsoft Translator peut aussi transcrire vocale à partir d’une langue dans le texte d’une autre langue.

## <a name="related-links"></a>Liens connexes

- [Documentation API de traduction de texte Translator Text](/azure/cognitive-services/translator/)
- [Utilisation d’un service Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Cognitive Services todo (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [API de traduction de texte Translator Text](/azure/cognitive-services/translator/reference/v3-0-reference)
