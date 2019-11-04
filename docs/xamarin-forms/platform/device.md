---
title: Classe de périphérique Xamarin. Forms
description: Cet article explique comment utiliser la classe de périphérique Xamarin. Forms pour contrôler précisément les fonctionnalités et les dispositions sur la base de chaque plateforme.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: 25ddbea75d0fd6858f848499281da5d5f0b68171
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697196"
---
# <a name="xamarinforms-device-class"></a>Classe de périphérique Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

La classe [`Device`](xref:Xamarin.Forms.Device) contient un certain nombre de propriétés et de méthodes pour aider les développeurs à personnaliser la disposition et les fonctionnalités en fonction de chaque plateforme.

En plus des méthodes et des propriétés pour cibler le code à des tailles et des types de matériel spécifiques, la classe `Device` comprend des méthodes qui peuvent être utilisées pour interagir avec des contrôles d’interface utilisateur à partir de threads d’arrière-plan. Pour plus d’informations, consultez [interagir avec l’interface utilisateur à partir de threads d’arrière-plan](#interact-with-the-ui-from-background-threads).

## <a name="providing-platform-specific-values"></a>Fournir des valeurs spécifiques à la plateforme

Avant Xamarin. Forms 2.3.4, la plateforme sur laquelle l’application s’exécutait pouvait être obtenue en examinant la propriété [`Device.OS`](xref:Xamarin.Forms.Device.OS) et en la comparant à l’énumération [`TargetPlatform.iOS`](xref:Xamarin.Forms.TargetPlatform.iOS), [`TargetPlatform.Android`](xref:Xamarin.Forms.TargetPlatform.Android), [`TargetPlatform.WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone)et [`TargetPlatform.Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) valeurs. De même, l’une des [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) surcharges peut être utilisée pour fournir des valeurs spécifiques à la plateforme à un contrôle.

Toutefois, étant donné que Xamarin. Forms 2.3.4, ces API ont été dépréciées et remplacées. La classe [`Device`](xref:Xamarin.Forms.Device) contient maintenant des constantes de chaîne publiques qui identifient les plateformes ( [`Device.iOS`](xref:Xamarin.Forms.Device.iOS), [`Device.Android`](xref:Xamarin.Forms.Device.Android), `Device.WinPhone` (obsolète), `Device.WinRT` (déconseillée), [`Device.UWP`](xref:Xamarin.Forms.Device.UWP)et [1](xref:Xamarin.Forms.Device.macOS). De même, les surcharges [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) ont été remplacées par les API [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) et [`On`](xref:Xamarin.Forms.On) .

Dans C#, les valeurs spécifiques à la plateforme peuvent être fournies en créant une instruction `switch` sur la propriété [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) , puis en fournissant des instructions `case` pour les plateformes requises :

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

Les classes [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) et [`On`](xref:Xamarin.Forms.On) fournissent les mêmes fonctionnalités en XAML :

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

La classe [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) est une classe générique qui doit être instanciée avec un attribut `x:TypeArguments` qui correspond au type cible. Dans la classe [`On`](xref:Xamarin.Forms.On) , l’attribut [`Platform`](xref:Xamarin.Forms.On.Platform) peut accepter une valeur de `string` unique ou plusieurs valeurs `string` délimitées par des virgules.

> [!IMPORTANT]
> Si vous fournissez une valeur d’attribut `Platform` incorrecte dans la classe `On`, aucune erreur ne sera générée. Au lieu de cela, le code s’exécute sans que la valeur propre à la plateforme soit appliquée.

Vous pouvez également utiliser l’extension de balisage `OnPlatform` dans XAML pour personnaliser l’apparence de l’interface utilisateur pour chaque plateforme. Pour plus d’informations, consultez [OnPlatform Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

## <a name="deviceidiom"></a>Appareil. idiome

La propriété `Device.Idiom` peut être utilisée pour modifier les dispositions ou les fonctionnalités en fonction de l’appareil sur lequel l’application s’exécute. L’énumération [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom) contient les valeurs suivantes :

- **Téléphone** : iPhone, iPod touch et appareils Android plus étroits que 600 DIP ^
- **Tablette** -iPad, appareils Windows et appareils Android plus larges que 600 DIP ^
- **Ordinateur de bureau** : retournés uniquement dans les [applications UWP](~/xamarin-forms/platform/windows/installation/index.md) sur les ordinateurs de bureau windows 10 (retourne `Phone` sur les appareils mobiles Windows, y compris dans les scénarios continus)
- **TV** -appareils TV Tizen
- **Regarder** : appareils Tizen Watch
- **Non pris en charge** – inutilisé

*^ les DIP ne sont pas nécessairement le nombre de pixels physiques*

La propriété `Idiom` est particulièrement utile pour créer des dispositions qui tirent parti des écrans plus grands, comme ceci :

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

La classe [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) fournit les mêmes fonctionnalités en XAML :

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

La classe [`OnIdiom`](xref:Xamarin.Forms.OnPlatform`1) est une classe générique qui doit être instanciée avec un attribut `x:TypeArguments` qui correspond au type cible.

Vous pouvez également utiliser l’extension de balisage `OnIdiom` dans XAML pour personnaliser l’apparence de l’interface utilisateur en fonction de l’idiome du périphérique sur lequel l’application s’exécute. Pour plus d’informations, consultez [OnIdiom Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom).

## <a name="deviceflowdirection"></a>Device. FlowDirection

La valeur [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) récupère une valeur d’énumération [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) qui représente le sens du déroulement actuel utilisé par l’appareil. La direction de flux est la direction dans laquelle les éléments d’IU sur la page sont analysés par l’œil. Ces valeurs sont les suivantes :

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

En XAML, la valeur [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) peut être récupérée à l’aide de l’extension de balisage `x:Static` :

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

Le code équivalent dans C# est le suivant :

```csharp
this.FlowDirection = Device.FlowDirection;
```

Pour plus d’informations sur le sens du déroulement, consultez [localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="devicestyles"></a>Device. styles

La [propriété `Styles`](~/xamarin-forms/user-interface/styles/index.md) contient des définitions de style intégrées qui peuvent être appliquées à certains contrôles (tels que `Label`) `Style` propriété. Les styles disponibles sont les suivants :

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>Appareil. GetNamedSize

`GetNamedSize` peut être utilisé lors de la définition de [`FontSize`](~/xamarin-forms/user-interface/text/fonts.md) dans le code C# :

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="devicestarttimer"></a>Appareil. StartTimer

La classe `Device` possède également une méthode `StartTimer` qui fournit un moyen simple de déclencher des tâches dépendantes du temps qui fonctionnent dans Xamarin. Forms Common Code, y compris une bibliothèque .NET Standard. Transmettez un `TimeSpan` pour définir l’intervalle et retournez `true` pour que la minuterie continue à s’exécuter ou `false` pour l’arrêter après l’appel en cours.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () =>
{
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Si le code à l’intérieur du minuteur interagit avec l’interface utilisateur (par exemple, en définissant le texte d’un `Label` ou en affichant une alerte), vous devez le faire à l’intérieur d’une expression de `BeginInvokeOnMainThread` (voir ci-dessous).

> [!NOTE]
> Les classes `System.Timers.Timer` et `System.Threading.Timer` sont .NET Standard alternatives à l’utilisation de la méthode `Device.StartTimer`.

## <a name="interact-with-the-ui-from-background-threads"></a>Interagir avec l’interface utilisateur à partir de threads d’arrière-plan

La plupart des systèmes d’exploitation, y compris iOS, Android et le plateforme Windows universelle, utilisent un modèle à thread unique pour le code impliquant l’interface utilisateur. Ce thread est souvent appelé thread *principal* ou thread d' *interface utilisateur*. Une conséquence de ce modèle est que tout le code qui accède aux éléments de l’interface utilisateur doit s’exécuter sur le thread principal de l’application.

Les applications utilisent parfois des threads d’arrière-plan pour exécuter des opérations potentiellement longues, telles que la récupération de données à partir d’un service Web. Si le code qui s’exécute sur un thread d’arrière-plan doit accéder aux éléments de l’interface utilisateur, il doit exécuter ce code sur le thread principal.

La classe `Device` comprend les méthodes de `static` suivantes qui peuvent être utilisées pour interagir avec des éléments d’interface utilisateur à partir de threads d’arrière-plan :

| Méthode | Arguments | Valeur renvoyée | Objectif |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | Appelle un `Action` sur le thread principal et n’attend pas qu’il se termine. |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Appelle un `Func<T>` sur le thread principal, puis attend qu’il se termine. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Appelle un `Action` sur le thread principal, puis attend qu’il se termine. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Appelle un `Func<Task<T>>` sur le thread principal, puis attend qu’il se termine. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Appelle un `Func<Task>` sur le thread principal, puis attend qu’il se termine. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Retourne le `SynchronizationContext` pour le thread principal. |

Le code suivant montre un exemple d’utilisation de la méthode `BeginInvokeOnMainThread` :

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>Liens connexes

- [Exemple d’appareil](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [Styles, exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [Appareil](xref:Xamarin.Forms.Device)
