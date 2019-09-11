---
title: Classe de périphérique de Xamarin.Forms
description: Cet article explique comment utiliser la classe de dispositifs de Xamarin.Forms, de mieux contrôler les mises en page et les fonctionnalités sur une base par plateforme.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: 77cc414cd9b15f99f95d4a54f7af5ce6f028c41a
ms.sourcegitcommit: ab51d32f4ea0e0d4701f0bf2f1465c9323cd070b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70887446"
---
# <a name="xamarinforms-device-class"></a>Classe de périphérique de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

Le [ `Device` ](xref:Xamarin.Forms.Device) classe contient un nombre de propriétés et méthodes pour aider les développeurs de personnaliser la disposition et les fonctionnalités sur une base par plateforme.

En plus des méthodes et des propriétés pour cibler le code à des tailles et des types `Device` de matériel spécifiques, la classe comprend des méthodes qui peuvent être utilisées pour interagir avec des contrôles d’interface utilisateur à partir de threads d’arrière-plan. Pour plus d’informations, consultez [interagir avec l’interface utilisateur à partir de threads d’arrière-plan](#interact-with-the-ui-from-background-threads).

## <a name="providing-platform-specific-values"></a>Fournir des valeurs spécifiques à la plateforme

Avant de Xamarin.Forms 2.3.4, la plateforme de l’application s’exécutait sur pourrait être obtenue en examinant le [ `Device.OS` ](xref:Xamarin.Forms.Device.OS) propriété et en la comparant à la [ `TargetPlatform.iOS` ](xref:Xamarin.Forms.TargetPlatform.iOS), [ `TargetPlatform.Android` ](xref:Xamarin.Forms.TargetPlatform.Android), [ `TargetPlatform.WinPhone` ](xref:Xamarin.Forms.TargetPlatform.WinPhone), et [ `TargetPlatform.Windows` ](xref:Xamarin.Forms.TargetPlatform.Windows) valeurs d’énumération. De même, un de la [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) surcharges peut servir à fournir des valeurs spécifiques à la plateforme à un contrôle.

Toutefois, depuis Xamarin.Forms 2.3.4 ces API est déconseillés et remplacés. Le [ `Device` ](xref:Xamarin.Forms.Device) classe contient maintenant des constantes de chaîne publique qui identifient les plateformes – [ `Device.iOS` ](xref:Xamarin.Forms.Device.iOS), [ `Device.Android` ](xref:Xamarin.Forms.Device.Android), `Device.WinPhone`() déprécié), `Device.WinRT` (déconseillé), [ `Device.UWP` ](xref:Xamarin.Forms.Device.UWP), et [ `Device.macOS` ](xref:Xamarin.Forms.Device.macOS). De même, le [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) surcharges ont été remplacés par le [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) et [ `On` ](xref:Xamarin.Forms.On) API.

En c#, les valeurs spécifiques à la plateforme peuvent être fournis en créant un `switch` instruction sur le [ `Device.RuntimePlatform` ](xref:Xamarin.Forms.Device.RuntimePlatform) propriété et en fournissant `case` instructions pour les plateformes requises :

```csharp
double top;
switch (Device.RuntimePlatform)
{
  case Device.iOS:
    top = 20;
    break;
  case Device.Android:
  case Device.UWP:
  default:
    top = 0;
    break;
}
layout.Margin = new Thickness(5, top, 5, 0);
```

Le [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) et [ `On` ](xref:Xamarin.Forms.On) classes fournissent les mêmes fonctionnalités dans XAML :

```xaml
<StackLayout>
  <StackLayout.Margin>
    <OnPlatform x:TypeArguments="Thickness">
      <On Platform="iOS" Value="0,20,0,0" />
      <On Platform="Android, UWP" Value="0,0,0,0" />
    </OnPlatform>
  </StackLayout.Margin>
  ...
</StackLayout>
```

Le [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) est une classe générique qui doit être instanciée avec une `x:TypeArguments` attribut qui correspond au type de cible. Dans le [ `On` ](xref:Xamarin.Forms.On) (classe), le [ `Platform` ](xref:Xamarin.Forms.On.Platform) attribut peut accepter un seul `string` valeur ou plusieurs virgules `string` valeurs.

> [!IMPORTANT]
> En fournissant un incorrect `Platform` attribut la valeur dans la `On` classe n’entraîne pas une erreur. Au lieu de cela, le code s’exécute sans la valeur spécifique à la plateforme en cours d’application.

Vous pouvez également le `OnPlatform` extension de balisage peut être utilisée dans XAML pour personnaliser l’apparence de l’interface utilisateur sur une base par plateforme. Pour plus d’informations, consultez [Extension de balisage OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

## <a name="deviceidiom"></a>Device.Idiom

Le `Device.Idiom` propriété peut être utilisée pour modifier les dispositions ou fonctionnalités en fonction de l’appareil de l’application sont en cours d’exécution. Le [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom) énumération contient les valeurs suivantes :

- **Téléphone** – iPhone, iPod touch et les appareils Android plus restrictif que les adresses IP 600 dynamiques ^
- **Tablette** : iPad, les appareils Windows et les appareils Android plus larges que les adresses IP 600 dynamiques ^
- **Desktop** : seuls retournées dans [applications UWP](~/xamarin-forms/platform/windows/installation/index.md) sur les ordinateurs de bureau Windows 10 (retourne `Phone` sur les appareils Windows mobiles, notamment dans les scénarios Continuum)
- **TV** : les appareils Tizen TV
- **Espion** – Tizen des appareils espion
- **Non pris en charge** – inutilisées

*^ DIP n’est pas nécessairement le nombre de pixels physiques*

Le `Idiom` propriété est particulièrement utile pour la création de dispositions qui tirent parti des écrans plus grands, comme suit :

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

Le [ `OnIdiom` ](xref:Xamarin.Forms.OnIdiom`1) classe fournit les mêmes fonctionnalités dans XAML :

```xaml
<StackLayout>
    <StackLayout.Margin>
        <OnIdiom x:TypeArguments="Thickness">
            <OnIdiom.Phone>0,20,0,0</OnIdiom.Phone>
            <OnIdiom.Tablet>0,40,0,0</OnIdiom.Tablet>
            <OnIdiom.Desktop>0,60,0,0</OnIdiom.Desktop>
        </OnIdiom>
    </StackLayout.Margin>
    ...
</StackLayout>
```

Le [ `OnIdiom` ](xref:Xamarin.Forms.OnPlatform`1) est une classe générique qui doit être instanciée avec une `x:TypeArguments` attribut qui correspond au type de cible.

Vous pouvez également le `OnIdiom` extension de balisage peut être utilisée dans XAML pour personnaliser l’apparence de l’interface utilisateur en fonction de l’idiome de l’appareil de l’application est en cours d’exécution. Pour plus d’informations, consultez [Extension de balisage OnIdiom](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom).

## <a name="deviceflowdirection"></a>Device.FlowDirection

Le [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) valeur récupère un [ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection) valeur d’énumération qui représente le sens du flux actuel utilisé par l’appareil. Le sens du flux est la direction dans laquelle les éléments d’interface utilisateur sur la page sont analysés par l’œil. Ces valeurs sont les suivantes :

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

Dans XAML, le [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) valeur peut être récupérée à l’aide de la `x:Static` extension de balisage :

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

Le code équivalent en c# est :

```csharp
this.FlowDirection = Device.FlowDirection;
```

Pour plus d’informations sur la direction du flux, consultez [localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="devicestyles"></a>Device.Styles

Le [ `Styles` propriété](~/xamarin-forms/user-interface/styles/index.md) contient les définitions de style prédéfini qui peuvent être appliquées à certains contrôles (tels que `Label`) `Style` propriété. Les styles disponibles sont :

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>Device.GetNamedSize

`GetNamedSize` peut être utilisé lors de la définition [ `FontSize` ](~/xamarin-forms/user-interface/text/fonts.md) en code c# :

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="deviceopenuri"></a>Device.OpenUri

Le `OpenUri` méthode peut être utilisée pour déclencher des opérations sur la plateforme sous-jacente, comme ouvrir une URL dans le navigateur web natifs (**Safari** sur iOS ou **Internet** sur Android).

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

Le [WebView exemple](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs) inclut un exemple d’utilisation `OpenUri` pour ouvrir les URL et déclencher également les appels téléphoniques.

Le [exemple Maps](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs) utilise également `Device.OpenUri` pour afficher les cartes et les itinéraires à l’aide de natif **mappe** applications sur iOS et Android.

## <a name="devicestarttimer"></a>Device.StartTimer

Le `Device` classe a également un `StartTimer` méthode qui fournit un moyen simple de déclencher des tâches de dépendant du temps qui fonctionne dans code commun de Xamarin.Forms, y compris une bibliothèque .NET Standard. Passer un `TimeSpan` pour définir l’intervalle et renvoyer `true` pour conserver le minuteur en cours d’exécution ou `false` pour l’arrêter après l’appel actuel.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Si le code à l’intérieur de la minuterie interagit avec l’interface utilisateur (telles que la définition du texte d’un `Label` ou l’affichage d’une alerte) elle doit être effectuée à l’intérieur d’un `BeginInvokeOnMainThread` expression (voir ci-dessous).

## <a name="interact-with-the-ui-from-background-threads"></a>Interagir avec l’interface utilisateur à partir de threads d’arrière-plan

La plupart des systèmes d’exploitation, y compris iOS, Android et le plateforme Windows universelle, utilisent un modèle à thread unique pour le code impliquant l’interface utilisateur. Ce thread est souvent appelé thread *principal* ou thread d' *interface utilisateur*. Une conséquence de ce modèle est que tout le code qui accède aux éléments de l’interface utilisateur doit s’exécuter sur le thread principal de l’application.

Les applications utilisent parfois des threads d’arrière-plan pour exécuter des opérations potentiellement longues, telles que la récupération de données à partir d’un service Web. Si le code qui s’exécute sur un thread d’arrière-plan doit accéder aux éléments de l’interface utilisateur, il doit exécuter ce code sur le thread principal.

La `Device` classe comprend les méthodes `static` suivantes qui peuvent être utilisées pour interagir avec des éléments d’interface utilisateur à partir de threads d’arrière-plan :

| Méthode | Arguments | Valeur renvoyée | Objectif |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | Appelle un `Action` sur le thread principal et n’attend pas qu’il se termine. |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Appelle un `Func<T>` sur le thread principal, puis attend qu’il se termine. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Appelle un `Action` sur le thread principal, puis attend qu’il se termine. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Appelle un `Func<Task<T>>` sur le thread principal, puis attend qu’il se termine. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Appelle un `Func<Task>` sur le thread principal, puis attend qu’il se termine. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Retourne le `SynchronizationContext` pour le thread principal. |

Le code suivant illustre un exemple d’utilisation de `BeginInvokeOnMainThread` la méthode :

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>Liens connexes

- [Exemple d’appareil](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [Exemple de styles](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [Appareil](xref:Xamarin.Forms.Device)
