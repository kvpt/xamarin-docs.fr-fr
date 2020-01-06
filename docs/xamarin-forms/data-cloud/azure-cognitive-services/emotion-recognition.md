---
title: Reconnaissance des émotions perçues à l’aide de l’API Visage
description: L’API visage prend une expression faciale dans une image en tant qu’entrée et retourne des données qui inclut des niveaux de confiance sur un ensemble d’émotions pour chaque visage de l’image. Cet article explique comment utiliser l’API visage de reconnaître l’émotion, pour évaluer une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 4dedcb0869c1e965679812239b1de09f07efa875
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487618"
---
# <a name="perceived-emotion-recognition-using-the-face-api"></a>Reconnaissance des émotions perçues à l’aide de l’API Visage

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

La API Visage peut effectuer la détection d’émotion pour détecter colère, satentant, dégoût, peur, bonheur, neutral, tristesse et surprise, dans une expression faciale basée sur des annotations perçues par des codeurs humains. Il est important de noter, cependant, que les expressions faciales seules ne peuvent pas nécessairement représenter les États internes des personnes.

En plus de retourner un résultat émotion pour une expression faciale, le API Visage peut également retourner un cadre englobant pour les visages détectés.

Reconnaissance des émotions peut être effectuée via une bibliothèque de client et via l’API REST. Cet article se concentre sur l’exécution de la reconnaissance des émotions via l’API REST. Pour plus d’informations sur l’API REST, consultez [API REST de visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

L’API visage peut également servir à reconnaître les expressions faciales des personnes dans la vidéo et peut retourner un résumé de leurs émotions. Pour plus d’informations, consultez [comment analyser des vidéos en temps réel](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/).

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer.

Une clé d’API doit être obtenue pour utiliser le API Visage. Cela peut être obtenue au [essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

Pour plus d’informations sur l’API visage, consultez [API visage](/azure/cognitive-services/face/overview/).

## <a name="authentication"></a>Authentification

Chaque demande adressée à l’API visage nécessite une clé d’API qui doit être spécifiée comme valeur de la `Ocp-Apim-Subscription-Key` en-tête. L’exemple de code suivant montre comment ajouter la clé API pour le `Ocp-Apim-Subscription-Key` en-tête d’une demande :

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

Échec de transmettre une clé API valide à l’API visage entraîne une erreur de 401 réponse.

## <a name="perform-emotion-recognition"></a>Effectuer la reconnaissance des émotions

La reconnaissance d’émotions est effectuée en effectuant une demande POST contenant une image à la `detect` à l’API `https://[location].api.cognitive.microsoft.com/face/v1.0`, où `[location]]` correspond à la région que vous avez utilisé pour obtenir votre clé API. Les paramètres facultatifs de la demande sont :

- `returnFaceId` – s’il faut retourner faceIds des faces détectés. La valeur par défaut est `true`.
- `returnFaceLandmarks` – s’il faut retourner les points de repère face des faces détectés. La valeur par défaut est `false`.
- `returnFaceAttributes` – s’il faut analyser et retourner un ou plusieurs spécifiés doivent faire face les attributs. Les attributs de visage pris en charge incluent `age`, `gender`, `headPose`, `smile`, `facialHair`, `glasses`, `emotion`, `hair`, `makeup`, `occlusion`, `accessories`, `blur`, `exposure`, et `noise`. Notez que face attribut analysis a le coût de calcul et le temps supplémentaire.

Contenu de l’image doit être placé dans le corps de la demande POST en tant qu’une URL ou les données binaires.

> [!NOTE]
> Formats de fichier image pris en charge sont JPEG, PNG, GIF et BMP, et la taille de fichier autorisée est de 1 Ko à 4 Mo.

Dans l’exemple d’application, le processus de reconnaissance d’émotions est appelé en appelant le `DetectAsync` méthode :

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

Cet appel de méthode spécifie le flux contenant les données d’image, qui faceIds doit être retourné, que les points de repère face ne doit pas être retournées, et que l’émotion de l’image doit être analysée. Il spécifie également que les résultats sont retournés sous forme de tableau de `Face` objets. À son tour, le `DetectAsync` méthode appelle le `detect` API REST qui effectue la reconnaissance d’émotions :

```csharp
public async Task<Face[]> DetectAsync(Stream imageStream, bool returnFaceId, bool returnFaceLandmarks, IEnumerable<FaceAttributeType> returnFaceAttributes)
{
  var requestUrl =
    $"{Constants.FaceEndpoint}/detect?returnFaceId={returnFaceId}" +
    "&returnFaceLandmarks={returnFaceLandmarks}" +
    "&returnFaceAttributes={GetAttributeString(returnFaceAttributes)}";
  return await SendRequestAsync<Stream, Face[]>(HttpMethod.Post, requestUrl, imageStream);
}
```

Cette méthode génère un URI de demande et envoie ensuite la demande à la `detect` API via le `SendRequestAsync` (méthode).

> [!NOTE]
> Vous devez utiliser la même région dans vos appels d’API visage en tant que vous avez utilisé pour obtenir vos clés d’abonnement. Par exemple, si vous avez obtenu vos clés d’abonnement à partir de la `westus` région, le point de terminaison de détection de visage sera `https://westus.api.cognitive.microsoft.com/face/v1.0/detect`.

### <a name="send-the-request"></a>Envoyer la demande

Le `SendRequestAsync` méthode rend la requête POST à l’API visage et retourne le résultat comme un `Face` tableau :

```csharp
async Task<TResponse> SendRequestAsync<TRequest, TResponse>(HttpMethod httpMethod, string requestUrl, TRequest requestBody)
{
  var request = new HttpRequestMessage(httpMethod, Constants.FaceEndpoint);
  request.RequestUri = new Uri(requestUrl);
  if (requestBody != null)
  {
    if (requestBody is Stream)
    {
      request.Content = new StreamContent(requestBody as Stream);
      request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
    }
    else
    {
      // If the image is supplied via a URL
      request.Content = new StringContent(JsonConvert.SerializeObject(requestBody, s_settings), Encoding.UTF8, "application/json");
    }
  }

  HttpResponseMessage responseMessage = await _client.SendAsync(request);
  if (responseMessage.IsSuccessStatusCode)
  {
    string responseContent = null;
    if (responseMessage.Content != null)
    {
      responseContent = await responseMessage.Content.ReadAsStringAsync();
    }
    if (!string.IsNullOrWhiteSpace(responseContent))
    {
      return JsonConvert.DeserializeObject<TResponse>(responseContent, s_settings);
    }
    return default(TResponse);
  }
  else
  {
    ...
  }
  return default(TResponse);
}
```

Si l’image est fournie via un flux de données, la méthode génère la requête POST en encapsulant le flux d’image dans un `StreamContent` instance, ce qui fournit du contenu HTTP basé sur un flux. Vous pouvez également, si l’image est fournie via une URL, la méthode s’appuie la demande POST en encapsulant l’URL dans un `StringContent` instance, ce qui fournit du contenu HTTP basé sur une chaîne.

La requête POST est ensuite envoyée à `detect` API. La réponse est lu, désérialisée et retournée à la méthode appelante.

Le `detect` API enverra un code d’état HTTP 200 (OK) dans la réponse, à condition que la demande est valide, ce qui indique que la demande a réussi et que les informations demandées figurent dans la réponse. Pour obtenir la liste des réponses d’erreur possibles, consultez [API REST de visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

### <a name="process-the-response"></a>Traiter la réponse

La réponse de l’API est renvoyée au format JSON. Les données JSON suivantes affiche un message de réponse correcte standard qui fournit les données demandées par l’exemple d’application :

```json
[  
   {  
      "faceId":"8a1a80fe-1027-48cf-a7f0-e61c0f005051",
      "faceRectangle":{  
         "top":192,
         "left":164,
         "width":339,
         "height":339
      },
      "faceAttributes":{  
         "emotion":{  
            "anger":0.0,
            "contempt":0.0,
            "disgust":0.0,
            "fear":0.0,
            "happiness":1.0,
            "neutral":0.0,
            "sadness":0.0,
            "surprise":0.0
         }
      }
   }
]
```

Un message de réponse correcte se compose d’un tableau d’entrées de visage classés selon la taille du rectangle face par ordre décroissant, tandis qu’une réponse vide n’indique aucun visages détectés. Chaque reconnu face inclut une série d’attributs de visage facultatif, qui sont spécifiées par le `returnFaceAttributes` l’argument de la `DetectAsync` (méthode).

Dans l’exemple d’application, la réponse JSON est désérialisée dans un tableau de `Face` objets. Lors de l’interprétation des résultats de l’API visage, l’émotion détectée doit être interprétée comme l’émotion avec le score le plus élevé, comme les scores sont normalisées à la somme est égale à un. Par conséquent, l’exemple d’application affiche l’émotion reconnue avec le score le plus élevé pour la plus grande face détectée dans l’image. Cela est possible avec le code suivant :

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

La capture d’écran suivante montre le résultat du processus de reconnaissance d’émotions dans l’exemple d’application :

![](emotion-recognition-images/emotion-recognition.png "Emotion Recognition")

## <a name="related-links"></a>Liens connexes

- [API visage](/azure/cognitive-services/face/overview/).
- [TODO Cognitive Services (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [REST API visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
