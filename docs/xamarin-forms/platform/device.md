---
title: Classe de périphérique Xamarin. Forms
description: Cet article explique comment utiliser la classe de périphérique Xamarin. Forms pour contrôler précisément les fonctionnalités et les dispositions sur la base de chaque plateforme.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2020
ms.openlocfilehash: d0f0fa7dd68e8852dd7a72486c155ec064540644
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517069"
---
# <a name="xamarinforms-device-class"></a>Classe de périphérique Xamarin. Forms

[![Télécharger l'](~/media/shared/download.png) exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

La [`Device`](xref:Xamarin.Forms.Device) classe contient un certain nombre de propriétés et de méthodes pour aider les développeurs à personnaliser la disposition et les fonctionnalités en fonction de chaque plateforme.

En plus des méthodes et des propriétés pour cibler le code à des tailles et des types `Device` de matériel spécifiques, la classe comprend des méthodes qui peuvent être utilisées pour interagir avec des contrôles d’interface utilisateur à partir de threads d’arrière-plan. Pour plus d’informations, consultez [interagir avec l’interface utilisateur à partir de threads d’arrière-plan](#interact-with-the-ui-from-background-threads).

## <a name="provide-platform-specific-values"></a>Fournir des valeurs spécifiques à la plateforme

Avant Xamarin. Forms 2.3.4, la plateforme sur laquelle l’application s’exécutait pouvait être obtenue en [`Device.OS`](xref:Xamarin.Forms.Device.OS) examinant la propriété et en [`TargetPlatform.iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)la [`TargetPlatform.Android`](xref:Xamarin.Forms.TargetPlatform.Android)comparant aux valeurs d’énumération,, [`TargetPlatform.WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone)et [`TargetPlatform.Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) . De même, l’une des [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) surcharges peut être utilisée pour fournir des valeurs spécifiques à la plateforme à un contrôle.

Toutefois, étant donné que Xamarin. Forms 2.3.4, ces API ont été dépréciées et remplacées. La [`Device`](xref:Xamarin.Forms.Device) classe contient maintenant des constantes de chaîne publiques qui identifient [`Device.Android`](xref:Xamarin.Forms.Device.Android)les `Device.WinPhone`plateformes – [`Device.iOS`](xref:Xamarin.Forms.Device.iOS),, `Device.WinRT` (déconseillées) [`Device.UWP`](xref:Xamarin.Forms.Device.UWP), ( [`Device.macOS`](xref:Xamarin.Forms.Device.macOS)déconseillées), et. De même, les [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) surcharges ont été remplacées par [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) les [`On`](xref:Xamarin.Forms.On) API et.

En C#, les valeurs spécifiques à la plateforme peuvent être fournies en `switch` créant une instruction [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) sur la propriété, puis `case` en fournissant des instructions pour les plateformes requises :

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

Les [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classes [`On`](xref:Xamarin.Forms.On) et fournissent les mêmes fonctionnalités en XAML :

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

La [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classe est une classe générique qui doit être instanciée avec un `x:TypeArguments` attribut qui correspond au type cible. Dans la [`On`](xref:Xamarin.Forms.On) classe, l' [`Platform`](xref:Xamarin.Forms.On.Platform) attribut peut accepter une valeur `string` unique ou plusieurs valeurs délimitées `string` par des virgules.

> [!IMPORTANT]
> La fourniture d' `Platform` une valeur d’attribut `On` incorrecte dans la classe ne génère pas d’erreur. Au lieu de cela, le code s’exécute sans que la valeur propre à la plateforme soit appliquée.

L' `OnPlatform` extension de balisage peut également être utilisée en XAML pour personnaliser l’apparence de l’interface utilisateur pour chaque plateforme. Pour plus d’informations, consultez [OnPlatform Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

## <a name="deviceidiom"></a>Appareil. idiome

La `Device.Idiom` propriété peut être utilisée pour modifier les dispositions ou les fonctionnalités en fonction de l’appareil sur lequel l’application s’exécute. L' [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom) énumération contient les valeurs suivantes :

- **Téléphone** : iPhone, iPod touch et appareils Android plus étroits que 600 DIP ^
- **Tablette** -iPad, appareils Windows et appareils Android plus larges que 600 DIP ^
- **Ordinateur de bureau** : retournés uniquement dans les [applications UWP](~/xamarin-forms/platform/windows/installation/index.md) sur les `Phone` ordinateurs de bureau Windows 10 (retours sur les appareils mobiles Windows, y compris dans les scénarios continus)
- **TV** -appareils TV Tizen
- **Regarder** : appareils Tizen Watch
- **Non pris en charge** – inutilisé

*^ les DIP ne sont pas nécessairement le nombre de pixels physiques*

La `Idiom` propriété est particulièrement utile pour créer des dispositions qui tirent parti des écrans plus grands, comme suit :

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

La [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) classe fournit les mêmes fonctionnalités en XAML :

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

La [`OnIdiom`](xref:Xamarin.Forms.OnPlatform`1) classe est une classe générique qui doit être instanciée avec un `x:TypeArguments` attribut qui correspond au type cible.

L' `OnIdiom` extension de balisage peut également être utilisée en XAML pour personnaliser l’apparence de l’interface utilisateur en fonction de l’idiome du périphérique sur lequel l’application s’exécute. Pour plus d’informations, consultez [OnIdiom Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom).

## <a name="deviceflowdirection"></a>Device. FlowDirection

La [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) valeur récupère une valeur [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) d’énumération qui représente le sens du déroulement actuel utilisé par l’appareil. La direction de flux est la direction dans laquelle les éléments d’IU sur la page sont analysés par l’œil. Ces valeurs sont les suivantes :

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

En XAML, la [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) valeur peut être récupérée à l’aide `x:Static` de l’extension de balisage :

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

Le code équivalent en C# est le suivant :

```csharp
this.FlowDirection = Device.FlowDirection;
```

Pour plus d’informations sur le sens du déroulement, consultez [localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="devicestyles"></a>Device. styles

La [ `Styles` propriété](~/xamarin-forms/user-interface/styles/index.md) contient des définitions de style intégrées qui peuvent être appliquées à une propriété de certains contrôles ( `Label`telle `Style` que). Les styles disponibles sont les suivants :

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>Appareil. GetNamedSize

`GetNamedSize`peut être utilisé lors de [`FontSize`](~/xamarin-forms/user-interface/text/fonts.md) la définition dans du code C# :

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="devicegetnamedcolor"></a>Appareil. GetNamedColor

Xamarin. Forms 4,6 introduit la prise en charge des couleurs nommées. Une couleur nommée est une couleur qui a une valeur différente selon le mode système (par exemple, clair ou sombre) qui est actif sur l’appareil. Sur Android, les couleurs nommées sont accessibles via la classe [R. Color](https://developer.android.com/reference/android/R.color#constants_2) . Sur iOS, les couleurs nommées sont appelées [couleurs système](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#system-colors). Sur la plateforme Windows universelle, les couleurs nommées sont appelées [ressources de thème XAML](/windows/uwp/design/controls-and-patterns/xaml-theme-resources).

La `GetNamedColor` méthode peut être utilisée pour récupérer des couleurs nommées sur Android, iOS et UWP. La méthode prend un `string` argument et retourne [`Color`](xref:Xamarin.Forms.Color):

```csharp
// Retrieve an Android named color
Color color = Device.GetNamedColor(NamedPlatformColor.HoloBlueBright);
```

`Color.Default`est retourné lorsqu’un nom de couleur est introuvable ou lorsque `GetNamedColor` est appelé sur une plateforme non prise en charge.

> [!NOTE]
> Étant donné `GetNamedColor` que la méthode `Color` retourne un qui est spécifique à une plateforme, elle doit généralement être utilisée conjointement avec [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) la propriété.

La `NamedPlatformColor` classe contient les constantes qui définissent les couleurs nommées pour Android, iOS et UWP :

| Android | iOS | UWP |
| --- | --- | --- |
| `BackgroundDark` | `Label` | `SystemAltHighColor` |
| `BackgroundLight` | `Link` | `SystemAltLowColor` |
| `Black` | `OpaqueSeparator` | `SystemAltMediumColor` |
| `DarkerGray` | `PlaceholderText` | `SystemAltMediumHighColor` |
| `HoloBlueBright` | `QuaternaryLabel` | `SystemAltMediumLowColor` |
| `HoloBlueDark` | `SecondaryLabel` | `SystemBaseHighColor` |
| `HoloBlueLight` | `Separator` | `SystemBaseLowColor` |
| `HoloGreenDark` | `SystemBlue` | `SystemBaseMediumColor` |
| `HoloGreenLight` | `SystemGray` | `SystemBaseMediumHighColor` |
| `HoloOrangeDark` | `SystemGray2` | `SystemBaseMediumLowColor` |
| `HoloOrangeLight` | `SystemGray3` | `SystemChromeAltLowColor` |
| `HoloPurple` | `SystemGray4` | `SystemChromeBlackHighColor` |
| `HoloRedDark` | `SystemGray5` | `SystemChromeBlackLowColor` |
| `HoloRedLight` | `SystemGray6` | `SystemChromeBlackMediumColor` |
| `TabIndicatorText` | `SystemGreen` | `SystemChromeBlackMediumLowColor` |
| `Transparent` | `SystemIndigo` | `SystemChromeDisabledHighColor` |
| `White` | `SystemListLowColor` | `SystemChromeDisabledLowColor` |
| `WidgetEditTextDark` | `SystemListMediumColor` | `SystemChromeHighColor` |
| | `SystemPink` | `SystemChromeLowColor` |
| | `SystemPurple` | `SystemChromeMediumColor` |
| | `SystemRed` | `SystemChromeMediumLowColor` |
| | `SystemTeal` | `SystemChromeWhiteColor` |
| | `SystemYellow` |
| | `TertiaryLabel` |

## <a name="devicestarttimer"></a>Appareil. StartTimer

La `Device` classe a également une `StartTimer` méthode qui fournit un moyen simple de déclencher des tâches dépendantes du temps qui fonctionnent dans Xamarin. Forms Common Code, y compris une bibliothèque .NET standard. Transmettez `TimeSpan` un pour définir l’intervalle et `true` revenez pour que la minuterie `false` continue à s’exécuter ou pour l’arrêter après l’appel en cours.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () =>
{
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Si le code à l’intérieur du minuteur interagit avec l’interface utilisateur (telle que la définition du `Label` texte d’un ou de l’affichage d’une alerte) `BeginInvokeOnMainThread` , il doit être effectué à l’intérieur d’une expression (voir ci-dessous).

> [!NOTE]
> Les `System.Timers.Timer` classes `System.Threading.Timer` et sont .NET standard alternatives à l’utilisation `Device.StartTimer` de la méthode.

## <a name="interact-with-the-ui-from-background-threads"></a>Interagir avec l’interface utilisateur à partir de threads d’arrière-plan

La plupart des systèmes d’exploitation, y compris iOS, Android et le plateforme Windows universelle, utilisent un modèle à thread unique pour le code impliquant l’interface utilisateur. Ce thread est souvent appelé thread *principal* ou thread d' *interface utilisateur*. Une conséquence de ce modèle est que tout le code qui accède aux éléments de l’interface utilisateur doit s’exécuter sur le thread principal de l’application.

Les applications utilisent parfois des threads d’arrière-plan pour exécuter des opérations potentiellement longues, telles que la récupération de données à partir d’un service Web. Si le code qui s’exécute sur un thread d’arrière-plan doit accéder aux éléments de l’interface utilisateur, il doit exécuter ce code sur le thread principal.

La `Device` classe comprend les méthodes `static` suivantes qui peuvent être utilisées pour interagir avec des éléments d’interface utilisateur à partir de threads d’arrière-plan :

| Méthode | Arguments | Retours | Objectif |
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
- [Styles, exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [API de l'appareil](xref:Xamarin.Forms.Device)
