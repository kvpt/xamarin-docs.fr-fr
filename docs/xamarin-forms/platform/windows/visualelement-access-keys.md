---
title: Clés d’accès VisualElement sur Windows
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plate-forme Windows qui spécifie une clé d’accès pour un VisualElement.
ms.prod: xamarin
ms.assetid: 771AF785-76B8-4372-89F5-E4D521D21E0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: ce1d682678c17bb24e7a4fd528c84554465c4fa6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656841"
---
# <a name="visualelement-access-keys-on-windows"></a>Clés d’accès VisualElement sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Les touches d’accès rapide sont des raccourcis clavier qui améliorent la convivialité et l’accessibilité des applications sur le plateforme Windows universelle (UWP) en fournissant un moyen intuitif aux utilisateurs de naviguer rapidement et d’interagir avec l’interface utilisateur visible de l’application via un clavier plutôt que via Touch. ou une souris. Ils sont des combinaisons de la touche Alt et une ou plusieurs touches alphanumériques, généralement enfoncés séquentiellement. Raccourcis clavier sont automatiquement pris en charge pour les clés d’accès qui utilisent un seul caractère alphanumérique.

Conseils sur les touches accès sont flottantes badges affichées en regard des contrôles qui intègrent des clés d’accès. Chaque Conseil de clé d’accès contient les clés d’alphanumériques qui activent le contrôle associé. Lorsqu’un utilisateur appuie sur la touche Alt, les conseils de clé d’accès sont affichés.

Ce propre à la plateforme UWP est utilisé pour spécifier une clé d’accès [`VisualElement`](xref:Xamarin.Forms.VisualElement)pour un. Elle est consommée dans XAML en définissant le [ `VisualElement.AccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) propriété attachée à une valeur alphanumérique et en définissant éventuellement le [ `VisualElement.AccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) propriété attachée à une valeur de la [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) énumération, le [ `VisualElement.AccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) propriété jointe un `double`et le [ `VisualElement.AccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) propriété jointe à un `double`:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <ContentPage Title="Page 1"
                 windows:VisualElement.AccessKey="1">
        <StackLayout Margin="20">
            ...
            <Switch windows:VisualElement.AccessKey="A" />
            <Entry Placeholder="Enter text here"
                   windows:VisualElement.AccessKey="B" />
            ...
            <Button Text="Access key F, placement top with offsets"
                    Margin="20"
                    Clicked="OnButtonClicked"
                    windows:VisualElement.AccessKey="F"
                    windows:VisualElement.AccessKeyPlacement="Top"
                    windows:VisualElement.AccessKeyHorizontalOffset="20"
                    windows:VisualElement.AccessKeyVerticalOffset="20" />
            ...
        </StackLayout>
    </ContentPage>
    ...
</TabbedPage>
```

Vous pouvez également, il peut être consommé à partir de C# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var page = new ContentPage { Title = "Page 1" };
page.On<Windows>().SetAccessKey("1");

var switchView = new Switch();
switchView.On<Windows>().SetAccessKey("A");
var entry = new Entry { Placeholder = "Enter text here" };
entry.On<Windows>().SetAccessKey("B");
...

var button4 = new Button { Text = "Access key F, placement top with offsets", Margin = new Thickness(20) };
button4.Clicked += OnButtonClicked;
button4.On<Windows>()
    .SetAccessKey("F")
    .SetAccessKeyPlacement(AccessKeyPlacement.Top)
    .SetAccessKeyHorizontalOffset(20)
    .SetAccessKeyVerticalOffset(20);
...
```

Le `VisualElement.On<Windows>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur la plateforme Windows universelle. Le [ `VisualElement.SetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.String)) (méthode), dans le [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, est utilisé pour définir la valeur de clé d’accès pour le `VisualElement`. Le [ `VisualElement.SetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},Xamarin.Forms.AccessKeyPlacement)) méthode, spécifie éventuellement la position à utiliser pour afficher le Conseil de clé d’accès, avec la [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) énumération fournissant les valeurs possibles suivantes :

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) – Indique que le placement de touche d’accès sera déterminé par le système d’exploitation.
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top) – Indique que le Conseil de clé d’accès s’affiche au-dessus du bord supérieur de la `VisualElement`.
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom) – Indique que le Conseil de clé d’accès apparaît sous le bord inférieur de la `VisualElement`.
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right) – Indique que le Conseil de clé d’accès s’affiche à droite du bord droit de la `VisualElement`.
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left) – Indique que le Conseil de clé d’accès s’affiche à gauche du bord gauche de la `VisualElement`.
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center) – Indique que le Conseil de clé d’accès se superposent sur le centre de la `VisualElement`.

> [!NOTE]
> En règle générale, le [ `Auto` ](xref:Xamarin.Forms.AccessKeyPlacement.Auto) le positionnement de touche est suffisant, qui inclut la prise en charge des interfaces utilisateur adaptative.

Le [ `VisualElement.SetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double)) et [ `VisualElement.SetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double)) méthodes peuvent être utilisées pour un contrôle plus précis de l’emplacement de touche d’accès. L’argument pour le `SetAccessKeyHorizontalOffset` méthode indique comment présent déplacer l’accès aux clés info-bulle à gauche ou droite et l’argument à la `SetAccessKeyVerticalOffset` méthode indique à quelle distance déplacer le Conseil de clé d’accès vers le haut ou vers le bas.

>[!NOTE]
> Décalages de touche d’accès ne peut pas être définis lorsque la valeur de l’emplacement de clé d’accès `Auto`.

En outre, le [ `GetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), [ `GetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), [ `GetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), et [ `GetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) méthodes peuvent être utilisées. pour récupérer une valeur et son emplacement de la clé.

Il en résulte qu’accès touches d’accès peuvent être affichées en regard d’une [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) instances qui définissent les clés, d’accès en appuyant sur la touche Alt :

![VisualElement accès aux clés de spécifique à la plateforme](visualelement-access-keys-images/visualelement-accesskeys.png "VisualElement accès aux clés de spécifique à la plateforme")

Lorsqu’un utilisateur Active une clé d’accès, en appuyant sur la touche Alt suivie de l’accès de clé, l’action par défaut pour le `VisualElement` sera exécuté. Par exemple, lorsqu’un utilisateur Active la clé d’accès sur un [ `Switch` ](xref:Xamarin.Forms.Switch), le `Switch` est activé ou désactivé. Lorsqu’un utilisateur Active la clé d’accès sur un [ `Entry` ](xref:Xamarin.Forms.Entry), le `Entry` Obtient le focus. Lorsqu’un utilisateur Active la clé d’accès sur un [ `Button` ](xref:Xamarin.Forms.Button), le Gestionnaire d’événements pour le [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) événement est exécuté.

Pour plus d’informations sur les clés d’accès, consultez [clés d’accès](/windows/uwp/design/input/access-keys#key-tip-positioning).

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
